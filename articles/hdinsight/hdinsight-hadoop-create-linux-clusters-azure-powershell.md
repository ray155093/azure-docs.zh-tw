<properties
   	pageTitle="在 Linux 上使用 Azure PowerShell 在 HDInsight 中建立 Hadoop、HBase、Storm 或 Spark 叢集 | Microsoft Azure"
   	description="了解如何在 Linux 上使用 Azure PowerShell 為 HDInsight 建立 Hadoop、HBase、Storm 或 Spark 叢集。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="07/08/2016"
   	ms.author="nitinme"/>

# 使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-selector-create-clusters.md)]

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Microsoft Azure 中控制和自動化工作量的部署與管理。本文件提供如何使用 Azure PowerShell 建立以 Linux 為基礎之 HDInsight 叢集的相關資訊。其中也包含一個範例指令碼。

> [AZURE.NOTE] Azure PowerShell 僅適用於 Windows 用戶端。如果您使用 Linux、Unix 或 Mac OS X 用戶端，請參閱 [使用 Azure CLI 建立以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-create-linux-clusters-azure-cli.md)，以取得使用 Azure CLI 建立叢集的相關資訊。

## 必要條件
開始進行此程序之前，您必須先具備下列項目：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- Azure PowerShell。如需搭配使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱[使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。如需 HDInsight Windows PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/dn858087.aspx)。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## 建立叢集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

若要使用 Azure PowerShell 建立 HDInsight 叢集，您必須完成下列程序：

- 建立 Azure 資源群組
- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集

建立 Linux 叢集時所必須設定的兩個最重要的參數，是用來指定 OS 類型和 SSH 使用者詳細資料的參數：

- 請務必將 **-OSType** 參數指定為 **Linux**。
- 若要對叢集上的遠端工作階段使用 SSH，您可以指定 SSH 使用者密碼或 SSH 公開金鑰。如果您同時指定 SSH 使用者密碼或 SSH 公開金鑰，系統會忽略公開金鑰。如果您想要對遠端工作階段使用 SSH 金鑰，您必須在出現密碼提示時指定空白的 SSH 密碼。如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

    * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

下列指令碼示範如何建立新叢集：

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

您對 **$clusterCredentials** 指定的值會用來建立叢集的 Hadoop 使用者帳戶。使用此帳戶來連線到叢集。

您對 **$sshCredentials** 指定的值會用來建立叢集的 SSH 使用者。使用此帳戶在叢集上啟動遠端 SSH 工作階段並執行工作。

> [AZURE.IMPORTANT] 在這個指令碼中，您必須指定將位於叢集中的背景工作節點數目。如果您規劃使用 32 個以上的背景工作角色節點 (在建立叢集時或在建立後調整叢集時)，則您也必須指定具有至少 8 個核心和 14 GB RAM 的前端節點大小。
>
> 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

建立叢集可能需要花費 20 分鐘的時間。

以下範例示範如何新增其他儲存體帳戶︰

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## 自訂叢集

- 請參閱[使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)。
- 請參閱[使用指令碼動作自訂 Windows 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)。

## 刪除叢集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## 後續步驟

既然您已成功建立 HDInsight 叢集，請使用下列資源來了解如何使用您的叢集。

### Hadoop 叢集

* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

### HBase 叢集

* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-started-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux.md)

### Storm 叢集

* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python-topology.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

### Spark 叢集

* [使用 Scala 來建立獨立的應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行工作](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark 和 BI：搭配 BI 工具來使用 HDInsight 中的 Spark 以執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和機器學習：使用 HDInsight 中的 Spark 來預測食物檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0914_2016-->