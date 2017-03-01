---
title: "Azure HDInsight 上的 R 伺服器簡介 | Microsoft Docs"
description: "取得 HDInsight 上的 R 伺服器簡介。 了解如何使用 R 伺服器來建立適用於巨量資料分析的應用程式。"
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: cd57f3a43142b3af3546eafd9749123fadd333c2
ms.openlocfilehash: 2f5cf1155f116b22fa28cf86bb8c87667df981b7
ms.lasthandoff: 01/14/2017


---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>HDInsight 上的 R 伺服器與開放原始碼 R 功能簡介

有了 Microsoft Azure HDInsight 之後，在 Azure 中建立 HDInsight 叢集時，即可選擇使用 Microsoft R 伺服器。 這項新功能可讓資料科學家、統計學家以及 R 程式設計人員隨其所需存取 HDInsight 上可調整大小的分散式分析方法。

您可依據手邊的專案和工作調整叢集大小，並於不再需要時加以終止。 因為這些叢集屬於 Azure HDInsight 的一部分，所以具備企業級全年無休支援、99.9% 執行時間的 SLA，同時具備與 Azure 生態系統中其他元件整合的彈性。

HDInsight 中的 R 伺服器所提供的最新功能，適用於幾乎任何大小的資料集上進行之以 R 為基礎的分析，且不論資料集是載入 Azure Blob 或 Data Lake 儲存體。 因為 R 伺服器的建置基礎為開放原始碼 R，因此您所建置的 R 應用程式可以任意運用 8000 多種開放原始碼 R 封裝，以及 ScaleR 中的常式與 R 伺服器隨附的 Microsoft 巨量資料分析封裝。

叢集的邊緣節點提供便利的地方，以便連接到叢集以及執行 R 指令碼。 有了邊緣節點之後，即可選擇跨邊緣節點伺服器的核心，執行 ScaleR 的平行分散式函數。 也可以選擇透過使用 ScaleR 的 Hadoop Map Reduce 或 Spark 計算內容，跨叢集的節點執行這些函數。

可以下載分析所產生的模型或預測，以便在內部部署使用。 也可以在 Azure 中的其他地方實際運作，例如透過 [Azure Machine Learning Studio](http://studio.azureml.net) [Web 服務](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)。

## <a name="get-started-with-r-on-hdinsight"></a>開始在 HDInsight 中使用 R
若要在 HDInsight 叢集中包含 R 伺服器，您必須在使用 Azure 入口網站建立 HDInsight 叢集的時候選取 R 伺服器叢集類型。 R 伺服器叢集類型包括在叢集資料節點上的 R 伺服器，以及在邊緣節點上，當作以 R 伺服器為基礎分析之登陸區域的 R 伺服器。 請參閱 [開始使用 HDInsight 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md) ，以了解建立叢集的深入逐步解說。

## <a name="learn-about-data-storage-options"></a>了解資料儲存體選項
HDInsight 叢集 HDFS 檔案系統的儲存體可以與 Azure 儲存體帳戶或 Azure Data Lake Store 關聯。 如此可確保在分析過程中，上傳至叢集儲存體的任何資料，皆保有永續性。 有各種工具可以將資料傳輸到您所選的儲存體選項，包括儲存體帳戶的入口網站型上傳工具，以及 [AzCopy](../storage/storage-use-azcopy.md) 公用程式。

不論您是選擇 Azure Blob 或 Data Lake 作為叢集的主要儲存體，您都可以選擇在叢集佈建期間，新增其他 Blob 和 Data Lake 儲存體的存取權。 如需新增其他帳戶存取權的相關資訊，請參閱[開始使用 HDInsight 中的 R 伺服器 (英文)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-get-started)，以及參閱補充文章[適用於 HDInsight R 伺服器的 Azure 儲存體選項 (英文)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-storage)，以了解如何使用多個儲存體帳戶。

您也可以將 [Azure 檔案](../storage/storage-how-to-use-files-linux.md) 做為在邊緣節點上使用的儲存體選項。 Azure 檔案可讓您將建立於 Azure 儲存體的檔案共用掛接至 Linux 檔案系統。 如需適用於 HDInsight 叢集中 R 伺服器的資料儲存體選項詳細資訊，請參閱[適用於 HDInsight 叢集中的 R 伺服器的 Azure 儲存體選項](hdinsight-hadoop-r-server-storage.md)。

## <a name="access-r-server-on-the-cluster"></a>存取叢集上的 R 伺服器
使用 R 伺服器建立叢集之後，如果您已在佈建程序期間選擇包含 RStudio Server (或之後再新增)，則您可以使用瀏覽器連線到邊緣節點上的 R 伺服器，或者使用 SSH/PuTTY 來存取 R 主控台。 如需在建立叢集之後安裝 RStudio Server 的詳細資訊，請參閱[在 HDInsight 叢集上安裝 RStudio Server](hdinsight-hadoop-r-server-install-r-studio.md)。   

## <a name="develop-and-run-r-scripts"></a>開發和執行 R 指令碼
您所建立與執行的 R 指令碼，可以任意運用 8000 多種開放原始碼 R 封裝，以及 ScaleR 程式庫中的平行與分散式常式。 一般而言，以 R 伺服器在邊緣節點上執行的指令碼會在該節點上的 R 解譯器內執行。 但以 Hadoop Map Reduce (RxHadoopMR) 或 Spark (RxSpark) 設定的計算內容呼叫 ScaleR 函式的步驟除外。

在這種情況下，函數會以分散方式，跨越與參考資料相關聯之叢集的資料 (工作) 節點執行。 如需不同計算內容選項的詳細資訊，請參閱 [適用於 HDInsight 中 R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)。

## <a name="operationalize-a-model"></a>模型運作
完成資料模型化時，可以在 Azure 中與內部部署中運作模型，以為新的資料進行預測。 這個程序稱為評分。 以下提供一些範例：

### <a name="score-in-hdinsight"></a>在 HDInsight 中評分
若要在 HDInsight 中評分，可以針對已載入至儲存體帳戶的新資料檔案，撰寫可呼叫模型的 R 函數以進行預測。 然後將預測存回儲存體帳戶。 您可以視需要在叢集的邊緣節點上執行常式，或使用排程作業來進行。  

### <a name="score-in-azure-machine-learning"></a>在 Azure Machine Learning 中評分
若要使用 Azure Machine Learning Web 服務來評分，請使用 Azure Machine Learning R 封裝開放原始碼 (稱為 [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) ) 來將您的模型發佈為 Azure Web 服務。 為了方便起見，此封裝是預先安裝在邊緣節點。 接下來，使用 Machine Learning 中的功能建立 Web 服務的使用者介面，然後呼叫評分所需的 Web 服務。

如果您選擇這個選項，就必須將所有 ScaleR 模型物件轉換成對等的開放原始碼模型物件，才可搭配 Web 服務使用。 這可以透過使用 ScaleR 強制型轉函數來完成，例如，適用於集成模型的 `as.randomForest()` 。

### <a name="score-on-premises"></a>內部部署評分
若要在建立模型之後進行內部部署評分，可以在 R 中序列化模型，加以下載、還原序列化，然後使用它進行新資料的評分。 您可以使用上述[在 HDInsight 中評分](#scoring-in-hdinsight)所描述的方法，或使用 [DeployR](https://deployr.revolutionanalytics.com/) 進行新資料的評分。

## <a name="maintain-the-cluster"></a>維護叢集
### <a name="install-and-maintain-r-packages"></a>安裝及維護 R 封裝
由於 R 指令碼大多數的步驟會在邊緣節點上執行，因此在邊緣節點上需要有大多數您所使用的 R 封裝。 若要在邊緣節點上安裝其他 R 封裝，可以在 R 中使用一般 `install.packages()` 方法。

在一般情況下，如果您只要在叢集中使用來自 ScaleR 程式庫的常式，就不需要在資料節點上安裝其他 R 封裝。 但您可能需要其他封裝，才可支援在資料節點上使用 **rxExec** 或 **RxDataStep** 執行。

在這種情況下，可以在建立叢集之後，使用指令碼動作來安裝其他封裝。 如需詳細資訊，請參閱 [建立含 R 伺服器的 HDInsight 叢集](hdinsight-hadoop-r-server-get-started.md)。   

### <a name="change-hadoop-map-reduce-memory-settings"></a>變更 Hadoop Map Reduce 記憶體設定
您可以在執行 Map Reduce 作業時，修改叢集以變更 R 伺服器的可用記憶體數目。 若要修改叢集，請使用可透過叢集的 Azure 入口網站刀鋒視窗取得的 Apache Ambari UI。 如需如何存取叢集的 Ambari UI 的指示，請參閱 [使用 Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

您也可以在 **RxHadoopMR** 的呼叫中使用 Hadoop 參數，變更 R 伺服器可用的記憶體數目，如下所示：

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>調整叢集的大小
您可透過入口網站，相應增加或相應減少現有的叢集。 藉由調整，您可以取得處理較大型工作所需的額外容量，或於叢集閒置時調回容量。 如需如何調整叢集規模的指示，請參閱 [管理 HDInsight 叢集](hdinsight-administer-use-portal-linux.md)。

### <a name="maintain-the-system"></a>維護系統
離峰期間，系統會在 HDInsight 叢集的基礎 Linux VM 上執行維護，以套用 OS 修補程式和其他更新。 一般而言，維護作業會在每星期一和星期四上午 3:30 (以 VM 的本地時間為準) 進行。 執行更新時，系統會以一次僅影響四分之一以內叢集的方式進行。  

由於前端節點是備援節點，且並非所有資料節點都會受到影響，因此在這段期間執行的任何工作可能會變慢。 不過，應該都可執行完成。 除非發生需要重建叢集的嚴重失敗，否則您擁有的任何自訂軟體或本機資料，在這些維護事件中皆會保留。

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>了解適用於 HDInsight 叢集中 R 伺服器的 IDE 選項
HDInsight 叢集的 Linux 邊緣節點，是 R 型分析的登陸區域。  HDInsight 的最近幾個版本，提供在邊緣節點上將社群版 [RStudio Server (英文)](https://www.rstudio.com/products/rstudio-server/) 安裝為瀏覽器型整合式開發環境 (IDE) 的預設選項。 比起僅使用 R 主控台，使用 RStudio Server 當作開發及執行 R 指令碼的 IDE 可大幅提升生產力。 如果您選擇不要在建立叢集的時候新增 RStudio Server，而想要於稍後再新增，請參閱[在 HDInsight 叢集上安裝 R Studio Server (英文)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-install-r-studio)。+

另一個完整 IDE 選項是安裝桌面版 IDE (如 Microsoft 最近推出的 [R Tools for Visual Studio (英文)](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS)、RStudio，或 Walware 以 Eclipse 為基礎的 [StatET (英文)](http://www.walware.de/goto/statet))，並藉由使用遠端 Map Reduce 或 Spark 計算內容來存取叢集。  

最後，只要透過 SSH 或 PuTY 連線，您就可以在 Linux 命令提示字元中輸入 **R** 來存取 R 伺服器主控台。 如果您在另一個視窗中執行 R 指令碼開發的文字編輯器，可視需要將您的指令碼區段剪下並貼入 R 主控台，以使用增強的主控台介面。

## <a name="learn-about-pricing"></a>了解價格
與含 R 伺服器的 HDInsight 叢集相關聯的費用結構，類似於標準 HDInsight 叢集的費用。 以各種名稱、資料和邊緣節點的基礎 VM 大小為基準，再加上彈性工時加費。 如需 HDInsight 價格及 30 天免費試用的詳細資訊，請參閱 [HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>後續步驟
瀏覽以下連結，可深入了解如何搭配使用 HDInsight 叢集與 R 伺服器。

* [開始使用 HDInsight 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)
* [將 RStudio Server 新增至 HDInsight (若未在建立叢集期間安裝)](hdinsight-hadoop-r-server-install-r-studio.md)
* [適用於 HDInsight 中 R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)
* [適用於 HDInsight R 伺服器的 Azure 儲存體選項](hdinsight-hadoop-r-server-storage.md)

