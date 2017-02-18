---
title: "使用命令列建立 Azure HDInsight (Hadoop) | Microsoft Docs"
description: "了解如何使用跨平台 Azure CLI、Azure Resource Manager 範本以及 Azure REST API 來建立 HDInsight 叢集。 您可以指定叢集類型 (Hadoop、HBase、或 Storm) 或使用指令碼來安裝自訂元件。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 777168c5d48cc589c54a12265bd54e87c4b64274


---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>使用 Azure CLI 建立 HDInsight 叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure CLI 是可讓您管理 Azure 服務的跨平台命令列公用工具。 它可搭配 Azure Resource Manager 範本用來建立 HDInsight 叢集，以及相關聯的儲存體帳戶和其他服務。

Azure Resource Manager 範本是描述**資源群組**與其中所有資源 (例如 HDInsight) 的 JSON 文件。此範本型方法可讓您在一個範本中定義 HDInsight 所需的所有資源。 它也可讓您透過 **部署**來整體管理群組的變更，以將變更套用至整個群組。

本文件中的步驟將逐步完成使用 Azure CLI 和範本建立新 HDInsight 叢集的程序。

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。


## <a name="prerequisites"></a>必要條件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **Azure CLI**。 這份文件中的步驟最近一次是以 Azure CLI 版本 0.10.1 來測試。
  
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>存取控制需求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="log-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶

依照 [從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶](../xplat-cli-connect.md) 中記載的步驟，使用 **login** 方法連線到您的訂用帳戶。

## <a name="create-a-cluster"></a>建立叢集

在安裝和設定 Azure CLI 之後，應該從命令提示字元、殼層或終端機工作階段執行下列步驟。

1. 使用下列命令來驗證您的 Azure 訂用帳戶：
   
        azure login
   
    系統會提示您提供使用者名稱與密碼。 如果您有多個 Azure 訂用帳戶，則可以使用 `azure account set <subscriptionname>` 來設定 Azure CLI 命令所使用的訂用帳戶。
2. 使用下列命令來切換至 Azure 資源管理員模式︰
   
        azure config mode arm
3. 建立資源群組。 此資源群組會包含 HDInsight 叢集和關聯的儲存體帳戶。
   
        azure group create groupname location
   
   * 以群組的唯一名稱取代 **groupname** 。 
   * 以您想要在其中建立群組的地理區域取代 **location** 。 
     
       如需有效位置的清單，請使用 `azure location list` 命令，然後使用 [名稱] 欄中的其中一個位置。
4. 建立儲存體帳戶。 此儲存體帳戶會用來做為 HDInsight 叢集的預設儲存體。
   
        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
   
   * 以上一個步驟中建立的群組名稱取代 **groupname** 。
   * 以與上一個步驟中使用的相同位置取代 **location** 。 
   * 以儲存體帳戶的唯一名稱取代 **storagename** 。
     
     > [!NOTE]
     > 如需有關此命令中所使用參數的詳細資訊，請使用 `azure storage account create -h` 來檢視此命令的說明。
     > 
     > 
5. 擷取用來存取儲存體帳戶的金鑰。
   
        azure storage account keys list -g groupname storagename
   
   * 以資源群組名稱取代 **groupname** 。
   * 以儲存體帳戶的名稱取代 **storagename** 。
     
     在傳回的資料中，儲存 **key1** 的**金鑰**值。
6. 建立 HDInsight 叢集。
   
        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername
   
   * 以資源群組名稱取代 **groupname** 。
   * 將 **Hadoop** 取代為您想要建立的叢集類型。 例如，`Hadoop`、`HBase`、`Storm` 或 `Spark`。
     
     > [!IMPORTANT]
     > HDInsight 叢集有各種不同類型，這些類型各自對應到叢集微調時所針對的工作負載或技術。 沒有任何支援方法可建立結合多個類型的叢集，例如在一個叢集上並存 Storm 和 HBase。 
     > 
     > 
   * 以與先前步驟中使用的相同位置取代 **location** 。
   * 以儲存體帳戶名稱取代 **storagename** 。
   * 以在上一個步驟中取得的金鑰取代 **storagekey** 。 
   * 針對 `--defaultStorageContainer` 參數，使用與您用於叢集的相同名稱。
   * 以當您透過 HTTPS 存取叢集時所要使用的名稱和密碼取代 **admin** 和 **httppassword**。
   * 以當您透過 SSH 存取叢集時所要使用的使用者名稱和密碼取代 **sshuser** 和 **sshuserpassword**
   
   > [!IMPORTANT]
   > 上述範例會使用 2 個背景工作節點建立叢集。 如果您 (在建立叢集期間或藉由調整叢集) 規劃 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB ram 的前端節點大小。 您可以使用 `--headNodeSize` 參數來設定前端節點大小。
   > 
   > 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。
     
     可能需要數分鐘的時間，才能完成叢集建立程序。 通常大約 15 分鐘。

## <a name="next-steps"></a>後續步驟
既然您已使用 Azure CLI 順利建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集：

### <a name="hadoop-clusters"></a>Hadoop 叢集
* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase 叢集
* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-started-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 叢集
* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python-topology.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)




<!--HONumber=Jan17_HO4-->


