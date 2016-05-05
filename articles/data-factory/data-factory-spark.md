<properties 
	pageTitle="從 Azure Data Factory 叫用 Spark 程式" 
	description="了解如何從 Azure Data Factory 使用 MapReduce 活動叫用 Spark 程式。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# 從 Data Factory 叫用 Spark 程式
## 簡介
您可以使用 Data Factory 管線的 MapReduce 活動，在 HDInsight Spark 叢集上執行 Spark 程式。如需使用活動的詳細資訊，請先參閱[從 Data Factory 叫用 MapReduce 程式](data-factory-map-reduce.md)一文。

## GitHub 上的 Spark 範例
[Spark - Data Factory sample on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) 示範如何使用 MapReduce 活動叫用 Spark 程式。Spark 程式只是將資料從一個 Azure Blob 容器複製到另一個。

## Data Factory 實體
**Spark-ADF/src/ADFJsons** 資料夾包含 Data Factory 實體的檔案 (連結的服務、資料集、管線)。

這個範例有兩個**連結服務**︰Azure 儲存體和 Azure HDInsight。您必須在 **StorageLinkedService.json** 中指定 Azure 儲存體名稱和金鑰值，在 **HDInsightLinkedService.json** 中指定 clusterUri、userName 和密碼。

這個範例中有兩個**資料集**︰**input.json** 和 **output.json**。這些檔案都位於 [資料集] 資料夾中。這些檔案代表 MapReduce 活動的輸入和輸出資料集

**ADFJsons/Pipeline** 資料夾的範例中有一個 **管線**。這是您必須檢閱的最重要實體，以了解如何使用 MapReduce 活動叫用 Spark 程式。

	{
	    "name": "SparkSubmit",
	    "properties": {
	        "description": "Submit a spark job",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "com.adf.spark.SparkJob",
	                    "jarFilePath": "libs/spark-adf-job-bin.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "--jarFile",
	                        "libs/sparkdemoapp_2.10-1.0.jar",
	                        "--jars",
	                        "/usr/hdp/current/hadoop-client/hadoop-azure-2.7.1.2.3.3.0-3039.jar,/usr/hdp/current/hadoop-client/lib/azure-storage-2.2.0.jar",
	                        "--mainClass",
	                        "com.adf.spark.demo.Demo",
	                        "--master",
	                        "yarn-cluster",
	                        "--driverMemory",
	                        "2g",
	                        "--driverExtraClasspath",
	                        "/usr/lib/hdinsight-logging/*",
	                        "--executorCores",
	                        "1",
	                        "--executorMemory",
	                        "4g",
	                        "--sparkHome",
	                        "/usr/hdp/current/spark-client",
	                        "--connectionString",
	                        "DefaultEndpointsProtocol=https;AccountName=<YOUR_ACCOUNT>;AccountKey=<YOUR_KEY>",
	                        "input=wasb://input@<YOUR_ACCOUNT>.blob.core.windows.net/data",
	                        "output=wasb://output@<YOUR_ACCOUNT>.blob.core.windows.net/results"
	                    ]
	                },
	                "inputs": [
	                    {
	                        "name": "input"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "output"
	                    }
	                ],
	                "policy": {
	                    "executionPriorityOrder": "OldestFirst",
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "Spark Launcher",
	                "description": "Submits a Spark Job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2015-11-16T00:00:01Z",
	        "end": "2015-11-16T23:59:00Z",
	        "isPaused": false,
	        "pipelineMode": "Scheduled"
	    }
	}

如您所見，MapReduce 活動設定為叫用 Azure 儲存體之 **libs** 容器中的 **spark-adf-job-bin.jar** (在 StorageLinkedService.json 中指定)。這個程式的原始程式碼位於 Spark-ADF/src/main/java/com/adf/spark 資料夾，呼叫 spark-submit 並執行 Spark 作業。

在 HDInsight Spark 叢集上執行的這個 MapReduce 程式 (spark-adf-job-bin.jar) 會叫用 Spark 程式 **sparkdemoapp\_2.10-1.0.jar**，並透過 MapReduce 活動將收到的引數傳遞給 Spark 程式 (如前文的 JSON 所示)。**sparkdemoapp\_2.10-1.0.jar** 包含將資料從一個 Azure blob 容器複製到另一個 Azure Blob 容器的 Scala 原始程式碼。您可以使用包含任何作業的任何其他 Jar 取代這個示範應用程式的 Jar，這個作業是您嘗試用 Spark 執行的任何作業。

簡言之，**MapReduce 活動**會叫用 MapReduce 程式 **spark-adf-job-bin.jar**，而後者叫用 Spark 程式 **sparkdemoapp\_2.10-1.0.jar**。若要執行您自己的 Spark 程式，請以您自己的程式取代 sparkdemoapp\_2.10-1.0.jar。

> [AZURE.NOTE] 您必須以這個方法使用您自己的 HDInsight Spark 叢集，使用 MapReduce 活動叫用 Spark 程式。不支援使用隨選 HDInsight 叢集。


## 另請參閱
- [Hive 活動](data-factory-hive-activity.md)
- [Pig 活動](data-factory-pig-activity.md)
- [MapReduce 活動](data-factory-map-reduce.md)
- [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
- [叫用 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0420_2016-->