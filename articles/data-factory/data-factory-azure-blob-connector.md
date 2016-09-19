<properties 
	pageTitle="在 Azure Blob 儲存體來回複製資料 | Azure Data Factory" 
	description="了解如何在 Azure Data Factory 複製 Blob 資料。使用我們的範例：如何在 Azure Blob 儲存體和 Azure SQL Database 將資料複製進來和複製出去。" 
    keywords="blob 資料, azure blob 複製"
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
	ms.date="08/25/2016" 
	ms.author="spelluru"/>

# 使用 Azure Data Factory 從 Azure Blob 來回移動資料
本文說明如何使用 Azure Data Factory 中的複製活動，透過從其他資料存放區獲得 Blob 資料在 Azure Blob 中來回移動資料。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

> [AZURE.NOTE]
複製活動支援在一般用途的 Azure 儲存體帳戶以及經常性存取/非經常性存取 Blob 儲存體來回複製資料。
> 
> 此活動支援從區塊、附加或分頁 Blob 讀取，但只支援寫入至區塊 Blob。

## 複製資料精靈
要建立將資料複製到 Azure Blob 儲存體，或複製 Azure Blob 儲存體資料的管線，最簡單的方法是使用複製資料精靈。如需使用複製資料精靈建立管線的快速逐步解說，請參閱[教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md)。

以下範例提供可用來使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 建立管線的範例 JSON 定義。它們會示範如何將資料複製到 Azure Blob 儲存體和 Azure SQL Database，以及複製其中的資料。不過，您可以在 Azure Data Factory 中使用複製活動，從任何來源**直接**將資料複製到[這裡](data-factory-data-movement-activities.md#supported-data-stores)所說的任何接收器。

## 範例：從 Azure Blob 複製資料到 Azure SQL
 

下列範例顯示︰

1.	[AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 類型的連結服務。
2.	[AzureStorage](#azure-storage-linked-service-properties) 類型的連結服務。
3.	[AzureBlob](#azure-blob-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.	[AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4.	具有使用 [BlobSource](#azure-blob-copy-activity-type-properties) 和 [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將時間序列資料從 Azure Blob 複製到 Azure SQL 資料表。範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure SQL 連結服務：**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure 儲存體連結服務：**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Azure Data Factory 支援兩種類型的 Azure 儲存體連結服務：**AzureStorage** 和 **AzureStorageSas**。對於前者指定連接字串，包含帳戶金鑰，對於後者指定共用存取簽章 (SAS) Uri。請參閱[連結服務](#linked-services)章節以取得詳細資料。

**Azure Blob 輸入資料集：**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。資料夾路徑使用開始時間的年、月、日部分，而檔案名稱使用開始時間的小時部分。“external”: “true” 設定會通知 Data Factory：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
	      "fileName": "{Hour}.csv",
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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure SQL 輸出資料集：**

此範例會將資料複製到 Azure SQL Database 中名為 "MyTable" 的資料表。請在Azure SQL Database 中建立此資料表，其資料行的數目如您預期 Blob CSV 檔案要包含的數目。此資料表會每小時加入新的資料列。

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **BlobSource**，而 **sink** 類型設為 **SqlSink**。

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## 範例：從 Azure SQL 複製資料到 Azure Blob
下列範例顯示︰

1.	[AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 類型的連結服務。
2.	[AzureStorage](#azure-storage-linked-service-properties) 類型的連結服務。
3.	[AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.	[AzureBlob](#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4.	具有使用 [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) 和 [BlobSink](#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。


此範例會每小時將時間序列資料從 Azure SQL 資料表複製到 Azure Blob。範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure SQL 連結服務：**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure 儲存體連結服務：**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Azure Data Factory 支援兩種類型的 Azure 儲存體連結服務：**AzureStorage** 和 **AzureStorageSas**。對於前者指定連接字串，包含帳戶金鑰，對於後者指定共用存取簽章 (SAS) Uri。請參閱[連結服務](#linked-services)章節以取得詳細資料。


**Azure SQL 輸入資料集：**

此範例假設您已在 SQL Azure 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。

設定 “external”: ”true” 會通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}


**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。資料夾路徑會使用開始時間的年、月、日和小時部分。

	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **SqlSource**，而 **sink** 類型設為 **BlobSink**。針對 **SqlReaderQuery** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureSQLtoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
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
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "SqlSource",
			            	"SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
		        	},
		       		"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
		      	}
		     ]
		}
	}

## 連結的服務
您可以使用兩種類型的連結服務，將 Azure Blob 儲存體連結至 Azure Data Factory。它們是：**AzureStorage** 連結服務和 **AzureStorageSas** 連結服務。Azure 儲存體連結服務可將 Azure 儲存體的全域存取權提供給 Data Factory。而 Azure 儲存體 SAS (共用存取簽章) 連結服務會將 Azure 儲存體的受限制/時間繫結存取權提供給 Data Factory。這兩個連結服務之間沒有其他差異。選擇符合您需求的連結服務。以下章節提供這兩個連結服務的詳細資料。

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## Azure Blob 資料集類型屬性

如需定義資料集的 JSON 區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **TypeProperties** 區段都不同，可提供資料存放區中資料的位置、格式等相關資訊。**AzureBlob** 類型資料集的 typeProperties 區段具有下列屬性。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- | 
| folderPath | Blob 儲存體中容器和資料夾的路徑。範例：myblobcontainer\\myblobfolder\\ | 是 |
| fileName | Blob 的名稱。fileName 是選填項目且區分大小寫。<br/><br/>如果您指定 fileName，則活動 (包括複製活動) 適用於特定的 Blob。<br/><br/>如果未指定 fileName，則複製活動將會包含輸入資料集 folderPath 中的所有 Blob。<br/><br/>如果未指定輸出資料集的 fileName，則所產生檔案的名稱會採用下列格式：Data.<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | 否 |
| partitionedBy | partitionedBy 是選擇性的屬性。您可以用來指定時間序列資料的動態 folderPath 和 filename。例如，folderPath 可針對每小時的資料進行參數化。如需詳細資訊和範例，請參閱[使用 partitionedBy 屬性](#using-partitionedBy-property)一節。 | 否
| format | 支援下列格式類型：**TextFormat**、**AvroFormat**、**JsonFormat** 及 **OrcFormat**。將格式下的 **type** 屬性設定為這些值其中之一。如需詳細資料，請參閱[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat) 及[指定 OrcFormat](#specifying-orcformat) 各節。如果您想要在以檔案為基礎的存放區之間依原樣複製檔案 (二進位複本)，您可以在輸入和輸出資料集定義中略過格式區段。| 否
| compression | 指定此資料的壓縮類型和層級。支援的類型為：**GZip**、**Deflate** 及 **BZip2**，而支援的層級為：**最佳**和**最快**。**AvroFormat** 或 **OrcFormat** 格式的資料目前不支援壓縮設定。如需詳細資訊，請參閱[壓縮支援](#compression-support)一節。 | 否 |

### 使用 partitionedBy 屬性
如上一節所述，您可以使用 **partitionedBy** 區段、Data Factory 巨集和系統變數 (SliceStart 和 SliceEnd，表示指定資料配量的開始和結束時間)，指定時間序列資料的動態 folderPath 和 filename。

請參閱 [Data Factory 系統變數](data-factory-scheduling-and-execution.md#data-factory-system-variables)和 [Data Factory 函式參考](data-factory-scheduling-and-execution.md#data-factory-functions-reference)，以了解您可以在 partitionedBy 區段中使用的 Data Factory 系統變數和函式。

請參閱[建立資料集](data-factory-create-datasets.md)和[排程和執行](data-factory-scheduling-and-execution.md)等文章，以了解時間序列資料集、排程和配量的詳細資訊。

#### 範例 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

在此範例中，{Slice} 會取代成 Data Factory 系統變數 SliceStart 的值 (使用指定的格式 (YYYYMMDDHH))。SliceStart 是指配量的開始時間。每個配量的 folderPath 都不同。例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104

#### 範例 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

在此範例中，SliceStart 的年、月、日和時間會擷取到 folderPath 和 fileName 屬性所使用的個別變數。

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]  
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## Azure Blob 複製活動類型屬性  
如需可用來定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。屬性 (例如名稱、描述、輸入和輸出資料集，以及原則) 適用於所有類型的活動。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。若為複製活動，這些屬性會根據來源和接收的類型而有所不同

**BlobSource** 在 **typeProperties** 區段中支援下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- | 
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 | True (預設值)、False | 否 | 

**BlobSink** 在 **typeProperties** 區段中支援下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | 當來源為 BlobSource 或 FileSystem 時，定義複製行為。 | **PreserveHierarchy：**在目標資料夾中保留檔案階層架構。來源檔案至來源資料夾的相對路徑，與目標檔案至目標資料夾的相對路徑完全相同。<br/><br/>**FlattenHierarchy：**來源資料夾的所有檔案會位於目標資料夾的第一層。目標檔案會有自動產生的名稱。<br/><br/>**MergeFiles：(預設值)** 將來源資料夾的所有檔案合併為一個檔案。如果已指定檔案/Blob 名稱，合併檔案名稱會是指定的名稱；否則，就會是自動產生的檔案名稱。 | 否 |

**BlobSource** 也支援這兩個屬性以提供回溯相容性。

- **treatEmptyAsNull**：指定是否將 null 或空字串視為 null 值。
- **skipHeaderLineCount** - 指定需略過多少行。僅適用於輸入資料集使用 TextFormat 時。

同樣地，**BlobSink** 支援下列屬性以提供回溯相容性。

- **blobWriterAddHeader**︰指定是否要在寫入至輸出資料集時新增資料行定義的標頭。

資料集現在支援下列會實作相同功能的屬性︰**treatEmptyAsNull**、**skipLineCount**、**firstRowAsHeader**。

下表提供有關使用新的資料集屬性來取代這些 Blob 來源/接收屬性的指引。

| 複製活動屬性 | 資料集屬性 |
| :---------------------- | :---------------- | 
| BlobSource 上的 skipHeaderLineCount | skipLineCount 和 firstRowAsHeader。會先略過行，然後讀取第一個資料列做為標頭。 |
| BlobSource 上的 treatEmptyAsNull | 輸入資料集上的 treatEmptyAsNull |
| BlobSink 上的 blobWriterAddHeader | 輸出資料集上的 firstRowAsHeader | 

如需這些屬性的詳細資訊，請參閱[指定 TextFormat](#specifying-textformat) 一節。

### 遞迴和 copyBehavior 範例
本節說明遞迴和 copyBehavior 值在不同組合的情況下，複製作業所產生的行為。

遞迴 | copyBehavior | 產生的行為
--------- | ------------ | --------
true | preserveHierarchy | 對於有下列結構的來源資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>目標資料夾 Folder1 是使用和來源資料夾相同的結構所建立<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。  
true | flattenHierarchy | 對於有下列結構的來源資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>目標 Folder1 是使用下列結構所建立：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&File1 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 有自動產生的名稱
true | mergeFiles | 對於有下列結構的來源資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>目標 Folder1 是使用下列結構所建立：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的內容會合併成一個有自動產生之檔案名稱的檔案
false | preserveHierarchy | 對於有下列結構的來源資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>目標資料夾 Folder1 是使用下列結構所建立<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。
false | flattenHierarchy | 對於有下列結構的來源資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>目標資料夾 Folder1 是使用下列結構所建立<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 有自動產生的名稱<br/><br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。
false | mergeFiles | 對於有下列結構的來源資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>目標資料夾 Folder1 是使用下列結構所建立<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個有自動產生之檔案名稱的檔案。File1 有自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。

  


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## 效能和微調  
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

<!----HONumber=AcomDC_0907_2016-->