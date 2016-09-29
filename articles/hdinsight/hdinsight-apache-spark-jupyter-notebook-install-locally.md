<properties 
	pageTitle="在電腦上安裝 Jupyter Notebook 並連接到 Azure HDInsight Spark 叢集 | Microsoft Azure" 
	description="了解如何在電腦本機安裝 Jupyter Notebook 並連接到 Azure HDInsight 上的 Apache Spark 叢集" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="jhubbard" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="nitinme"/>


# 在電腦上安裝 Jupyter Notebook 並連接到 HDInsight Linux 上的 Apache Spark 叢集

在這篇文章中，您將了解如何搭配含有 Spark magic 的自訂 PySpark (適用於 Python) 和 Spark (適用於 Scala) 核心來安裝 Jupyter Notebook，然後將 Notebook 連接到 HDInsight 叢集。在您的本機電腦上安裝 Jupyter 可以有數種原因，而且也會面臨數種挑戰。如需原因和挑戰的清單，請參閱這篇文章結尾的[為什麼我應該在我的電腦上安裝 Jupyter](#why-should-i-install-jupyter-on-my-computer)。

在電腦上安裝 Jupyter 和 Spark magic 涉及三個主要步驟。

* 安裝 Jupyter Notebook
* 安裝含有 Spark magic 的 PySpark 和 Spark 核心
* 設定 Spark magic 以存取 HDInsight 上的 Spark 叢集

如需有關可供 HDInsight 叢集之相關 Jupyter Notebook 使用的自訂核心和 Spark magic 的詳細資訊，請參閱 [HDInsight 上的 Apache Spark Linux 叢集可供 Jupyter Notebook 使用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)。

##必要條件

此處所列的必要條件不是針對安裝 Jupyter。這些是用來在安裝 Notebook 之後將 Jupyter Notebook 連接到 HDInsight 叢集。

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## 在電腦上安裝 Jupyter Notebook

您必須先安裝 Python，才能安裝 Jupyter Notebook。Python 和 Jupyter 皆為 [Ananconda 發行版本](https://www.continuum.io/downloads)的一部分。當您安裝 Anaconda 時，實際上安裝的是某個 Python 發行版本。安裝 Anaconda 之後，您便可以執行命令來新增 Jupyter 安裝。本節提供您必須遵循的指示。

1. 下載您平台適用的 [Anaconda 安裝程式](https://www.continuum.io/downloads)，然後執行安裝程式。執行安裝精靈時，請確定您選取將 Anaconda 新增至 PATH 變數的選項。

2. 執行下列命令來安裝 Jupyter。

		conda install jupyter

	如需有關安裝 Jupyter 的詳細資訊，請參閱[使用 Anaconda 來安裝 Jupyter](http://jupyter.readthedocs.io/en/latest/install.html)。

## 安裝核心和 Spark magic

在本節中，您會安裝 Spark magic、PySpark 核心及 Spark 核心，然後設定這些核心以連接到在 Azure HDInsight 中執行的 Apache Spark 叢集。

1. 從 [Github](https://github.com/jupyter-incubator/sparkmagic/archive/publicpreview0.5.zip) 下載最新的 Spark magic 公開預覽。

2. 將下載的檔案解壓縮到磁碟上的位置。在下列指示中，我們將此路徑稱為 `$SPARKMAGIC_PATH`。

2. 執行下列命令

		pip install -r $SPARKMAGIC_PATH/requirements.txt  

3. 執行下列命令來安裝 Spark magic。

		pip install -e $SPARKMAGIC_PATH

4. 安裝 PySpark 和 Spark 核心執行下列命令。

		jupyter-kernelspec install $SPARKMAGIC_PATH/remotespark/kernels/sparkkernel
		jupyter-kernelspec install $SPARKMAGIC_PATH/remotespark/kernels/pysparkkernel

## 設定 Spark magic 以存取 HDInsight Spark 叢集

在本節中，您會設定稍早安裝的 Spark magic，以連線到您必須已在 Azure HDInsight 中建立的 Apache Spark 叢集。

1. Jupyter 組態資訊通常儲存在使用者主目錄中。若要在任何作業系統平台上找出您的主目錄，輸入下列命令。

	啟動 Python 殼層。在命令視窗中，輸入下列命令：

		python

	在 Python 殼層中，輸入下列命令來找出主目錄。

		import os
		print os.path.expanduser('~')

2. 瀏覽至主目錄，然後建立一個叫做 **.sparkmagic** 的資料夾 (如果尚未存在)。

3. 在該資料夾中，建立一個叫做 **config.json** 的檔案，然後在檔案中新增下列 JSON 程式碼片段。

		{
		  "kernel_python_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  },
		  "kernel_scala_credentials" : {
		    "username": "{USERNAME}",
		    " base64_password ": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  }
		}

4. 以適當的值替代 **{USERNAME}**、**{CLUSTERDNSNAME}** 及 **{BASE64ENCODEDPASSWORD}**。您可以在您慣用的程式設計語言中或線上，使用一些公用程式來產生 base64 編碼的密碼，以做為您的實際密碼。以下是一個從命令提示字元執行的簡單 Python 程式碼片段︰

		python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. 啟動 Jupyter。從命令提示字元使用下列命令。

		jupyter notebook

6. 確認您可以使用 Jupyter Notebook 來連接到叢集，並且可以使用核心隨附的 Spark magic。請執行下列步驟：

	1. 建立新的 Notebook。從右下角，按一下 [新增]。您應該會看到預設核心 **Python2**，以及您安裝的兩個新核心 **PySpark** 和 **Spark**。

		![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "建立新的 Jupyter Notebook")

	
		按一下 [PySpark]。


	2. 執行下列程式碼片段。

			%%sql
			SELECT * FROM hivesampletable LIMIT 5

		如果您可以順利擷取輸出，即表示已測試您對 HDInsight 叢集的連線。

	>[AZURE.TIP] 如果您想要更新 Notebook 組態以連接到不同的叢集，請將 config.json 更新成一組新的值，如上述的步驟 3 所示。

## 為什麼我應該在我的電腦上安裝 Jupyter？

您想要在您的電腦上安裝 Jupyter，然後將其連接至 HDInsight 上的 Spark 叢集有數種原因。

* 雖然 Azure HDInsight 的 Spark 叢集上已經有 Jupyter Notebook 可用，但在電腦上安裝 Jupyter 可讓您選擇在本機建立 Notebook、針對正在執行的叢集測試您的應用程式，然後將 Notebook 上傳到叢集。若要將 Notebook 上傳到叢集，您可以使用在叢集上執行的 Jupyter Notebook 來上傳它們，或將它們儲存到與叢集關聯之儲存體帳戶的 [/HdiNotebooks] 資料夾中。如需有關 Notebook 在叢集上的儲存方式詳細資訊，請參閱 [Jupyter Notebook 會儲存在哪裡](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)？
* 使用本機可用的 Notebook，您可以根據您的應用程式需求，連接至不同的 Spark 叢集。
* 您可以使用 GitHub 實作來源控制系統，並且具有 Notebook 的版本控制。您也可以有共同作業環境，其中多個使用者可以使用相同的 Notebook。
* 您甚至不需要啟用叢集，即可在本機使用 Notebook。您只需要叢集針對 Notebook 進行測試，不必以手動方式管理您的 Notebook 或開發環境。
* 設定自己的本機開發環境比設定叢集的 Jupyter 安裝更容易。您可以利用您在本機安裝的所有軟體，而不需要設定一或多個遠端叢集。

>[AZURE.WARNING] 將 Jupyter 安裝在本機電腦上，多個使用者可以同時在相同的 Spark 叢集上執行相同的 Notebook。在這種情況下，會建立多個 Livy 工作階段。如果您遇到問題，而且想要偵錯，則追蹤哪個 Livy 工作階段屬於哪個使用者會是複雜的工作。




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

* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->