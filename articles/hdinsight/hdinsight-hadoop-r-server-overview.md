<properties
	pageTitle="什麼是 HDInsight 中的 R？ HDInsight (預覽) 中的 R 伺服器簡介 | Microsoft Azure"
	description="什麼是 HDInsight (預覽) 中的 R 伺服器，以及如何使用 R 伺服器建立巨量資料分析的應用程式。"
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# HDInsight (預覽) 中的 R 伺服器概觀

有了 Microsoft Azure HDInsight 之後，在 Azure 中建立 HDInsight 叢集時，即可選擇使用 Microsoft R 伺服器。這項新功能可讓資料科學家、統計學家以及 R 程式設計人員隨其所需存取 HDInsight 上可調整大小的分散式分析方法。

您可依據手邊的專案和工作調整叢集大小，並於不再需要時加以終止。因為這些叢集屬於 Azure HDInsight 的一部分，所以具備企業級全年無休支援、99.9% 執行時間的 SLA，同時具備與 Azure 生態系統中其他元件整合的彈性。

>[AZURE.NOTE] 只有 HDInsight Premium 提供 R 伺服器，且目前 HDInsight Premium 僅提供給 Hadoop 與 Spark 叢集使用。因此，在 HDInsight 上目前只可搭配 Hadoop 與 Spark 叢集使用 R 伺服器。如需詳細資訊，請參閱 [HDInsight 提供的不同服務等級與 Hadoop 元件？](hdinsight-component-versioning.md)。

HDInsight 上的 R 伺服器能為在載入至 Azure Blob 儲存體的大型資料集上進行之 R 型分析，提供最新的功能。因為 R 伺服器的建置基礎為開放原始碼 R，因此您所建置的 R 應用程式可以任意運用 8000 多種開放原始碼 R 封裝，以及 ScaleR 中的常式與 R 伺服器隨附的 Microsoft 巨量資料分析封裝。

Premium 叢集的邊緣節點提供便利的地方，以便連接到叢集並執行 R 指令碼。有了邊緣節點之後，即可選擇跨邊緣節點伺服器的核心，執行 ScaleR 的平行分散式函數。也可以選擇透過使用 ScaleR 的 Hadoop Map Reduce 或 Spark 計算內容，跨叢集的節點執行這些函數。

可以下載分析所產生的模型或預測，以便在內部部署使用。也可以在 Azure 中的其他地方實際運作，例如透過 [Azure Machine Learning Studio](http://studio.azureml.net) [Web 服務](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)。

## 開始在 HDInsight 中使用 R

若要在 HDInsight 叢集中納入 R 伺服器，必須在使用 Azure 入口網站建立叢集時，使用 Premium 選項建立 Hadoop 或 Spark 叢集。這兩種叢集類型皆使用相同的組態，其中包括叢集資料節點上的 R 伺服器，以及作為 R 伺服器型分析登陸區域的邊緣節點。請參閱[開始使用 HDInsight 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)，以了解建立叢集的深入逐步解說。

## 了解資料儲存體選項

HDInsight 叢集的預設儲存體為 Blob 儲存體，其中含有對應至 Blob 容器的 HDFS 檔案系統。如此可確保在分析的過程中，任何上傳至叢集儲存體或寫入叢集儲存體的資料，皆保有永續性。您可以使用 [AzCopy](../storage/storage-use-azcopy.md) 公用程式，從 Blob 來回複製資料。

除了使用 Blob 儲存體，您還可以選擇將 [Azure Data Lake 儲存體](https://azure.microsoft.com/services/data-lake-store/)使用於您的叢集。如果您使用 Data Lake，則可針對 HDFS 儲存體同時使用 Blob 儲存體和 Data Lake。

您也可以將 [Azure 檔案](../storage/storage-how-to-use-files-linux.md)做為在邊緣節點上使用的儲存體選項。Azure 檔案可讓您將建立於 Azure 儲存體的檔案共用掛接至 Linux 檔案系統。如需適用於 HDInsight 叢集中 R 伺服器的資料儲存體選項詳細資訊，請參閱[適用於 HDInsight 叢集中的 R 伺服器的儲存體選項](hdinsight-hadoop-r-server-storage.md)。

## 存取叢集上的 R 伺服器

建立含 R 伺服器的叢集之後，即可透過 SSH/Putty 連線到叢集邊緣節點的 R 主控台。如果您選擇在邊緣節點上安裝選用的 RStudio 伺服器 IDE，也可透過瀏覽器進行連線。如需安裝 RStudio 伺服器的詳細資訊，請參閱[在 HDInsight 叢集上安裝 RStudio 伺服器](hdinsight-hadoop-r-server-install-r-studio.md)。

## 開發和執行 R 指令碼

您所建立與執行的 R 指令碼，可以任意運用 8000 多種開放原始碼 R 封裝，以及 ScaleR 程式庫中的平行與分散式常式。一般而言，在邊緣節點上 R Server 中執行的指令碼會在該節點上的 R 解譯器內執行。但以 Hadoop Map Reduce (RxHadoopMR) 或 Spark (RxSpark) 設定的計算內容呼叫 ScaleR 函式的步驟除外。

在這種情況下，函數會以分散方式，跨越與參考資料相關聯之叢集的資料 (工作) 節點執行。如需不同計算內容選項的詳細資訊，請參閱[適用於 HDInsight Premium 中 R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)。

## 模型運作

完成資料模型化時，可以在 Azure 中與內部部署中運作模型，以為新的資料進行預測。這個程序稱為評分。以下提供一些範例：

### 在 HDInsight 中評分

若要在 HDInsight 中評分，可以針對已載入至儲存體帳戶的新資料檔案，撰寫可呼叫模型的 R 函數以進行預測。然後將預測存回儲存體帳戶。您可以視需要在叢集的邊緣節點上執行常式，或使用排程作業來進行。

### 在 Azure Machine Learning 中評分

若要使用 Azure Machine Learning Web 服務來評分，請使用 Azure Machine Learning R 封裝開放原始碼 (稱為 [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)) 來將您的模型發佈為 Azure Web 服務。為了方便起見，此封裝是預先安裝在邊緣節點。接下來，使用 Machine Learning 中的功能建立 Web 服務的使用者介面，然後呼叫評分所需的 Web 服務。

如果您選擇這個選項，就必須將所有 ScaleR 模型物件轉換成對等的開放原始碼模型物件，才可搭配 Web 服務使用。這可以透過使用 ScaleR 強制型轉函數來完成，例如，適用於集成模型的 `as.randomForest()`。


### 內部部署評分

若要在建立模型之後進行內部部署評分，可以在 R 中序列化模型，加以下載、還原序列化，然後使用它進行新資料的評分。您可以使用上述[在 HDInsight 中評分](#scoring-in-hdinsight)所描述的方法，或使用 [DeployR](https://deployr.revolutionanalytics.com/) 進行新資料的評分。

## 維護叢集

### 安裝及維護 R 封裝

由於大多數 R 指令碼會在邊緣節點上執行，因此在邊緣節點上需要有大多數您所使用的 R 封裝。若要在邊緣節點上安裝其他 R 封裝，可以在 R 中使用一般 `install.packages()` 方法。

在大部分的情況下，如果您只要在叢集中使用來自 ScaleR 程式庫的常式，就不需要在資料節點上安裝其他 R 封裝。但您可能需要其他封裝，才可支援在資料節點上使用 **rxExec** 或 **RxDataStep** 執行。

在這種情況下，必須在建立叢集之後，使用指令碼動作來指定其他封裝。如需詳細資訊，請參閱[建立含 R 伺服器的 HDInsight 叢集](hdinsight-hadoop-r-server-get-started.md)。

### 變更 Hadoop Map Reduce 記憶體設定

您可以在執行 Map Reduce 作業時，修改叢集以變更 R 伺服器的可用記憶體數目。若要修改叢集，請使用可透過叢集的 Azure 入口網站刀鋒視窗取得的 Apache Ambari UI。如需如何存取叢集的 Ambari UI 的指示，請參閱[使用 Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

您也可以在 **RxHadoopMR** 的呼叫中使用 Hadoop 參數，變更 R 伺服器可用的記憶體數目，如下所示：

	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### 調整叢集的大小

您可透過入口網站，相應增加或相應減少現有的叢集。藉由調整，您可以取得處理較大型工作所需的額外容量，或於叢集閒置時調回容量。如需如何調整叢集規模的指示，請參閱[管理 HDInsight 叢集](hdinsight-administer-use-portal-linux.md)。

### 維護系統

離峰期間，系統會在 HDInsight 叢集的基礎 Linux VM 上執行維護，以套用 OS 修補程式和其他更新。一般而言，維護作業會在每星期一和星期四上午 3:30 (以 VM 的本地時間為準) 進行。執行更新時，系統會以一次僅影響四分之一以內叢集的方式進行。

由於前端節點是備援節點，且並非所有資料節點都會受到影響，因此在這段期間執行的任何工作可能會變慢。不過，應該都可執行完成。除非發生需要重建叢集的嚴重失敗，否則您擁有的任何自訂軟體或本機資料，在這些維護事件中皆會保留。

## 了解適用於 HDInsight 叢集中 R 伺服器的 IDE 選項

HDInsight Premium 叢集的 Linux 邊緣節點，是 R 型分析的登陸區域。連線到叢集之後，即可在 Linux 命令提示字元處輸入 **R**，啟動 R 伺服器的主控台介面。如果您在另一個視窗中執行 R 指令碼開發的文字編輯器，可視需要將您的指令碼區段剪下並貼入 R 主控台，以使用增強的主控台介面。

更複雜的 R 指令碼開發工具是可在桌面上使用的 R 型 IDE，例如 Microsoft 最近宣布的 [R Tools for Visual Studio](https://www.visualstudio.com/zh-TW/features/rtvs-vs.aspx) (RTVS)。這是 [RStudio](https://www.rstudio.com/products/rstudio-server/) 提供的一系列桌面和伺服器工具。您也可以使用 Walware 的 Eclipse 型 [StatET](http://www.walware.de/goto/statet)。

另一個選項是在 Linux 邊緣節點上安裝 IDE。比較常用的選擇是 [RStudio 伺服器](https://www.rstudio.com/products/rstudio-server/)，其提供可讓遠端用戶端使用的瀏覽器型整合式開發環境 (IDE)。在 HDInsight Premium 叢集的邊緣節點上安裝 RStudio 伺服器時，可提供完整的 IDE 體驗供透過叢集上的 R 伺服器來開發及執行 R 指令碼。相較於 R 主控台，它可以大幅提高生產力。如果您想要使用 RStudio 伺服器，請參閱[在 HDInsight 叢集上安裝 RStudio 伺服器](hdinsight-hadoop-r-server-install-r-studio.md)。

## 了解價格

與含 R 伺服器的 HDInsight Premium 叢集相關聯的費用結構，類似於標準 HDInsight 叢集的費用。以各種名稱、資料和邊緣節點的基礎 VM 大小為基準，再加上 Premium 的彈性工時加費。如需 HDInsight Premium 價格 (包括公開預覽期間價格) 及 30 天免費試用的詳細資訊，請參閱 [HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

## 後續步驟

瀏覽以下連結，可深入了解如何搭配使用 HDInsight 叢集與 R 伺服器。

- [開始使用 HDInsight 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)

- [將 RStudio 伺服器加入 HDInsight Premium 中](hdinsight-hadoop-r-server-install-r-studio.md)

- [適用於 HDInsight (預覽) 中 R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)

- [適用於 HDInsight Premium R 伺服器的 Azure 儲存體選項](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0817_2016-->