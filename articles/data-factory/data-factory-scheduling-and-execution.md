<properties
	pageTitle="使用 Data Factory 進行排程和執行 | Microsoft Azure"
	description="了解 Azure Data Factory 應用程式模型的排程和執行層面。"
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
	ms.date="08/22/2016"
	ms.author="spelluru"/>

# Data Factory 排程和執行

本文說明 Azure Data Factory 應用程式模型的排程和執行層面。本文是根據[建立管線](data-factory-create-pipelines.md)和[建立資料集](data-factory-create-datasets.md)所建置，並且假設您已了解 Data Factory 應用程式模型的基本概念，包括活動、管線、連結的服務和資料集。

## 排程活動

使用活動 JSON 的 [排程器] 區段，您可以指定活動的週期性排程。例如，您可以排程每小時進行一次的活動，如下所示：

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  

![排程器範例](./media/data-factory-scheduling-and-execution/scheduler-example.png)

如圖中所示，為活動指定排程將會建立一系列輪轉視窗。輪轉視窗是一系列的固定大小、非重疊的連續時間間隔。活動的這些邏輯輪轉視窗稱為*活動視窗*。

針對目前正在執行的活動時段，只要使用活動 JSON 中的 [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) 和 [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) 系統變數，即可存取與該活動時段關聯的時間間隔。您可以在活動 JSON 中針對不同用途使用這些變數。例如，您可以使用它們從代表時間序列資料的輸入和輸出資料集選取資料。

在資料集中，**scheduler** 屬性支援的子屬性與 **availability** 屬性所支援的相同。請參閱[資料集可用性](data-factory-create-datasets.md#Availability)以取得詳細資料。範例︰在特定時間位移排程，將模式設定為在活動時間間隔開始或結束時對齊處理。

您可以為活動指定排程器屬性，但此為選擇性動作。如果您指定屬性，它就必須符合您在輸出資料集定義中指定的頻率。目前，輸出資料集會影響排程，因此即使活動並未產生任何輸出，您都必須建立輸出資料集。如果活動沒有任何輸入，您可以略過建立輸入資料集。

## 時間序列資料集和資料配量

時間序列資料是連續順序的資料點，通常包含一段時間間隔的後續度量。時間序列資料的常見範例包括感應器資料和應用程式遙測資料。

使用 Data Factory，您可以批次方式使用活動執行處理時間序列資料。通常有週期性節奏，在該節奏下，輸入資料抵達以及產生需要的輸出資料。這個節奏是由指定資料集的 [可用性] 而模式化，如下所示：

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

活動執行取用和產生的每個資料單位稱為資料配量。下圖顯示具有一個輸入資料集和一個輸出資料集的活動範例。這些資料集的**可用性**頻率是設定為每小時。

![可用性排程器](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

上圖顯示輸入和輸出資料集的每小時資料配量。圖中顯示 3 個已經可供處理的輸入配量。10-11 AM 活動正在進行中，會產生 10-11 AM 輸出配量。

您可以使用 [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) 和 [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) 變數存取與資料集 JSON 中產生之目前配量相關聯的時間間隔。

目前 Data Factory 需要活動中指定的排程與輸出資料集之**可用性**中指定的排程完全相符。因此，**WindowStart**、**WindowEnd**、**SliceStart** 和 **SliceEnd** 一律對應到相同的時間期間和單一輸出配量。

如需適用於可用性區段之不同屬性的詳細資訊，請參閱[建立資料集](data-factory-create-datasets.md)。

## 使用複製活動將資料從 Azure SQL Database 移至 Azure Blob 儲存體

讓我們整理並且實際操作一下，方法是建立管線以便每小時將資料從 Azure SQL Database 資料表複製到 Azure Blob 儲存體。

**輸入：Azure SQL Database 資料集**

	{
	    "name": "AzureSqlInput",
	    "properties": {
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}


在 [可用性] 區段中，[頻率] 設為 [小時]，[間隔] 設為 [1]。

**輸出：Azure Blob 儲存體資料集**

	{
	    "name": "AzureBlobOutput",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
	            "format": {
	                "type": "TextFormat"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


在 [可用性] 區段中，[頻率] 設為 [小時]，[間隔] 設為 [1]。



**活動：複製活動**

	{
	    "name": "SamplePipeline",
	    "properties": {
	        "description": "copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "name": "AzureSQLtoBlob",
	                "description": "copy activity",
	                "typeProperties": {
	                    "source": {
	                        "type": "SqlSource",
	                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 100000,
	                        "writeBatchTimeout": "00:05:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AzureSQLInput"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutput"
	                    }
	                ],
	       			"scheduler": {
	          			"frequency": "Hour",
	          			"interval": 1
	        		}
	            }
	        ],
	        "start": "2015-01-01T08:00:00Z",
	        "end": "2015-01-01T11:00:00Z"
	    }
	}


範例顯示設為每小時頻率的活動排程和資料集可用性區段。此範例顯示如何使用 **WindowStart** 和 **WindowEnd** 選取活動執行的相關資料，並將它複製到具有適當 **folderPath** 的 Blob。**folderPath** 會參數化為讓每小時具有個別的資料夾。

當上午 8–11 點之間有 3 個配量執行，Azure SQL Database 中的資料如下︰

![範例輸入](./media/data-factory-scheduling-and-execution/sample-input-data.png)

在管線部署之後，Azure Blob 會填入如下資料：

-	具有資料的檔案 mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt

			10002345,334,2,2015-01-01 08:24:00.3130000
			10002345,347,15,2015-01-01 08:24:00.6570000
			10991568,2,7,2015-01-01 08:56:34.5300000

	> [AZURE.NOTE] &lt;Guid&gt; 會取代為實際的 GUID。範例檔案名稱：Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
-	具有資料的檔案 mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt：

			10002345,334,1,2015-01-01 09:13:00.3900000
			24379245,569,23,2015-01-01 09:25:00.3130000
			16777799,21,115,2015-01-01 09:47:34.3130000
-	沒有資料的檔案 mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt。


## 建立資料配量、設定管線的作用期間並同時執行配量

[建立管線](data-factory-create-pipelines.md)導入藉由設定 **start** 和 **end** 屬性來指定管線的使用中週期的概念。

您可以設定過去日期的管線使用中週期的開始日期。Data Factory 會自動計算 (回補) 過去的所有資料配量，並且開始處理。

您可以設定讓回補的資料配量以平行方式執行。您可以在活動 JSON 的 policy 區段中設定 **concurrency** 屬性來執行此作業，如[建立管線](data-factory-create-pipelines.md)所示。

## 重新執行失敗的資料配量並自動追蹤資料相依性

您可以使用豐富的視覺化方式監視配量的執行。如需詳細資訊，請參閱[使用 Azure 入口網站刀鋒視窗監視和管理管線](data-factory-monitor-manage-pipelines.md)或[監視和管理應用程式](data-factory-monitor-manage-app.md)。

請思考一下會顯示兩個活動的下列範例。Activity1 會產生時間序列資料集，具有由 Activity2 做為輸入取用的輸出配量，以產生最終輸出時間序列資料集。

![失敗的配量](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

圖中顯示 3 個最近的配量當中有失敗，針對 Dataset2 產生 9-10 AM 配量。Data Factory 會自動追蹤時間序列資料集的相依性。因此，它不會開始 9-10 AM 下游配量的活動執行。


Data Factory 監視和管理工具可讓您深入診斷記錄以了解失敗的配量，輕鬆地找出問題的根本原因並加以修正。在您修正問題之後，即可輕易地開始活動執行以產生失敗的配量。如需有關如何重新執行和了解資料配量的狀態轉換的其他詳細資料，請參閱[使用 Azure 入口網站刀鋒視窗監視和管理管線](data-factory-monitor-manage-pipelines.md)或[監視和管理應用程式](data-factory-monitor-manage-app.md)。

在您重新執行 **Dataset2** 的 9-10 AM 配量之後，Data Factory 會開始執行最終資料集上 9-10 AM 相依的配量。

![重新執行失敗的配量](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## 依序執行活動
您可以將一個活動的輸出資料集設為另一個活動的輸入資料集，藉此鏈結兩個活動 (讓一個活動接著另一個活動執行)。活動可以在相同的管線中或在不同的管線中。只有當第一個活動執行成功完成時，第二個活動才會執行。

例如，請考慮下列情況：

1.	管線 P1 具有需要外部輸入資料集 D1 的活動 A1，並且會產生輸出資料集 D2。
2.	管線 P2 具有需要來自資料集 D2 之輸入的活動 A2，並且會產生輸出資料集 D3。

在此案例中，活動 A1 和 A2 是在不同的管線中。活動 A1 會在有外部資料可供使用且達到排定的可用性頻率時執行。活動 A2 會在來自 D2 的排定分割可供使用且達到排定的可用性頻率時執行。如果資料集 D2 中的其中一個分割發生錯誤，則不會針對該分割執行 A2，直到該分割可供使用為止。

[圖表] 檢視看起來如下圖：

![兩個管線中的鏈結活動](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

如先前所述，活動可以在相同的管線中。兩個活動同時在相同管線中的 [圖表] 檢視看起來如下圖：

![相同管線中的鏈結活動](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### 循序複製
您可以利用循序/排序的方式，逐一執行多個複製作業。例如，您在管線中可能有兩個具有下列輸入資料輸出資料集的複製活動 (CopyActivity1 和 CopyActivity2)。

CopyActivity1

輸入：Dataset1。輸出：Dataset2。

CopyActivity2

輸入：Dataset2。輸出：Dataset3。

唯有當 CopyActivity1 成功執行且 Dataset2 可供使用時，CopyActivity2 才會執行。

以下是範例管線 JSON：

	{
		"name": "ChainActivities",
	    "properties": {
			"description": "Run activities in sequence",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "copyBehavior": "PreserveHierarchy",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset1"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob1ToBlob2",
	                "description": "Copy data from a blob to another"
	            },
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset3"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob2ToBlob3",
	                "description": "Copy data from a blob to another"
	            }
	        ],
	        "start": "2016-08-25T01:00:00Z",
	        "end": "2016-08-25T01:00:00Z",
	        "isPaused": false
	    }
	}

請注意，在此範例中，是將第一個複製活動的輸出資料集 (Dataset2) 指定為第二個活動的輸入。因此，只有當來自第一個活動的輸出資料集準備就緒時，第二個活動才會執行。

在範例中，CopyActivity2 可以有不同的輸入 (例如 Dataset3)，但是您必須指定 Dataset2 做為 CopyActivity2 的輸入，因此在 CopyActivity1 完成之前，活動不會執行。例如：

CopyActivity1

輸入︰Dataset1。輸出：Dataset2。

CopyActivity2

輸入︰Dataset3、Dataset2。輸出︰Dataset4。

	{
		"name": "ChainActivities",
	    "properties": {
			"description": "Run activities in sequence",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "copyBehavior": "PreserveHierarchy",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset1"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlobToBlob",
	                "description": "Copy data from a blob to another"
	            },
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset3"
	                    },
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset4"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob3ToBlob4",
	                "description": "Copy data from a blob to another"
	            }
	        ],
	        "start": "2017-04-25T01:00:00Z",
	        "end": "2017-04-25T01:00:00Z",
	        "isPaused": false
	    }
	}


請注意，在此範例中，為第二個複製活動指定了兩個輸入資料集。指定多個輸入時，只有第一個輸入資料集會用來複製資料，但是其他資料集會用來做為相依性。CopyActivity2 只會在符合下列條件後才開始︰

- CopyActivity1 已順利完成且 Dataset2 可供使用。將資料複製到 Dataset4 時，不會使用此資料集。它只會用來做為 CopyActivity2 的排程相依性。
- Dataset3 可供使用。此資料集代表已複製到目的地的資料。



## 使用不同的頻率模型化資料集

在範例中，輸入和輸出資料集和活動排程時段的頻率是相同的。某些案例需要能夠以不同於一或多個輸入的頻率產生輸出。Data Factory 支援模型化這些案例。

### 範例 1：對每小時可用的輸入資料產生每日輸出報告

請設想 Azure Blob 儲存體中每小時會有來自感應器的輸入測量資料的案例。您想要為具有 [Data Factory Hive 活動](data-factory-hive-activity.md)的日子，產生具有統計資料 (例如平均值、最大值及最小值) 的每日彙總報告。

以下是使用 Data Factory 模型化此案例的方式：

**輸入資料集**

每小時輸入檔案會針對指定日期在資料夾中卸除。輸入的可用性設定為**小時** (頻率：小時、間隔：1)。

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**輸出資料集**

每天會在當天的資料夾中建立一個輸出檔。輸出的可用性設定為**日** (頻率：日、間隔：1)。


	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**活動：管線中的 Hive 活動**

Hive 指令碼會收到適當的「日期時間」資訊，做為使用 **WindowStart** 變數的參數，如下列程式碼片段所示。Hive 指令碼會使用此變數將資料從正確的資料夾載入，並執行彙總來產生輸出。

		{  
		    "name":"SamplePipeline",
		    "properties":{  
		    "start":"2015-01-01T08:00:00",
		    "end":"2015-01-01T11:00:00",
		    "description":"hive activity",
		    "activities": [
		        {
		            "name": "SampleHiveActivity",
		            "inputs": [
		                {
		                    "name": "AzureBlobInput"
		                }
		            ],
		            "outputs": [
		                {
		                    "name": "AzureBlobOutput"
		                }
		            ],
		            "linkedServiceName": "HDInsightLinkedService",
		            "type": "HDInsightHive",
		            "typeProperties": {
		                "scriptPath": "adftutorial\\hivequery.hql",
		                "scriptLinkedService": "StorageLinkedService",
		                "defines": {
		                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
		                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
		                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
		                }
		            },
		            "scheduler": {
		                "frequency": "Day",
		                "interval": 1
		            },			
		            "policy": {
		                "concurrency": 1,
		                "executionPriorityOrder": "OldestFirst",
		                "retry": 2,
		                "timeout": "01:00:00"
		            }
	             }
		     ]
		   }
		}

下圖顯示從資料相依性觀點來看的案例。

![資料相依性](./media/data-factory-scheduling-and-execution/data-dependency.png)

每一天的輸出配量取決於輸入資料集之 24 小時每小時的配量。Data Factory 會自動計算這些相依性，方法是釐清落在與要產生之輸出配量相同時間期間的輸入資料配量。如果這 24 個輸入配量中有任何一個無法使用，Data Factory 會先等待輸入配量就緒，再開始每日活動執行。


### 範例 2：使用運算式和 Data Factory 函數指定相依性

我們來看一下另一種案例。假設您有處理兩個輸入資料集的 Hive 活動。其中一個每日有新資料，但是另一個會每週取得新資料。假設您想要跨兩個輸入進行聯結作業，並且每日產生輸出。

Data Factory 會藉由對齊輸出資料配量的時間期間來自動找出要處理的正確輸入配量的簡單方法不會奏效。

您必須指定對於每個活動執行，Data Factory 應該針對每週輸入資料集使用上一週的資料配量。您可以透過 Azure Data Factory 函數的協助執行此作業，如下列程式碼片段所示。

**Input1：Azure Blob**

第一個輸入是將會每日更新的 Azure Blob。

	{
	  "name": "AzureBlobInputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Input2：Azure Blob**

Input2 是將會每週更新的 Azure Blob。

	{
	  "name": "AzureBlobInputWeekly",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 7
	    }
	  }
	}

**輸出：Azure Blob**

每天會在資料夾中建立一個當天的輸出檔。輸出的可用性設定為**日** (頻率：日、間隔：1)。

	{
	  "name": "AzureBlobOutputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**活動：管線中的 Hive 活動**

Hive 活動接受 2 個輸入，並且每日產生輸出配量。您可以針對每週輸入指定每日的輸出配量是根據上一週的輸入配量，如下所示。

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"hive activity",
	    "activities": [
	      {
	        "name": "SampleHiveActivity",
	        "inputs": [
	          {
	            "name": "AzureBlobInputDaily"
	          },
	          {
	            "name": "AzureBlobInputWeekly",
	            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
	            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutputDaily"
	          }
	        ],
	        "linkedServiceName": "HDInsightLinkedService",
	        "type": "HDInsightHive",
	        "typeProperties": {
	          "scriptPath": "adftutorial\\hivequery.hql",
	          "scriptLinkedService": "StorageLinkedService",
	          "defines": {
	            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
	            "Month": "$$Text.Format('{0:%M}',WindowStart)",
	            "Day": "$$Text.Format('{0:%d}',WindowStart)"
	          }
	        },
	        "scheduler": {
	          "frequency": "Day",
	          "interval": 1
	        },			
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 2,  
	          "timeout": "01:00:00"
	        }
		   }
	     ]
	   }
	}


## Data Factory 函式與系統變數   

如需 Data Factory 所支援的函數與系統變數清單，請參閱 [Data Factory 函式與系統變數](data-factory-functions-variables.md)。

## 資料相依性的深入探討

若要依據活動執行來產生資料集配量，Data Factory 會使用下列*相依性模型*來判斷活動取用的資料集與活動產生的資料集之間的關聯性。

需要輸入資料集的時間範圍以產生稱為「相依性期間」的輸出資料集配量。

活動執行只會在相依性期間內輸入資料集的資料配量可用時產生資料集配量。這表示包含相依性期間的所有輸入配量必須具有**就緒**狀態，活動執行才會產生輸出資料集配量。

若要產生資料集配量 [**start**, **end**]，函數必須將資料集配量對應至其相依性期間。此函式基本上是將資料集配量的開始和結束轉換為相依性期間的開始和結束的公式。更正式的說法：

	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

**F** 和 **g** 是對應函數，計算每個活動輸入的相依性期間的開始和結束。

如範例所示，相依性期間與產生資料配量的期間是相同的。在這些情況下，Data Factory 會自動計算落在相依性期間內的輸入配量。

例如：在彙總範例中，其中輸出是每日產生，而輸入資料每小時可用，資料配量期間為 24 小時。Data Factory 會針對此時間期間尋找相關的每小時輸入配量，讓輸出配量相依於輸入配量。

您也可以針對相依性期間提供自己的對應，如範例所示，當中的其中一個輸入是每週，而輸出配量是每日產生。

## 資料相依性和驗證

資料集可以具有定義的驗證原則，指定配量執行所產生的資料在供取用之前如何驗證。如需詳細資訊，請參閱[建立資料集](data-factory-create-datasets.md)。

在這種情況下，於配量完成執行後，輸出配量狀態就會變更為**等候**，子狀態為**驗證**。配量通過驗證之後，配量狀態會變更為**就緒**。

如果已產生資料配量但是未通過驗證，將不會處理相依於驗證失敗的配量的下游配量活動執行。

[監視和管理管線](data-factory-monitor-manage-pipelines.md)涵蓋 Data Factory 中資料配量的各種狀態。

## 外部資料

資料集可以標示為外部 (如下列 JSON 程式碼片段所示)，意味著它尚未使用 Data Factory 產生。在這種情況下，資料集原則可以包含一組額外的參數，描述資料集的驗證和重試原則。如需所有屬性的描述，請參閱[建立管線](data-factory-create-pipelines.md)。

類似於 Data Factory 產生的資料集，在可以處理相依配量之前，外部資料的資料配量必須是就緒狀態。

	{
		"name": "AzureSqlInput",
		"properties":
		{
			"type": "AzureSqlTable",
			"linkedServiceName": "AzureSqlLinkedService",
			"typeProperties":
			{
				"tableName": "MyTable"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1     
			},
			"external": true,
			"policy":
			{
				"externalData":
				{
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}  
		}
	}


## Onetime 管線
您可以建立和排程管線，以在管線定義中指定的開始和結束時間內定期執行 (例如：每小時或每日)。如需詳細資訊，請參閱[排程活動](#scheduling-and-execution)。您也可以建立只執行一次的管線。若要這樣做，您需將管線定義中的 **pipelineMode** 屬性設定為 **onetime** (如下列 JSON 範例所示)。這個屬性的預設值是 **scheduled**。

	{
	    "name": "CopyPipeline",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset"
	                    }
	                ]
	                "name": "CopyActivity-0"
	            }
	        ]
	        "pipelineMode": "OneTime"
	    }
	}

請注意：

- 未指定管線的**開始**和**結束**時間。
- 已指定輸入和輸出資料集的**可用性** (**頻率**和**間隔**)，即使 Data Factory 未使用這些值也是一樣。
- 圖表檢視不會顯示一次性管線。這是設計的行為。
- 一次性管線無法更新。您可以複製一次性管線、將其重新命名、更新屬性，以及加以部署來建立另一個管線。

<!---HONumber=AcomDC_0907_2016-->