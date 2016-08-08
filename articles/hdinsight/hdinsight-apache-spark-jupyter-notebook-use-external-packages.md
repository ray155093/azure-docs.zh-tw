<properties 
	pageTitle="在 HDInsight 上 Apache Spark 叢集中搭配 Jupyter Notebook 使用外部套件 | Azure"
	description="說明如何設定讓 HDInsight Spark 叢集隨附之 Jupyter Notebook 使用外部 Spark 套件的逐步指示。" 
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


# 在 HDInsight Linux 上的 Apache Spark 叢集中搭配 Jupyter Notebook 使用外部套件

了解如何在 HDInsight (Linux) 上的 Apache Spark 叢集中設定讓 Jupyter Notebook 使用不是叢集中現成隨附的外部、社群提供套件。

您可以搜尋 [Maven 儲存機制](http://search.maven.org/)來取得可用套件的完整清單。您也可以從其他來源取得可用套件清單。例如，從 [Spark 套件](http://spark-packages.org/)可以取得社群提供套件的完整清單。

在這篇文章中，您將了解如何搭配 Jupyter Notebook 使用 [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件。

##必要條件

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## 搭配 Jupyter Notebook 使用外部套件 

1. 在 [Azure 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ，然後在 [叢集儀表板] 刀鋒視窗中按一下 [Jupyter Notebook]。出現提示時，輸入叢集的系統管理員認證。

	> [AZURE.NOTE] 您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。使用您叢集的名稱取代 __CLUSTERNAME__：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。按一下 [新增]，然後按一下 [Spark]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "提供 Notebook 的名稱")

4. 您將使用 `%%configure` magic 來設定讓 Notebook 使用外部套件。在使用外部套件的 Notebook 中，確定您在第一個程式碼單元中呼叫 `%%configure` magic。這可確保將核心設定為在啟動工作階段之前即使用此套件。

		%%configure
		{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


	>[AZURE.IMPORTANT] 如果您忘記在第一個單元中設定核心，您可以搭配 `-f` 參數使用 `%%configure`，但這會重新啟動工作階段，而所有進度都將遺失。

5. 在上述程式碼片段中，`packages` 會預期有「Maven 中央儲存機制」中的 maven 座標。在此程式碼片段中，`com.databricks:spark-csv_2.10:1.4.0` 是 **spark-csv** 套件的 maven 座標。以下說明如何建立套件的座標。

	a.在「Maven 儲存機制」中找出套件。針對本教學課程，我們使用 [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。
	
	b.從儲存機制收集 [GroupId]、[ArtifactId] 及 [版本] 的值。

	![搭配 Jupyter Notebook 使用外部套件](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "搭配 Jupyter Notebook 使用外部套件")

	c.串連三個值，其中以冒號分隔 (**:**)。

		com.databricks:spark-csv_2.10:1.4.0

6. 以 `%%configure` magic 執行程式碼單元。這會將基礎 Livy 工作階段設定為使用您提供的套件。在 Notebook 的後續單元中，您現在已可以使用套件，如以下所示。

		val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. 接著，您可以執行程式碼片段 (如以下所示) 以檢視來自您在上一個步驟中所建立之資料框架的資料。

		df.show()

		df.select("Time").count()


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

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式遠端偵錯 Spark 應用程式](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0727_2016-->