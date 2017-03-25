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
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2af5d275bb331101b370e4a12043e27b6cdf5b68
ms.lasthandoff: 03/15/2017


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

## <a name="hdinsight-linked-service"></a>HDInsight 連結服務
在 Data Factory 管線中使用 Spark 活動之前，請先建立 HDInsight (您自己的) 連結服務。 下列 JSON 片段顯示的 HDInsight 連結服務定義，指向您自己的 Azure HDInsight Spark 叢集。   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> 目前，Spark 活動不支援使用 Data Lake Store 做為主要儲存體或隨選 HDInsight 連結服務的 Spark 叢集。 

如需 HDInsight 連結服務和其他計算連結服務的詳細資訊，請參閱 [Data Factory 計算連結服務](data-factory-compute-linked-services.md)一文。 

## <a name="spark-activity-json"></a>Spark 活動 JSON
以下是 Spark 活動的 JSON 定義範例：    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
下表說明 JSON 定義中使用的 JSON 屬性： 

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 名稱 | 管線中的活動名稱。 | 是 |
| 說明 | 說明活動用途的文字。 | 否 |
| 類型 | 這個屬性必須設為 HDInsightSpark。 | 是 |
| 預設容器 | 參考 Spark 程式執行所在的 HDInsight 連結服務。 | 是 |
| rootPath | Spark 檔案所在的 Azure Blob 容器和資料夾。 檔案名稱有區分大小寫。 | 是 |
| entryFilePath | Spark 程式碼/套件之根資料夾的相對路徑 | 是 |
| className | 應用程式的 Java/Spark 主要類別 | 否 | 
| arguments | Spark 程式的命令列引數清單。 | 否 | 
| proxyUser | 模擬來執行 Spark 程式的使用者帳戶 | 否 | 
| sparkConfig | Spark 組態屬性 | 否 | 
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

## <a name="spark-sample-on-github"></a>GitHub 上的 Spark 範例
在支援 Spark 活動之前，從 Data Factory 管線執行 Spark 程式的解決辦法是使用 MapReduce 活動。 您仍然可以在 Data Factory 管線中使用 [MapReduce 活動](data-factory-map-reduce.md)，以便在 HDInsight Spark 叢集上執行 Spark 程式。 我們建議您使用 Spark 活動，而不要使用 MapReduce 活動。 

[Spark - Data Factory sample on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) 示範如何使用 MapReduce 活動叫用 Spark 程式。 Spark 程式只是將資料從一個 Azure Blob 容器複製到另一個。 

## <a name="see-also"></a>另請參閱
* [Hive 活動](data-factory-hive-activity.md)
* [Pig 活動](data-factory-pig-activity.md)
* [MapReduce 活動](data-factory-map-reduce.md)
* [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
* [叫用 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


