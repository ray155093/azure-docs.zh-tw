---
title: "使用 Distcp 將送至/來自 WASB 的資料複製到 Data Lake Store | Microsoft Docs"
description: "使用 Distcp 工具將送至/來自 Azure 儲存體 Blob 的資料複製到資料湖存放區"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f1c8c5b9bfa14b817efb635cf812242afaa70e35
ms.openlocfilehash: d0475ff29da03d2c4a12e72e458175d03ce608fd


---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>使用 Distcp 在 Azure 儲存體 Blob 與資料湖存放區之間複製資料
> [!div class="op_single_selector"]
> * [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

在您建立可存取 Data Lake Store 帳戶的 HDInsight 叢集後，您可以使用 Distcp 之類的 Hadoop 生態系統工具，將 **送至/來自** HDInsight 叢集儲存體 (WASB) 的資料複製到 Data Lake Store 帳戶中。 本文提供執行此作業的相關指示。

## <a name="prerequisites"></a>必要條件
開始閱讀本文之前，您必須符合下列必要條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure 資料湖儲存區帳戶**。 如需有關如何建立帳戶的詳細指示，請參閱 [開始使用 Azure 資料湖儲存區](data-lake-store-get-started-portal.md)
* **Azure HDInsight 叢集** 。 請參閱 [建立具有 Data Lake Store 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確實為叢集啟用遠端桌面。

## <a name="do-you-learn-fast-with-videos"></a>使用影片快速學習？
[觀看這部影片](https://mix.office.com/watch/1liuojvdx6sie) ，主題是關於如何使用 DistCp 在 Azure 儲存體 Blob 與 Data Lake Store 之間複製資料。

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>從遠端桌面 (Windows 叢集) 或 SSH (Linux 叢集) 使用 Distcp
HDInsight 叢集隨附 Distcp 公用程式，可用來將不同來源的資料複製到 HDInsight 叢集。 如果您已將 HDInsight 叢集設定為使用資料湖存放區做為額外的儲存體，則您也可以使用現成可用的 Distcp 公用程式將資料複製到資料湖存放區帳戶，或從中複製資料。 在本節中，我們將討論如何使用 Distcp 公用程式。

1. 如果您有 Windows 叢集，請從遠端連接到可存取資料湖存放區帳戶的 HDInsight 叢集。 如需指示，請參閱 [使用 RDP 連接到叢集](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。 從叢集桌面開啟 Hadoop 命令列。

    如果您有 Linux 叢集，請使用 SSH 連接到叢集。 請參閱 [連線至以 Linux 為基礎的 HDInsight 叢集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect)。 從 SSH 提示字元執行命令。
2. 確認您是否可存取 Azure 儲存體 Blob (WASB)。 執行以下命令：

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    這應會提供儲存體 blob 中的內容清單。
3. 同樣地，請確認您是否可從叢集存取資料湖存放區帳戶。 執行以下命令：

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    這應會提供資料湖存放區帳戶中的檔案/資料夾清單。
4. 使用 Distcp 將資料從 WASB 複製到資料湖存放區帳戶。

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    這會將 WASB 中的 **/example/data/gutenberg/** 資料夾的內容複製到 Data Lake Store 帳戶中的 **/myfolder**。
5. 同樣地，請使用 Distcp 將資料從資料湖存放區帳戶複製到 WASB。

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    這會將 Data Lake Store 帳戶中的 **/myfolder** 的內容複製到 WASB 中的 **/example/data/gutenberg/** 資料夾。

## <a name="performance-considerations-while-using-distcp"></a>使用 DistCp 時的效能考量

因為 DistCp 以單一檔案為最低細微程度，若要針對 Data Lake Store 而達到最佳化，設定同步複本數目上限是最重要的參數。 這是在命令列設定對應程式數目 (‘m’) 參數來控制。 這個參數指定用來複製資料的對應程式數目上限。 預設值為 20。

**範例**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>如何決定要使用的對應程式數目？

以下是一些您可以使用的指引。

* **步驟 1︰判斷 YARN 記憶體總計** - 第一個步驟是判斷您執行 DistCp 作業的叢集可用的 YARN 記憶體。 您可以在與叢集相關聯的 Ambari 入口網站中取得這項資訊。 瀏覽至 YARN，檢視 [設定] 索引標籤以查看 YARN 記憶體。 若要計算 YARN 記憶體總計，請將每個節點的 YARN 記憶體乘以您在叢集中的節點數目。

* **步驟 2︰計算對應程式數目** - **m** 的值等於 YARN 記憶體總計除以 YARN 容器大小的商數。 Ambari 入口網站中也提供 YARN 容器大小的資訊。 瀏覽至 YARN，檢視 [設定] 索引標籤。 YARN 容器大小會顯示在此視窗中。 計算對應程式數目 (**m**) 的方程式是

        m = (number of nodes * YARN memory for each node) / YARN container size

**範例**

假設您在叢集中有 4 個 D14v2s 節點，且嘗試從 10 個不同的資料夾傳輸 10TB 的資料。 每個資料夾包含不同的資料量，且每個資料夾內的檔案大小都不同。

* YARN 記憶體總計 - 從 Ambari 入口網站，您可以判斷 D14 節點的 YARN 記憶體是 96GB。 因此，4 節點叢集的 YARN 記憶體總計為︰ 

        YARN memory = 4 * 96GB = 384GB

* 對應程式數目 - 從 Ambari 入口網站，您可以判斷 D14 叢集節點的 YARN 容器大小是 3072。 因此，對應程式數目為︰

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

如果有其他應用程式在使用記憶體，您可以選擇讓 DistCp 只使用叢集的一部分 YARN 記憶體。

### <a name="copying-large-datasets"></a>複製大型資料集

如果要移動的資料集很大 (例如 > 1TB) 或您有許多不同的資料夾，請考慮使用多個 DistCp 作業。 雖然可能不會提高效能，但可分散作業，如果有任何作業失敗，您只需要重新啟動該特定的作業，而不是整個作業。

### <a name="limitations"></a>限制

* DistCp 會嘗試建立較小的對應程式，以最佳化效能。 增加對應程式數目不見得會提高效能。

* DistCp 受限於每個檔案只有一個對應程式。 因此，對應程式數目不應該超過您擁有的檔案數目。 因為 DistCp 只能將一個對應程式指派給一個檔案，這會限制可用於複製大量檔案的並行程度。

* 如果您的大型檔案很少，則應該將它們分割成 256MB 的檔案區塊，以提高並行潛力。 
 
* 如果您從 Azure Blob 儲存體帳戶複製，Blob 儲存體端可能會節流複製作業。 這會降低複製作業的效能。 若要深入了解 Azure Blob 儲存體的限制，請參閱 [Azure 訂用帳戶和服務限制](../azure-subscription-service-limits.md)中的 Azure 儲存體限制。

## <a name="see-also"></a>另請參閱
* [將資料從 Azure 儲存體 Blob 複製到資料湖存放區](data-lake-store-copy-data-azure-storage-blob.md)
* [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Store 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Dec16_HO1-->


