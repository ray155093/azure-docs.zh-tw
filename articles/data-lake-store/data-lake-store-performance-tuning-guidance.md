---
title: "Azure Data Lake Store 效能微調指導方針 | Microsoft Docs"
description: "Azure Data Lake Store 效能微調指導方針"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: af11866fc812cd8a375557b7bf9df5cdc9bba610
ms.openlocfilehash: f0d0c05c08ce198e2702c76ad35b348107c664c7
ms.lasthandoff: 01/18/2017


---
# <a name="performance-tuning-guidance-for-azure-data-lake-store"></a>Azure Data Lake Store 的效能微調指導方針

本文提供如何在針對效能 Azure Data Lake Store 進行資料寫入或讀取時取得最佳化效能的指導方針。 本文的目的是協助使用者了解可以針對常用資料上傳/下載工具及資料分析工作負載進行設定的參數。 本指南中的微調是以需使用大量資源的工作負載為目標，此類工作負載需要針對 Data Lake Store 讀取或寫入大量資料。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure 資料湖儲存區帳戶**。 如需有關如何建立帳戶的詳細指示，請參閱 [開始使用 Azure 資料湖儲存區](data-lake-store-get-started-portal.md)
* **Azure HDInsight 叢集** 。 請參閱 [建立具有 Data Lake Store 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確實為叢集啟用遠端桌面。


## <a name="guidelines-for-data-ingestion-tools"></a>資料擷取工具的方針

本節提供改進資料複製或移至 Data Lake Store 期間效能的一般指引。 在本節中，我們將討論限制效能的因素，以及克服那些限制的方式。 下列為幾項需要考量的重點。

* **來源資料** - 來源資料的源頭可能會產生許多限制。 如果來源資料所在位置具有較低的輸送量能力 (例如轉速較慢或遠端的儲存體)，則輸送量可能會成為瓶頸。 SSD (位於本機磁碟最佳) 能透過較高的磁碟輸送量，提供最佳的效能。

* **網路** - 如果您的來源資料位於 VM 上，則 VM 和 Data Lake Store 之間的網路連線將變得非常重要。 使用具有最大 NIC 的 VM 來取得更多網路頻寬。

* **跨區域複製** - 跨區域資料 I/O 本身便具有相當大的網路成本，例如在美國東部 2 的 VM 上執行資料擷取工具，寫入位於美國中部的 Data Lake Store 帳戶。 如果您要跨區域複製資料，效能可能會降低。 我們建議在和目的地 Data Lake Store 帳戶位於相同區域的 VM 上使用資料擷取作業，來最大化網路輸送量。                                                                                                                                        

* **叢集** - 如果您正在透過 HDInsight 叢集 (例如針對 DistCp) 執行資料擷取作業，我們建議針對叢集使用 D 系列 VM，因為它們具有較多的記憶體。 數目較多的核心也能協助提升輸送量。                                                                                                                                                                                                                                                                                                            

* **執行緒的並行** - 如果您正在使用 HDInsight 叢集來從儲存體容器複製資料，根據您叢集大小、容器大小及執行緒設定，可使用的執行緒平行數目將會受到限制。 在 Data Lake Store 上取得更加效能的其中一個重要方式，便是提升並行數目。 您應該調整設定以取得最大的並行數目，以取得更高的輸送量。 下列表格為每個擷取方法的設定，可以設定來取得更多並行數目。 請透過表格中的連結，參閱如何使用工具來將資料內嵌到 Data Lake Store 的文章，以及如何針對工具進行效能微調以取得最大輸送量的文章。

    | 工具               | 並行設定                                                                |
    |--------------------|------------------------------------------------------------------------------------|
    | [Powershell](data-lake-store-get-started-powershell.md)       | PerFileThreadCount、ConcurrentFileCount |
    | [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)    | Azure Data Lake Analytics units         |
    | [DistCp](data-lake-store-copy-data-wasb-distcp.md)            | -m (mapper)                             |
    | [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)| parallelCopies                          |
    | [Sqoop](data-lake-store-data-transfer-sql-sqoop.md)           | fs.azure.block.size、-m (mapper)        |


## <a name="guidelines-while-working-with-hdinsight-workloads"></a>使用 HDInsight 工作負載時的方針

針對 Data Lake Store 中的資料執行分析工作負載時，我們建議您使用 HDInsight 3.5 叢集版本以取得最佳的 Data Lake Store 效能。 當您的作業較為 I/O 密集時，部分參數可以針對效能因素進行設定。 例如，如果作業主要包含讀取和寫入，則為 I/O 提升針對 Azure Data Lake Store 的並行將會提升效能。

Azure Data Lake Store 在有更多並行數目的情況下將能取得最佳化的效能。 有幾種基本的方法可以提升 I/O 密集作業的並行。

1. **執行大量的計算 YARN 容器，而不要執行數目較少的大型 YARN 容器** – 擁有較多的容器將會提升輸入和輸出作業的並行，並進一步運用 Data Lake Store 的能力。

    例如，假設您在 HDInsight 叢集中具有 2 個 D3v2 節點。 每個 D3v2 節點都具有 12GB 的 YARN 記憶體，因此 2 部 D3v2 機器將會有 24GB 的 YARN 記憶體。 我們再假設您已將 YARN 容器大小設為 6GB。 這代表您會有 4 個具有 6GB 的容器。 因此，您可以平行執行 4 個並行工作。 若要提升並行數目，您可以將容器的大小減少至 3GB，這會讓您有 8 個具有 3GB 的容器。 這能讓您平行執行 8 個並行工作。 下列為圖例。

    ![Data Lake Store 效能](./media/data-lake-store-performance-tuning-guidance/image-1.png)

    一個常見的問題，是為何不將容器大小減少至 1GB 的記憶體，以便取得 24 個容器並進一步提升並行數目。 這取決於工作是否需要 3GB 的記憶體，或是 1GB 便已足夠。  您在容器中執行的可能是僅需要 1GB 的簡單作業，或是需要 3GB 記憶體的複雜作業。  如果您將容器的大小減少太多，可能會出現記憶體不足的例外狀況。  當發生此狀況時，您應該提升容器的記憶體。  除了記憶體之外，虛擬核心數目也會影響平行處理。

    ![Data Lake Store 效能](./media/data-lake-store-performance-tuning-guidance/image-2.png)

2. **提升叢集中的記憶體以取得更多並行數目** – 您可以透過提升叢集大小，或是選擇具有更多記憶體的 VM 類型，來提升叢集中的記憶體。 這將會提升可用的 YARN 記憶體，讓您可以建立更多容器，並進一步提升並行數目。  

    例如，假設您的 HDInsight 叢集中具有單一 D3v2 節點，該節點具有 12GB 的 YARN 記憶體和 3GB 的容器。  您將叢集調整為 2 個 D3v2，這將會把 YARN 記憶體提升至 24GB。  這會把並行數目從 4 提升到 8。

    ![Data Lake Store 效能](./media/data-lake-store-performance-tuning-guidance/image-3.png)

3. **從把工作數目設定為您目前的並行數目開始** – 現在，您已經正確設定容器大小以取得最大的並行數目。 您接著應該設定工作數目以使用所有容器。 每個工作負載中都有不同的工作名稱。

    您也可能要考慮作業大小。 如果作業的大小較大，每個工作可能需要處理較大量的資料。 您可以考慮使用更多工作，讓每個工作不需要處理太多資料。

    例如，假設您有 6 個容器。 我們在一開始應該將工作數目設定為 6。 您可以嘗試使用較高的工作數目，來看看效能是否會提升。 設定較高的工作數目將不會提升並行數目。 如果我們將工作數目設定為大於 6，該工作在下一波之前將不會執行。 如果我們將工作數目設定為小於 6，系統將不會完全運用並行。

    每個工作負載都具有不同的可用參數，可用來設定工作數目。 下列表格將列出部分參數。

    | 工作負載               | 設定工作的參數                                                         |
    |--------------------|------------------------------------------------------------------------------------|
    | [HDInsight 上的 Spark](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
    | [Hive on HDInsight](data-lake-store-performance-tuning-hive.md)    | hive.tez.container.size         |
    | [MapReduce on HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
    | [Storm on HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>背景工作處理序數目</li><li>Spout 執行程式執行個體數目</li><li>Bolt 執行程式執行個體數目 </li><li>Spout 工作數目</li><li>Bolt 工作數目</li></ul>|

## <a name="see-also"></a>另請參閱
* [Azure 資料湖儲存區概觀](data-lake-store-overview.md)
* [開始使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

