 <properties
	pageTitle="使用 IntelliJ IDEA 的 HDInsight 外掛程式建立 Spark Scala 應用程式 | Microsoft Azure"
	description="了解如何建立在 HDInsight Spark 叢集上執行的獨立 Spark 應用程式。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="nitinme"/>


# 使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立 HDInsight Spark Linux 叢集的 Spark 應用程式

本文章逐步指引您開發以 Scala 撰寫的 Spark 應用程式，以及使用 IntelliJ IDEA 的 HDInsight 外掛程式，將它提交給 HDInsight spark 叢集。您可以幾個不同的方式使用此外掛程式：

* 在 HDInsight Spark 叢集上開發並提交 Scala Spark 應用程式
* 存取您的 Azure HDInsight Spark 叢集資源
* 在本機開發並執行 Scala Spark 應用程式

您也可以遵循[這裡](https://mix.office.com/watch/1nqkqjt5xonza)的影片來協助您開始。

>[AZURE.IMPORTANT] 此工具可用來只對 Linux 上的 HDInsight Spark 叢集建立並提交應用程式。


##必要條件

* Azure 訂閱。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
* Oracle Java Development Kit。您可以從[這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)加以安裝。
* IntelliJ IDEA。本文章使用 15.0.1 版。您可以從[這裡](https://www.jetbrains.com/idea/download/)加以安裝。

## 安裝 IntelliJ IDEA 的 HDInsight Tools 外掛程式

IntelliJ IDEA 的 HDInsight 工具外掛程式已是 Azure Toolkit for IntelliJ 的一部份。如需有關如何安裝 Azure 工具組的指示，請參閱[安裝 Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij-installation.md)。

## 在 HDInsight Spark 叢集上執行 Spark Scala 應用程式

1. 啟動 IntelliJ IDEA，並建立新的專案。在新增專案對話方塊中選取下列選項，然後按 [下一步]。

	![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* 從左窗格中，選取 [HDInsight]。
	* 從右窗格中，選取 [HDInsight 上的 Spark (Scala)]。
	* 按 [下一步]。

2. 在下一個視窗中，提供專案詳細資料。

	* 提供專案名稱和專案位置。
	* 對於 [專案 SDK]，請確定您提供的 Java 版本大於 7。
	* 對於 **Scala SDK**，請依序按一下 [建立]、[下載]，然後選取要使用的 Scala 版本。**確定您未使用 2.11.x 版**。此範例使用 **2.10.6** 版。

		![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* 對於 **Spark SDK**，請從[這裡](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)下載並使用 SDK。您也可以略過此項，並改用 [Spark Maven Repository](http://mvnrepository.com/search?q=spark)，不過請確定您已安裝正確的 Maven 儲存機制，以便開發 Spark 應用程式。(例如，您需要先確定您有安裝 Spark Streaming 組件，如果您使用 Spark Streaming 的話；也請確定您使用標示為 Scala 2.10 的儲存機制 - 請勿使用標示為 Scala 2.11 的儲存機制)。

		![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* 按一下 [完成]。

3. Spark 專案會自動為您建立構件。若要查看構件，請遵循下列步驟。

	1. 在 [檔案] 功能表中，按一下 [專案結構]。
	2. 在 [專案結構] 對話方塊中，按一下 [構件]，以查看建立的預設構件。

		![建立 JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)

	您也可以建立自己的構件，方法是按一下上圖中強調顯示的 [+] 圖示。

4. 在 [專案結構] 對話方塊中，按一下 [專案]。如果 [Project SDK (專案 SDK)] 設定為 1.8，請確定 [Project language level (專案語言層級)]設為 [7 - Diamonds, ARM, multi-catch, etc (7 - Diamonds、ARM、Multi-Catch 等)]。

	![設定專案語言層級](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)

5. 新增應用程式的原始程式碼。

	1. 從 [專案總管] 中，以滑鼠右鍵按一下 [src]、指向 [新增]，然後按一下 [Scala 類別]。

		![新增原始程式碼](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. 在 [建立新的 Scala 類別] 對話方塊中，提供一個名稱，並針對 [種類] 選取 [物件]，然後按一下 [確定]。

		![新增原始程式碼](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. 在 **MyClusterApp.scala** 檔案中，貼入下列程式碼。此程式碼會從 HVAC.csv (所有 HDInsight Spark 叢集上均有提供) 讀取資料、擷取在 CSV 的第七個資料行中只有個位數的資料列，並將輸出寫入到叢集預設儲存體容器下的 **/HVACOut**。


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext

			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)

			    val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

			    rdd1.saveAsTextFile("wasbs:///HVACOut")
			  }

			}

5. 在 HDInsight Spark 叢集上執行應用程式。

	1. 從 [專案總管] 中，以滑鼠右鍵按一下專案名稱，然後選取 [Submit Spark Application to HDInsight (將 Spark 應用程式提交給 HDInsight)]。

		![提交 Spark 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. 系統將提示您輸入 Azure 訂用帳戶認證。在 [Spark Submission (提交 Spark)] 對話方塊中，提供下列值。

		* 針對 [Spark clusters (Linux only) (Spark 叢集 (僅限 Linux))]，選取您要在其上執行應用程式的 HDInsight Spark 叢集。

		* 您需要從 IntelliJ 專案中選取構件，或從硬碟中選取一個。

		* 針對 [主要類別名稱] 文字方塊，按一下省略符號 (![省略符號](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png))，並在應用程式的原始程式碼中選取主要類別，然後按一下 [確定]。

			![提交 Spark 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* 因為此範例中的應用程式程式碼不需要任何命令列引數或參考 JAR 或檔案，所以您可以將其餘的文字方塊保留空白。

		* 提供所有輸入之後，對話方塊應該如下所示。

			![提交 Spark 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* 按一下 [提交]。

	3. 視窗底部的 [Spark Submission (提交 Spark)] 索引標籤應會開始顯示進度。您也可以按一下 [Spark Submission] (提交 Spark) 視窗中的紅色按鈕，即可停止應用程式。

        ![Spark 應用程式結果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    在下節中，您會了解如何使用 IntelliJ IDEA 的 HDInsight 外掛程式來存取作業輸出。


## 使用 IntelliJ HDInsight 的外掛程式存取和管理 HDInsight Spark 叢集

您可以使用 HDInsight 外掛程式執行各種作業。

### 存取叢集的儲存體容器

1. 從 [檢視] 功能表中，指向 [工具視窗]，然後按一下 [HDInsight 總管]。如果出現提示，請輸入認證來存取您的 Azure 訂用帳戶。

2. 展開 [HDInsight] 根節點，以查看可用的 HDInsight Spark 叢集清單。

3. 展開叢集名稱以查看叢集的儲存體帳戶和預設儲存體容器。

	![存取叢集儲存體](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. 按一下與叢集相關聯的儲存體容器名稱。在右窗格中，您應該會看到名稱為 **HVACOut** 的資料夾。按兩下以開啟資料夾，即可看到 **part-*** 檔案。開啟其中一個檔案以查看應用程式的輸出。

### 直接在外掛程式中存取作業檢視

1. 從 [HDInsight 總管] 中，展開 Spark 叢集名稱，然後按一下 [作業]。

2. 在右窗格中，[Spark 作業檢視] 索引標籤會顯示已在叢集上執行的所有應用程式。按一下您想要查看更多詳細資料的應用程式名稱。

	![存取作業檢視](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. 適用於 [錯誤訊息]、[作業輸出]、[Livy 作業記錄] 及 [Spark 驅動程式記錄] 的方塊都會根據您選取的應用程式來填入。

4. 您也可以按一下畫面頂端的個別按鈕來開啟 [Spark 歷程記錄 UI] 和 [YARN UI] (應用程式層級)。

### 存取 Spark 歷程記錄伺服器

1. 從 [HDInsight 總管] 中，以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟 Spark 歷程記錄 UI]。出現提示時，輸入叢集的系統管理員認證。在佈建叢集時，您必須已指定這些項目。

2. 在 [Spark 歷程記錄伺服器] 儀表板中，您可以使用應用程式名稱，尋找您剛完成執行的應用程式。在上述程式碼中，您使用 `val conf = new SparkConf().setAppName("MyClusterApp")` 來設定應用程式名稱。因此，Spark 應用程式名稱為 **MyClusterApp**。

### 啟動 Ambari 入口網站

從 [HDInsight 總管] 中，以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [Open Cluster Management Portal (Ambari)(開啟叢集管理入口網站 (Ambari))] 。出現提示時，輸入叢集的系統管理員認證。在佈建叢集時，您必須已指定這些項目。

### 管理 Azure 訂用帳戶

根據預設，HDInsight 外掛程式會列出所有 Azure 訂用帳戶中的 Spark 叢集。如有需要，您可以指定您要存取其叢集的訂用帳戶。從 [HDInsight 總管] 中，以滑鼠右鍵按一下 [HDInsight] 根節點，然後按一下 [管理訂用帳戶]。從對話方塊中，清除您不想存取的訂用帳戶核取方塊，然後按一下 [關閉]。如果您想要從 Azure 訂用帳戶登出，也可以按一下 [登出]。


## 在本機執行 Spark Scala 應用程式

您可以使用 IntelliJ IDEA 的 HDInsight Tools外掛程式，在本機的工作站上執行 Spark Scala 應用程式。一般而言，這類應用程式不需要存取叢集資源 (例如儲存體容器)，而且可在本機上執行並測試。

### 必要條件

在 Windows 電腦上執行本機 Spark Scala 應用程式時，可能會發生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的例外狀況，此例外狀況發生的原因是因為 Windows 上缺少 WinUtils.exe。若要解決這個錯誤，您必須[從這裡下載可執行檔](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)，並將其放至 **C:\\WinUtils\\bin** 之類的位置。然後，您必須新增環境變數 **HADOOP\_HOME**，並將變數的值設為 **C\\WinUtils**。

### 執行本機 Spark Scala 應用程式	 

1. 啟動 IntelliJ IDEA，並建立新的專案。在新增專案對話方塊中選取下列選項，然後按 [下一步]。

	![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* 從左窗格中，選取 [HDInsight]。
	* 從右窗格中，選取 [Spark on HDInsight Local Run Sample (Scala) (HDInsight 上的 Spark 本機執行範例 (Scala))]。
	* 按 [下一步]。

2. 在下一個視窗中，提供專案詳細資料。

	* 提供專案名稱和專案位置。
	* 對於 [專案 SDK]，請確定您提供的 Java 版本大於 7。
	* 對於 **Scala SDK**，請依序按一下 [建立]、[下載]，然後選取要使用的 Scala 版本。**確定您未使用 2.11.x 版**。此範例使用 **2.10.6** 版。

		![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* 對於 **Spark SDK**，請從[這裡](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)下載並使用 SDK。您也可以略過此項，並改用 [Spark Maven Repository](http://mvnrepository.com/search?q=spark)，不過請確定您已安裝正確的 Maven 儲存機制，以便開發 Spark 應用程式。(例如，您需要先確定您有安裝 Spark Streaming 組件，如果您使用 Spark Streaming 的話；也請確定您使用標示為 Scala 2.10 的儲存機制 - 請勿使用標示為 Scala 2.11 的儲存機制)。

		![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)

	* 按一下 [完成]。

3. 範本會在 **src** 資料夾下方安裝可在電腦本機執行的程式碼範例 (**LogQuery**)。

	![本機 Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

4.  以滑鼠右鍵按一下 **LogQuery** 應用程式，然後按一下 [Run 'LogQuery' (執行 'LogQuery')]。您會在底部的 [執行] 索引標籤上看到如下的輸出。

	![Spark 應用程式本機執行結果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## 轉換現有的 IntelliJ IDEA 應用程式以使用 HDInsight 工具外掛程式

您也可以將 IntelliJ IDEA 中建立的 Spark Scala 應用程式轉換為與 HDInsight 工具外掛程式相容。這可讓您使用工具，將應用程式提交給 HDInsight Spark 叢集。您可以執行下列步驟來這麼做：

1. 對於使用 IntelliJ IDEA 建立的現有 Spark Scala 應用程式，開啟相關聯的.iml 檔案。
2. 在根層級中，您會看到如下的 **module** 元素：

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. 編輯該元素以加入 `UniqueKey="HDInsightTool"`，使 **module** 元素看起來如下所示：

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. 儲存變更。您的應用程式現在應該與 HDInsight 工具外掛程式相容。您可以滑鼠右鍵按一下 [專案總管] 中的專案名稱，來測試此情況。快顯功能表現在應該具有 [Submit Spark Application to HDInsight (將 Spark 應用程式提交給 HDInsight)] 的選項。


## 疑難排解

### 本機執行發生「請使用較大的堆積大小」錯誤

在 Spark 1.6 中，如果您在本機執行期間使用 32 位元的 Java SDK，您可能會遇到下列錯誤︰

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
    	at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
    	at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
    	at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
    	at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
    	at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
    	at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
    	at LogQuery$.main(LogQuery.scala:53)
    	at LogQuery.main(LogQuery.scala)
    	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    	at java.lang.reflect.Method.invoke(Method.java:606)
    	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

這是因為堆積大小不足，導致 Spark 無法執行，因為 Spark 至少需要 471 MB (如有需要，您可以從 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) 取得更多詳細資料)。其中一個簡單的解決方案就是使用 64 位元的 Java SDK。您也可以新增下列選項來變更 IntelliJ 中的 JVM 設定：

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Spark 應用程式本機執行結果](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## 意見反應和已知問題

目前不支援直接檢視 Spark 輸出，我們正在處理此問題。

如果您有任何建議或意見反應，或使用此工具時，遇到任何問題，歡迎寄電子郵件到 hdivstool@microsoft.com。

## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 建立及執行應用程式

* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)

* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### 工具和擴充功能

* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式遠端偵錯 Spark 應用程式](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [使用 Eclipse 的 HDInsight Tools 外掛程式來建立 Spark 應用程式](hdinsight-apache-spark-eclipse-tool-plugin.md)

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0727_2016-->