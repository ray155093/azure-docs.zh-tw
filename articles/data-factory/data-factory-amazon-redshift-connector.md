<properties 
	pageTitle="使用 Data Factory 從 Amazon Redshift 移動資料 | Microsoft Azure" 
	description="了解如何使用 Azure Data Factory 從 Amazon Redshift 移動資料。" 
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
	ms.date="08/23/2016" 
	ms.author="spelluru"/>

# 使用 Azure Data Factory 從 Amazon Redshift 移動資料

本文將概述如何使用 Azure 資料處理站中的複製活動將資料從 Amazon Redshift 移動到另一個資料存放區。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及來源/接收資料存放區清單。

Data Factory 目前只支援將資料從 Amazon Redshift 移到其他資料存放區，而不支援將資料從其他資料存放區移到 Amazon Redshift。

## 必要條件

- 如果您要移動資料到內部部署資料存放區，請將 Amazon Redshift 叢集的存取權授與資料管理閘道 (使用機器的 IP 位址)。如需相關指示，請參閱[授權存取叢集](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)。
- 如果您要移動資料到 Azure 資料存放區，請參閱 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)以取得 Microsoft Azure 資料中心所使用的計算 IP 位址範圍 (包括 SQL 範圍)。

## 複製資料精靈
若要建立從 Amazon Redshift 複製資料的管線，最簡單的方法就是使用複製資料精靈。如需使用複製資料精靈建立管線的快速逐步解說，請參閱[教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md)。

下列範例提供您使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 來建立管線時，可使用的範例 JSON 定義。它將示範如何將資料從 Amazon Redshift 複製到「Azure Blob 儲存體」。不過，您可以將資料複製到[這裡](data-factory-data-movement-activities.md#supported-data-stores)所述的任何接收器。

## 範例：將資料從 Amazon Redshift 複製到 Azure Blob
此範例示範如何將資料從 Amazon Redshift 資料庫複製到 Azure Blob 儲存體。不過，您可以在 Azure Data Factory 中使用複製活動，**直接**將資料複製到[這裡](data-factory-data-movement-activities.md#supported-data-stores)所說的任何接收器。
 
此範例具有下列 Data Factory 實體：

- [AmazonRedshift](#linked-service-properties) 類型的連結服務。
- [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務。
- [RelationalTable](#dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
- [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
- 具有使用 [RelationalSource](#copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每個小時將資料從 Amazon Redshift 中的查詢結果複製到 Blob。範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Amazon Redshift 連結服務**

	{
	    "name": "AmazonRedshiftLinkedService",
	    "properties":
	    {
	        "type": "AmazonRedshift",
	        "typeProperties":
	        {
	            "server": "< The IP address or host name of the Amazon Redshift server >",
	            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
	            "database": "<The database name of the Amazon Redshift database>",
	            "username": "<username>",
	            "password": "<password>"
	        }
	    }
	}


**Azure 儲存體連結服務**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Amazon Redshift 輸入資料集**

設定 **"external": true** 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。在不是由管線中的活動所產生的輸入資料集上，請將此屬性設定為 true。

	{
	    "name": "AmazonRedshiftInputDataset",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "AmazonRedshiftLinkedService",
	        "typeProperties": {
	            "tableName": "<Table name>"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true
	    }
	}


**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。資料夾路徑會使用開始時間的年、月、日和小時部分。

	{
	    "name": "AzureBlobOutputDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **RelationalSource**，而 **sink** 類型設為 **BlobSink**。針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。
	
	{
	    "name": "CopyAmazonRedshiftToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AmazonRedshiftInputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutputDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "AmazonRedshiftToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## 連結服務屬性

下表提供 Amazon Redshift 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- | 
| 類型 | 類型屬性必須設為：**AmazonRedshift**。 | 是 |
| 伺服器 | Amazon Redshift 伺服器的 IP 位址或主機名稱。 | 是 |
| 連接埠 | Amazon Redshift 伺服器用來接聽用戶端連線的 TCP 連接埠號碼。 | 否，預設值︰5439 |
| 資料庫 | Amazon Redshift 資料庫的名稱。 | 是 |
| username | 可存取資料庫之使用者的名稱。| 是 |
| password | 使用者帳戶的密碼。| 是 |


## 資料集類型屬性

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。所有資料集類型 (Azure SQL、Azure Blob、Azure 資料表等) 的結構、可用性及原則等區段都相似。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。**RelationalTable** 資料集類型的 typeProperties 區段 (包含 Amazon Redshift 資料集) 具有下列屬性

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| tableName | Amazon Redshift 資料庫中連結服務所參照的資料表名稱。 | 否 (如果已指定 **RelationalSource** 的 **query**) | 

## 複製活動類型屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

另一方面，活動的 **typeProperties** 區段中可用的屬性會隨著每個活動類型而有所不同。就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

當複製活動的來源是 **RelationalSource** 類型 (包含 Amazon Redshift) 時，typeProperties 區段可使用下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| query | 使用自訂查詢來讀取資料。 | SQL 查詢字串。例如：select * from MyTable。 | 否 (如果已指定 **dataset** 的 **tableName**) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Amazon Redshift 的類型對應

如[資料移動活動](data-factory-data-movement-activities.md)一文所述，複製活動會藉由下列含有兩個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

將資料移到 Amazon Redshift 時，下列對應將用於從 Amazon Redshift 類型到 .NET 類型。

Amazon Redshift 類型 | 以 .Net 為基礎的類型
-------------------- | ---------------
SMALLINT | Int16
INTEGER | Int32
BIGINT | Int64
DECIMAL | 十進位
REAL | 單一
DOUBLE PRECISION | 兩倍
BOOLEAN | String
CHAR | String
VARCHAR | String
日期 | DateTime
時間戳記 | DateTime
TEXT | String



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## 效能和微調  
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

## 後續步驟
請參閱下列文章：
- [複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，以取得使用「複製活動」來建立管線的逐步指示。

<!---HONumber=AcomDC_0914_2016-->