---
title: "使用 Azure Data Factory 從 SAP Hana 移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從 SAP Hana 移動資料。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 415e91ca2f1e47094fc8b64569b5cc29a706999a
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP Hana 移動資料
本文將概述如何使用 Azure 資料處理站管線中的複製活動將資料從 SAP Hana 移動到另一個資料存放區。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。 資料處理站目前只支援將資料從 SAP Hana 移動到其他資料存放區，而不支援將資料從其他資料存放區移動到 SAP Hana。

## <a name="supported-versions-and-installation"></a>支援的版本和安裝
此連接器支援任何版本的 SAP HANA 資料庫。 它支援從 HANA 資訊模型 (例如分析和計算檢視) 及使用 SQL 查詢資料列/資料行資料表複製資料。

若要啟用 SAP HANA 執行個體的連線，請安裝下列元件︰
- **資料管理閘道器**：資料處理站服務支援使用稱為資料管理閘道器的元件連接到內部部署資料存放區 (包括 SAP HANA)。 若要了解資料管理閘道和設定閘道的逐步指示，請參閱[在內部部署資料存放區和雲端資料存放區之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文。 即使 SAP Hana 裝載於 Azure IaaS 虛擬機器 (VM) 中，也必須要有閘道。 您可以將閘道安裝在與資料存放區相同或相異的 VM 上，只要閘道可以連線到資料庫即可。
- 閘道器電腦上的 **SAP HANA ODBC 驅動程式**。 您可以從 [SAP 軟體下載中心](https://support.sap.com/swdc)下載 SAP Hana ODBC 驅動程式。 使用關鍵字 **SAP HANA CLIENT for Windows** 搜尋。 

## <a name="supported-sinks"></a>支援的接收器
如需「複製活動」支援做為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表格。 您可以從 SAP Hana 將資料移至任何支援的接收資料存放區中。 

## <a name="copy-data-wizard"></a>複製資料精靈
若要建立管線以將資料從 SAP Hana 複製到任何支援的接收資料存放區，最簡單的方式是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

下列範例提供您使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 來建立管線時，可使用的範例 JSON 定義。 此範例示範如何將資料從內部部署 SAP Hana 複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所列的任何接收器。  

> [!IMPORTANT]
> 此範例提供 JSON 程式碼片段。 其中並不包含建立 Data Factory 的逐步指示。 如需逐步指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文。

## <a name="sample-copy-data-from-sap-hana-to-azure-blob"></a>範例：從 SAP Hana 複製資料到 Azure Blob
此範例具有下列 Data Factory 實體：

1. [SapHana](#sap-hana-linked-service) 類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
3. [RelationalTable](#sap-hana-dataset) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [RelationalSource](#sap-hana-source-in-copy-activity) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

範例會每隔一小時就把 SAP Hana 執行個體的資料複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

第一步是設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

### <a name="sap-hana-linked-service"></a>SAP HANA 連結服務
此連結服務會將您的 SAP HANA 執行個體連結至資料處理站。 Type 屬性設為 **SapHana**。 TypeProperties 區段提供 SAP HANA 執行個體的連接資訊。

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務
此連結服務會將您的 Azure 儲存體帳戶連結至資料處理站。 Type 屬性設為 **AzureStorage**。 typeProperties 區段提供 Azure 儲存體帳戶的連線資訊。

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="sap-hana-input-dataset"></a>SAP HANA 輸入資料集

此資料集會定義 SAP HANA 資料集。 將資料處理站資料集的類型設定為 **RelationalTable**。 目前，您不指定任何特定類型的 SAP HANA 資料集屬性。 複製活動定義中的查詢會指定要從 SAP Hana 執行個體讀取的資料。 

將 external 屬性設定為 true 會通知 Data Factory 服務，指出資料表是在資料處理站外部，不是由資料處理站中的活動所產生。

頻率和間隔屬性會定義排程。 在此情況下，每小時會從 SAP Hana 執行個體讀取資料。 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob 輸出資料集
此資料集會定義輸出 Azure Blob 資料集。 Type 屬性設為 AzureBlob。 TypeProperties 區段提供從 SAP HANA 執行個體所儲存之複製資料的位置。 資料會每小時寫入新的 blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
```


### <a name="pipeline-with-copy-activity"></a>具有複製活動的管線

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，已將 **source** 類型設為 **RelationalSource** (適用於 SAP HANA 來源)，並將 **sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


## <a name="sap-hana-linked-service"></a>SAP HANA 連結服務
下表提供 SAP Hana 連結服務專屬 JSON 元素的描述。

屬性 | 說明 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
伺服器 | SAP Hana 執行個體所在之伺服器的名稱。 如果您的伺服器使用自訂連接埠，指定 `server:port`。 | 字串 | 是
authenticationType | 驗證類型。 | 字串。 "Basic" 或 "Windows" | 是 
username | 具有 SAP 伺服器存取權之使用者的名稱 | string | 是
password | 使用者的密碼。 | 字串 | 是
gatewayName | Data Factory 服務應該用來連接到內部部署 SAP Hana 執行個體的閘道器名稱。 | 字串 | 是
encryptedCredential | 加密的認證字串。 | string | 否

## <a name="sap-hana-dataset"></a>SAP HANA 資料集
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。 沒有類型特定的屬性支援 **RelationalTable** 的 SAP HANA 資料集類型。 


## <a name="sap-hana-source-in-copy-activity"></a>複製活動中的 SAP HANA 來源
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

而活動的 **typeProperties** 區段中，可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

當複製活動中的來源類型為 **RelationalSource** (包括 SAP Hana) 時，typeProperties 區段中可使用下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| query | 指定 SQL 查詢從 SAP HANA 執行個體讀取資料。 | SQL 查詢。 | 是 |

### <a name="type-mapping-for-sap-hana"></a>SAP Hana 的類型對應
如 [資料移動活動](data-factory-data-movement-activities.md) 一文所述，複製活動會藉由下列含有兩個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 SAP Hana 移動資料時，下列對應會用於從 SAP Hana 類型對應至 .NET 類型。

SAP Hana 類型 | 以 .Net 為基礎的類型
------------- | ---------------
TINYINT | 位元組
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | 單一
DOUBLE | 單一
DECIMAL | 十進位
BOOLEAN | 位元組
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
日期 | DateTime
TIME | 時間範圍
時間戳記 | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>已知限制
從 SAP HANA 複製資料時，有幾個已知的限制︰

- NVARCHAR 字串會截斷成 4000 個 Unicode 字元的最大長度
- 不支援 SMALLDECIMAL
- 不支援 VARBINARY
- 有效日期為 1899/12/30 到 9999/12/31 之間

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

