---
title: "從 Azure Data Factory 叫用 Spark 程式"
description: "了解如何從 Azure Data Factory 使用 MapReduce 活動叫用 Spark 程式。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 3c7d109ec7fd92859cad4ded7422105e8094485c
ms.lasthandoff: 03/30/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>從 Data Factory 叫用 Spark 程式
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 活動](data-factory-hive-activity.md) 
> * [Pig 活動](data-factory-pig-activity.md)
> * [MapReduce 活動](data-factory-map-reduce.md)
> * [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
> * [Spark 活動](data-factory-spark.md)
> * [Machine Learning Batch 執行活動](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新資源活動](data-factory-azure-ml-update-resource-activity.md)
> * [預存程序活動](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活動](data-factory-usql-activity.md)
> * [.NET 自訂活動](data-factory-use-custom-activities.md)

## <a name="introduction"></a>簡介
Data Factory [管線](data-factory-create-pipelines.md)中的 HDInsight Spark 活動會在[您自己的](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) HDInsight 叢集上執行 Spark 程式。 本文是根據 [資料轉換活動](data-factory-data-transformation-activities.md) 一文，它呈現資料轉換和支援的轉換活動的一般概觀。

> [!IMPORTANT]
> 如果您不熟悉 Azure Data Factory，建議您在閱讀本文之前，先瀏覽[建置第一個管線](data-factory-build-your-first-pipeline.md)教學課程。 如需 Data Factory 服務的概觀，請參閱 [Azure Data Factory 簡介](data-factory-introduction.md)。 

## <a name="apache-spark-cluster-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Spark 叢集
首先，依照本教學課程中的指示在 Azure HDInsight 中建立 Apache Spark 叢集︰[在 Azure HDInsight 中建立 Apache Spark 叢集](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)。 

## <a name="create-data-factory"></a>建立資料處理站 
以下是使用 Spark 活動建立 Data Factory 管線的一般步驟。  

1. 建立資料處理站。
2. 建立連結服務，將 Azure HDInsight 中的 Apache Spark 叢集連結至您的資料處理站。
3. 目前，您必須指定活動的輸出資料集，即使沒有產生任何輸出。 若要建立 Azure Blob 資料集，請執行下列步驟︰
    1. 建立連結服務，將 Azure 儲存體帳戶連結至資料處理站。
    2. 建立可參考 Azure 儲存體連結服務的資料集。  
3. 使用 Spark 活動建立管線，以參考 #2 中建立的 Apache HDInsight 連結服務。 此活動已使用您在上一個步驟中建立的資料集設定為輸出資料集。 輸出資料集可以驅動排程 (每小時、每天等)。 因此，您必須指定輸出資料集，即使活動並未真的產生輸出。

如需建立資料處理站的詳細逐步指示，請參閱教學課程︰[建置第一個管線](data-factory-build-your-first-pipeline.md)。 本教學課程會使用 Hive 活動搭配 HDInsight Hadoop 叢集，但步驟類似於使用 Spark 活動搭配 HDInsight S park 叢集。   

下列各節提供 Data Factory 實體的相關資訊，以在您的資料處理站中使用 Apache Spark 叢集和 Spark 活動。   

## <a name="hdinsight-linked-service"></a>HDInsight 連結服務
在 Data Factory 管線中使用 Spark 活動之前，請先建立 HDInsight (您自己的) 連結服務。 下列 JSON 片段顯示的 HDInsight 連結服務定義，指向 Azure HDInsight Spark 叢集。   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<nameofyoursparkcluster>.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> 目前，Spark 活動不支援使用 Azure Data Lake Store 做為主要儲存體或隨選 HDInsight 連結服務的 Spark 叢集。 

如需 HDInsight 連結服務和其他計算連結服務的詳細資訊，請參閱 [Data Factory 計算連結服務](data-factory-compute-linked-services.md)一文。 

## <a name="spark-activity-json"></a>Spark 活動 JSON
以下是使用 Spark 活動之管線的 JSON 定義範例：    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

請注意下列幾點： 
- type 屬性會設為 HDInsightSpark。 
- rootPath 會設為 adfspark\\pyFiles ，其中 adfspark 是 Azure Blob 容器，而 pyFiles 是該容器中的正常資料夾。 在此範例中，Azure Blob 儲存體是與 Spark 叢集相關聯的儲存體。 您可以將檔案上傳至不同的 Azure 儲存體。 如果您這麼做，請建立 Azure 儲存體連結服務，以將該儲存體帳戶連結至資料處理站。 然後，將連結服務的名稱指定為 sparkJobLinkedService 屬性的值。 如需此屬性和 Spark 活動所支援的其他屬性詳細資訊，請參閱 [Spark 活動屬性](#spark-activity-properties)。  
- entryFilePath 會設為 test.py，這就是 python 檔案。 
- 使用 arguments 屬性可傳遞 Spark 程式的參數值。 此範例中有兩個引數：arg1 和 arg2。 
- getDebugInfo 屬性會設為 [永遠]，這表示一律會產生記錄檔 (不論成功或失敗)。 
- sparkConfig 區段指定一個 python 環境設定︰worker.memory。 
- outputs 區段有一個輸出資料集。 您必須指定輸出資料集，即使 Spark 程式不會產生任何輸出。 輸出資料集可以驅動管線的排程 (每小時、每天等)。     

本文稍後的 [Spark 活動屬性](#spark-activity-properties)一節會說明 type 屬性 (在 typeProperties 區段中)。 

如先前所述，您必須指定活動的輸出資料集，因為它可驅動管線的排程 (每小時、每天等)。 在此範例中會使用 Azure Blob 資料集。 若要建立 Azure Blob 資料集，您必須先建立 Azure 儲存體連結服務。 

以下是 Azure 儲存體連結服務和 Azure Blob 資料集的範例定義︰ 

**Azure 儲存體連結服務︰**
```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>"
        }
    }
}
```
 

**Azure blob 資料集：** 
```json
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "sparkoutput.txt",
            "folderPath": "spark/output/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

此資料集不只是虛擬資料集。 Data Factory 會使用頻率和間隔設定，並在管線的開始和結束時間內每日執行管線。 範例管線定義中的開始和結束時間只相隔一天，所以管線只會執行一次。 

## <a name="spark-activity-properties"></a>Spark 活動屬性

下表說明 JSON 定義中使用的 JSON 屬性： 

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 名稱 | 管線中的活動名稱。 | 是 |
| 說明 | 說明活動用途的文字。 | 否 |
| 類型 | 這個屬性必須設為 HDInsightSpark。 | 是 |
| 預設容器 | Spark 程式執行所在的 HDInsight 連結服務名稱。 | 是 |
| rootPath | Spark 檔案所在的 Azure Blob 容器和資料夾。 檔案名稱有區分大小寫。 | 是 |
| entryFilePath | Spark 程式碼/套件之根資料夾的相對路徑。 | 是 |
| className | 應用程式的 Java/Spark 主要類別 | 否 | 
| arguments | Spark 程式的命令列引數清單。 | 否 | 
| proxyUser | 模擬來執行 Spark 程式的使用者帳戶 | 否 | 
| sparkConfig | Spark 組態屬性。 | 否 | 
| getDebugInfo | 指定何時將 Spark 記錄檔複製到 HDInsight 叢集所使用 (或) sparkJobLinkedService 所指定的 Azure 儲存體。 允許的值︰None、Always 或 Failure。 預設值：None。 | 否 | 
| sparkJobLinkedService | 存放 Spark 作業檔案、相依性和記錄的 Azure 儲存體連結服務。  如果您未指定此屬性的值，則會使用與 HDInsight 叢集相關聯的儲存體。 | 否 |

## <a name="folder-structure"></a>資料夾結構
不同於 Pig 和 Hive 活動，Spark 活動不支援內嵌指令碼。 Spark 作業也比 Pig/Hive 作業更具擴充性。 對於 Spark 作業，您可以提供多個相依性，例如 jar 套件 (置於 java CLASSPATH)、python 檔案 (置於 PYTHONPATH) 和任何其他檔案。

在 HDInsight 連結服務所參考的 Azure Blob 儲存體中，建立下列資料夾結構。 然後，將相依檔案上傳至根資料夾中以 **entryFilePath** 表示的適當子資料夾。 比方說，將 python 檔案上傳至根資料夾的 pyFiles 子資料夾，將 jar 檔案上傳至 jars 子資料夾。 在執行階段，Data Factory 服務會預期 Azure Blob 儲存體中有下列資料夾結構︰     

| 路徑 | 說明 | 必要 | 類型 |
| ---- | ----------- | -------- | ---- | 
| 。    | Spark 作業在儲存體連結服務中的根路徑    | 是 | 資料夾 |
| &lt;使用者定義&gt; | 指向 Spark 作業輸入檔案的路徑 | 是 | 檔案 | 
| ./jars | 此資料夾下的所有檔案會上傳並放在叢集的 java 類別路徑 | 否 | 資料夾 |
| ./pyFiles | 此資料夾下的所有檔案會上傳並放在叢集的 PYTHONPATH | 否 | 資料夾 |
| ./files | 此資料夾下的所有檔案會上傳並放在執行程式工作目錄 | 否 | 資料夾 |
| ./archives | 此資料夾下的所有檔案未壓縮 | 否 | 資料夾 |
| ./logs | 此資料夾儲存來自 Spark 叢集的記錄。| 否 | 資料夾 |

以下是 HDInsight 連結服務所參考的 Azure Blob 儲存體中，含有兩個 Spark 作業檔案的儲存體範例。 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> 如需有關建立含有轉換活動之管線的完整逐步解說，請參閱[建立管線以轉換資料](data-factory-build-your-first-pipeline-using-editor.md)一文。 



## <a name="see-also"></a>另請參閱
* [Hive 活動](data-factory-hive-activity.md)
* [Pig 活動](data-factory-pig-activity.md)
* [MapReduce 活動](data-factory-map-reduce.md)
* [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
* [叫用 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


