---
title: "將其他 Azure 儲存體帳戶新增至 HDInsight | Microsoft Docs"
description: "了解如何將其他 Azure 儲存體帳戶新增至現有的 HDInsight 叢集。"
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/23/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d391c5c6289aa63e969f63f189eb5db680883f0a
ms.openlocfilehash: b8c5e53ed5fe86ed099e37644d405080477f8c27
ms.lasthandoff: 03/01/2017

---

# <a name="add-additional-storage-accounts-to-hdinsight"></a>將其他儲存體帳戶新增至 HDInsight

了解如何使用指令碼動作，將其他 Azure 儲存體帳戶新增至使用 Linux 當成作業系統的現有 HDInsight 叢集。

> [!IMPORTANT]
> 本文件中的資訊是有關如何在建立叢集之後，將其他儲存體新增至叢集。 如需在叢集建立期間新增其他儲存體帳戶的相關資訊，請參閱[建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#use-additional-storage)文件中的__使用其他儲存體__一節。

## <a name="how-it-works"></a>運作方式

此指令碼採用下列參數︰

* __Azure 儲存體帳戶名稱__：要新增至 HDInsight 叢集的儲存體帳戶名稱。 執行指令碼之後，HDInsight 可以讀取和寫入此儲存體帳戶中儲存的資料。

* __Azure 儲存體帳戶金鑰__︰授與存取權給儲存體帳戶的金鑰。

* __-p__ (選用)︰若已指定，金鑰不會加密，而且會以純文字方式儲存在 core-site.xml 檔案。

在處理期間，此指令碼會執行下列動作：

* 如果儲存體帳戶已經存在於叢集的 core-site.xml 組態中，則指令碼會結束，不會執行任何進一步的動作。

* 確認儲存體帳戶存在並可使用金鑰來存取。

* 使用叢集認證加密金鑰。

* 將儲存體帳戶新增至 core-site.xml 檔案。

* 停止後重新啟動 Oozie、YARN、MapReduce2 和 HDFS 服務，以便它們挑選新的儲存體帳戶資訊。

> [!WARNING]
> 如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會遇到效能不佳。 存取不同區域中的資料會傳送 Azure 資料中心外部和跨越公用網際網路的網路流量，這可能會造成延遲。 此外，傳送區域資料中心外部的資料可能會比較昂貴，因為當資料離開資料中心時適用輸出費用。

## <a name="the-script"></a>指令碼

__指令碼位置__：[https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__需求__：

* 指令碼必須套用於__前端節點__。

## <a name="to-use-the-script"></a>若要使用指令碼

如需透過 Azure 入口網站、Azure PowerShell 和 Azure CLI 使用指令碼動作的資訊，請參閱[使用指令碼動作來自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)文件中的「將指令碼動作套用到執行中的叢集」一節。

使用自訂文件中提供的資訊時，請以這個指令碼的 URI 取代範例指令碼動作 URI (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)。 以即將新增至叢集的儲存體帳戶的 Azure 儲存體帳戶名稱和金鑰取代範例參數。

> [!NOTE]
> 您不需要將此指令碼標示為 [已保存]，因為它會直接更新叢集的 Ambari 組態。

## <a name="known-issues"></a>已知問題

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>儲存體帳戶未顯示在 Azure 入口網站或工具中

在 Azure 入口網站中檢視 HDInsight 叢集時，選取 [屬性] 之下的 [儲存體帳戶] 項目，並不會顯示透過此指令碼動作新增的儲存體帳戶。 Azure PowerShell 和 Azure CLI 也不會顯示其他儲存體帳戶。

沒有顯示儲存體資訊是因為指令碼只修改叢集的 core-site.xml 組態。 使用 Azure 管理 API 擷取叢集資訊時，不會使用這項資訊。

若要檢視使用此指令碼新增至叢集的儲存體帳戶資訊，請使用 Ambari REST API。 下列命令示範如何使用 [cURL (http://curl.haxx.se/)](http://curl.haxx.se/) 和 [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) 從 Ambari 擷取及剖析 JSON 資料：

> [!div class="tabbedCodeSnippets" data-resources="OutlookServices.Calendar"]
> ```PowerShell
> curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["""fs.azure.account.key.STORAGEACCOUNT.blob.core.windows.net"""] | select(. != null)'
> ```
> ```Bash
> curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.STORAGEACCOUNT.blob.core.windows.net"] | select(. != null)'
> ```

使用此命令時，將 __CLUSTERNAME__ 替換為 HDInsight 叢集的名稱。 將 __PASSWORD__ 替換為叢集的 HTTP 登入密碼。 將 __STORAGEACCOUNT__ 替換為使用指令碼動作新增的儲存體帳戶名稱。 此命令傳回的資訊看起來類似下列文字：

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

此文字是已加密的金鑰範例，可用來存取儲存體帳戶。

### <a name="unable-to-access-storage-after-changing-key"></a>無法在變更金鑰之後存取儲存體

如果您變更儲存體帳戶的金鑰，HDInsight 就無法再存取儲存體帳戶。 HDInsight 在叢集的 core-site.xml 中使用金鑰的快取複本。 此快取副本必須更新以符合新的金鑰。

再次執行指令碼動作並__不會__更新金鑰，因為指令碼會查看儲存體帳戶的項目是否已經存在。 如果項目已經存在，就不會進行任何變更。

若要解決這個問題，您必須移除儲存體帳戶的現有項目。 執行下列步驟以移除現有項目：

1. 在 Web 瀏覽器中，對您的 HDInsight 叢集開啟 Ambari Web UI。 URI 是 https://CLUSTERNAME.azurehdinsight.net。 將 __CLUSTERNAME__ 取代為您叢集的名稱。

    出現提示時，輸入您叢集的 HTTP 登入使用者和密碼。

2. 從頁面左邊的服務清單中，選取 [HDFS]。 然後選取頁面中間的 [設定] 索引標籤。

3. 在 [篩選...] 欄位中，輸入 __fs.azure.account__ 值。 這會傳回已新增到叢集的任何其他儲存體帳戶項目。 有兩種項目類型：__keyprovider__ 和 __key__。 兩者都會包含儲存體帳戶名稱做為金鑰名稱的一部分。 

    下列是名為 __mystorage__ 之儲存體帳戶的項目範例：

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. 在您識別您需要移除之儲存體帳戶的金鑰之後，請使用項目右邊的紅色 '-' 圖示將它刪除。 然後使用 [儲存] 按鈕來儲存您的變更。

5. 在儲存變更之後，使用指令碼動作將儲存體帳戶和新的金鑰值新增至叢集。

### <a name="poor-performance"></a>效能不佳

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會遇到效能不佳。 存取不同區域中的資料會傳送 Azure 資料中心外部和跨越公用網際網路的網路流量，這可能會造成延遲。

### <a name="additional-charges"></a>額外費用

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會發現 Azure 帳單上出現額外輸出費用。 當資料離開區域資料中心時適用輸出費用，即使流量的目的地是不同區域中的其他 Azure 資料中心亦然。

## <a name="next-steps"></a>後續步驟

您已了解如何將其他儲存體帳戶新增至現有的 HDInsight 叢集。 如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)
