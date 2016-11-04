---
title: 使用 HDInsight 上的 Spark 分析 Application Insights 記錄檔 | Microsoft Docs
description: 了解如何將 Application Insights 記錄檔匯出至 blob 儲存體，並接著使用 HDInsight 上的 Spark 分析記錄檔。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/15/2016
ms.author: larryfr

---
# 使用 HDInsight 上的 Spark 分析 Application Insights 遙測記錄檔
[Visual Studio Application Insights](../application-insights/app-insights-overview.md) 是一項分析服務，可監視您的 Web 應用程式。可以將 Application Insights 產生的遙測資料匯出至 Azure 儲存體，並從該處就可以進行 HDInsight 分析。

在本文件中，您將學習如何使用 HDInsight 加上 Apache Spark 來分析 Application Insights 遙測資料。

## 必要條件
* Azure 訂用帳戶。
* 設定要使用 Application Insights 的應用程式。
* 熟悉以 Linux 為基礎的 HDInsight 叢集的建立程序。如果您不熟悉叢集建立，請參閱 [在 HDInsight 上建立 Spark](hdinsight-apache-spark-jupyter-spark-sql.md) 取得詳細資訊。
  
  > [!NOTE]
  > 這份文件不提供建立新叢集的逐步解說。但會提供其他參考文件的連結，由其他文件提供如何建立可存取遙測資料之叢集的資訊。
  > 
  > 
* 網頁瀏覽器。這是用來使用 Jupyter Notebook 以互動方式執行分析。

開發和測試本文件時使用了以下項目︰

* Application Insights 遙測資料，由[設定為使用 Application Insights 的 Node.js Web 應用程式](../application-insights/app-insights-nodejs.md)產生。
* 在 Linux 上使用 HDInsight 叢集版本 3.4 上的 Spark 分析資料。

## 架構與規劃
下圖說明此範例的服務架構：

![顯示資料從 Application Insights 傳輸至 blob 儲存體，然後由 HDInsight 上的 Spark 處理的圖](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### Azure 儲存體
HDInsight 叢集可以直接從 Azure 儲存體帳戶存取區塊 blob，Application Insights 可以設定為持續將遙測資訊匯出到 Azure 儲存體中的 blob。不過，有一些您必須遵守的需求︰

* **位置**：儲存體帳戶必須和 HDInsight 位於相同的區域中。這可在存取資料時減少延遲，並避免在區域之間移動資料時所發生的出口流量費用。
* **blob 類型**：HDInsight 僅支援區塊 blob。Application Insights 預設為使用區塊 blob，因此應該使用預設項目來搭配 HDInsight。
* **存取權限**：如果您在 Application Insights 連續匯出和 HDInsight 的預設儲存體使用相同的儲存體帳戶，HDInsight 便會具有對 Application Insights 遙測資料的完整存取權。這就表示可以刪除 HDInsight 叢集中的遙測資料。
  
    相反地，建議您為 HDInsight 和 Application Insights 遙測使用不同的儲存體帳戶，並且[使用共用存取簽章 (SAS) 來限制對 HDInsight 上資料的存取](hdinsight-storage-sharedaccesssignature-permissions.md)。使用 SAS 可讓您授與對 HDInsight 遙測資料的唯讀存取權。

### 資料結構描述
Application Insights 提供[匯出資料模型](../application-insights/app-insights-export-data-model.md)資訊，做為匯出至 blob 之遙測資料的格式依據。這份文件中的步驟使用 Spark SQL 來處理資料。Spark SQL 可以自動產生 Application Insights 所記錄的 JSON 資料結構的結構描述，因此您不需要在執行分析時手動定義結構描述。

## 匯出遙測資料
依照[設定連續匯出](../application-insights/app-insights-export-telemetry.md)中的步驟設定您的 Application Insights 將遙測資訊匯出到 Azure 儲存體 blob。

## 設定 HDInsight 來存取資料
使用[使用共用存取簽章 (SAS) 來限制對 HDInsight 上資料的存取](hdinsight-storage-sharedaccesssignature-permissions.md)中的資訊來建立 SAS，用於包含匯出之遙測資料的 blob 容器。SAS 應該提供資料的唯讀存取權。

「共用存取簽章」的文件中提供資訊說明如何將 SAS 儲存體加入現有的以 Linux 為基礎的 HDInsight 叢集。它也提供如何在建立新的 HDInsight 叢集時將它加入的資訊。

## 使用 Python 分析資料 (PySpark)
1. 在 [Azure 入口網站](https://portal.azure.com)中選取您 HDInsight 叢集上的 Spark。在 [快速連結] 區段中，選取 [叢集儀表板]，然後選取 [叢集儀表板] 刀鋒視窗中的 [Jupyter Notebook]。
   
    ![叢集儀表板](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. 在 Jupyter 頁面右上角依序選取 [新增]、[PySpark]。這會開啟新的瀏覽器索引標籤，其中包含以 Python 為基礎的 Jupyter Notebook。
3. 在頁面上第一個欄位 (稱為 **儲存格**) 輸入下列命令︰
   
        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   
    這會讓 Spark 以遞迴方式存取輸入資料的目錄結構。Application Insights 遙測會記錄到類似這樣的目錄結構︰
   
        /{telemetry type}/YYYY-MM-DD/{##}/
4. 使用 **SHIFT + ENTER** 執行程式碼。在儲存格的左邊，在方括號之間會出現 ' *' 即表示正在執行此儲存格中的程式碼。當執行完成時，' *' 會變成數字，儲存格下方會顯示類似以下的輸出︰
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. 新的儲存格將會建立在第一個儲存格之下。在新的儲存格中輸入下列命令。將 **CONTAINER** 和 **STORAGEACCOUNT** 取代為您設定 Application Insights 連續匯出時所使用的 Azure 儲存體帳戶名稱和 blob 容器名稱。
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    使用 **SHIFT + ENTER** 執行程此儲存格。您將看到類似以下的輸出：
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    傳回的 wasb 路徑是 Application Insights 遙測資料的位置。將儲存格中的 `hdfs dfs -ls` 這一行變更為使用傳回的 wasb 路徑，然後使用 **SHIFT + ENTER** 再執行一次儲存格。此時，結果應該會顯示包含遙測資料的目錄。
   
   > [!NOTE]
   > 本節中步驟的其餘部分使用 `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` 目錄。這個目錄可能不存在，除非您的遙測資料是用於 Web 應用程式。如果您使用不包含要求目錄的遙測資料，請選取其他目錄，並將其餘的步驟調整為使用該目錄和儲存在其中之資料的結構描述。
   > 
   > 
6. 在下一個儲存格中輸入下列命令。將 **WASB\_PATH** 取代為先前步驟中的路徑。
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    這會從連續匯出程序匯出的 JSON 檔案建立新的資料框架。使用 **SHIFT + ENTER** 執行此儲存格。
7. 在下一個儲存格中輸入並執行下列命令，以檢視 Spark 為 JSON 檔案建立的結構描述︰
   
        jsonData.printSchema()
   
    每種類型的遙測資料的結構描述皆不同。以下是針對 Web 要求產生的結構描述 (資料儲存在 `Requests` 子目錄中)：
   
        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. 使用下列命令將資料框架註冊為暫存資料表，並針對資料執行查詢︰
   
        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   
    此查詢會傳回 context.location.city 不是 null 的前 20 筆記錄的 city 資訊。
   
   > [!NOTE]
   > context 結構會出現在 Application Insights 記錄的所有遙測中；不過，在您的記錄檔中可能不會填入 city 元素。使用結構描述找出您可以查詢可能包含您的記錄檔資料的其他元素。
   > 
   > 
   
    這個查詢會傳回類似以下的資訊：
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## 使用 Scala 分析資料
1. 在 [Azure 入口網站](https://portal.azure.com)中選取您 HDInsight 叢集上的 Spark。在 [快速連結] 區段中，選取 [叢集儀表板]，然後選取 [叢集儀表板] 刀鋒視窗中的 [Jupyter Notebook]。
   
    ![叢集儀表板](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. 在 Jupyter 頁面右上角依序選取 [新增]、[Scala]。這會開啟新的瀏覽器索引標籤，其中包含以 Scala 為基礎的 Jupyter Notebook。
3. 在頁面上第一個欄位 (稱為 **儲存格**) 輸入下列命令︰
   
        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   
    這會讓 Spark 以遞迴方式存取輸入資料的目錄結構。Application Insights 遙測會記錄到類似這樣的目錄結構︰
   
        /{telemetry type}/YYYY-MM-DD/{##}/
4. 使用 **SHIFT + ENTER** 執行程式碼。在儲存格的左邊，在方括號之間會出現 ' *' 即表示正在執行此儲存格中的程式碼。當執行完成時，' *' 會變成數字，儲存格下方會顯示類似以下的輸出︰
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. 新的儲存格將會建立在第一個儲存格之下。在新的儲存格中輸入下列命令。將 **CONTAINER** 和 **STORAGEACCOUNT** 取代為您設定 Application Insights 連續匯出時所使用的 Azure 儲存體帳戶名稱和 blob 容器名稱。
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    使用 **SHIFT + ENTER** 執行程此儲存格。您將看到類似以下的輸出：
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    傳回的 wasb 路徑是 Application Insights 遙測資料的位置。將儲存格中的 `hdfs dfs -ls` 這一行變更為使用傳回的 wasb 路徑，然後使用 **SHIFT + ENTER** 再執行一次儲存格。此時，結果應該會顯示包含遙測資料的目錄。
   
   > [!NOTE]
   > 本節中步驟的其餘部分使用 `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` 目錄。這個目錄可能不存在，除非您的遙測資料是用於 Web 應用程式。如果您使用不包含要求目錄的遙測資料，請選取其他目錄，並將其餘的步驟調整為使用該目錄和儲存在其中之資料的結構描述。
   > 
   > 
6. 在下一個儲存格中輸入下列命令。將 **WASB\_PATH** 取代為先前步驟中的路徑。
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    這會從連續匯出程序匯出的 JSON 檔案建立新的資料框架。使用 **SHIFT + ENTER** 執行此儲存格。
7. 在下一個儲存格中輸入並執行下列命令，以檢視 Spark 為 JSON 檔案建立的結構描述︰
   
        jsonData.printSchema
   
    每種類型的遙測資料的結構描述皆不同。以下是針對 Web 要求產生的結構描述 (資料儲存在 `Requests` 子目錄中)：
   
        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. 使用下列命令將資料框架註冊為暫存資料表，並針對資料執行查詢︰
   
        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   
    此查詢會傳回 context.location.city 不是 null 的前 20 筆記錄的 city 資訊。
   
   > [!NOTE]
   > context 結構會出現在 Application Insights 記錄的所有遙測中；不過，在您的記錄檔中可能不會填入 city 元素。使用結構描述找出您可以查詢可能包含您的記錄檔資料的其他元素。
   > 
   > 
   
    這個查詢會傳回類似以下的資訊：
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## 後續步驟
如需在 Azure 中使用 Spark 處理資料和服務的範例，請參閱下列文件︰

* [Spark 和 BI：搭配 BI 工具來使用 HDInsight 中的 Spark 以執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

如需建立和執行 Spark 應用程式的詳細資訊，請參閱下列文件︰

* [使用 Scala 來建立獨立的應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行工作](hdinsight-apache-spark-livy-rest-interface.md)

<!---HONumber=AcomDC_0914_2016-->