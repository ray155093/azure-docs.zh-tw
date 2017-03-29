---
title: "使用 Azure Data Factory 從 SAP Business Warehouse 移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從 SAP Business Warehouse 移動資料。"
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
ms.openlocfilehash: 6a14e5e0cab25b26782ebd45b52aa7542b7e24d5
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP Business Warehouse 移動資料
本文概述如何使用 Azure Data Factory 管線中的複製活動，將資料從 SAP Business Warehouse 移動至另一個資料存放區。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。 資料處理站目前只支援將資料從 SAP Business Warehouse 移動至其他資料存放區，而不支援將資料從其他資料存放區移動至 SAP Business Warehouse。


## <a name="supported-versions-and-installation"></a>支援的版本和安裝
此連接器支援 SAP Business Warehouse 7.x 版。 它支援使用 MDX 查詢從 Infocube 和 QueryCubes (包括 BEx 查詢) 複製資料。

若要啟用 SAP BW 執行個體的連線，請安裝下列元件︰
- **資料管理閘道**：資料處理站服務支援使用稱為資料管理閘道的元件，以連線至內部部署資料存放區 (包括 SAP Business Warehouse)。 若要了解資料管理閘道和設定閘道的逐步指示，請參閱[在內部部署資料存放區和雲端資料存放區之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文。 即使 SAP Business Warehouse 裝載於 Azure IaaS 虛擬機器 (VM) 中，也需要有閘道器。 您可以將閘道安裝在與資料存放區相同或相異的 VM 上，只要閘道可以連線到資料庫即可。
- 閘道電腦上的 **SAP NetWeaver 程式庫**。 您可以從 SAP 系統管理員那裡取得 SAP Netweaver 程式庫，或直接從 [SAP 軟體下載中心](https://support.sap.com/swdc)取得。 搜尋 **SAP 附註 #1025361** 以取得最新版本的下載位置。 請確定 SAP NetWeaver 程式庫 (32 位元或 64 位元) 的架構符合您的閘道器安裝。 然後根據 SAP 附註，安裝 SAP NetWeaver RFC SDK 中包含的所有檔案。 SAP NetWeaver 程式庫也隨附於 SAP 用戶端工具安裝。

## <a name="supported-sinks"></a>支援的接收器
如需「複製活動」支援做為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表格。 您可以將資料從 SAP Business Warehouse 移至任何支援的接收資料存放區。 

## <a name="copy-data-wizard"></a>複製資料精靈
若要建立管線以將資料從 SAP Business Warehouse 複製到任何支援的接收資料存放區，最簡單的方式是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

下列範例提供您使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 來建立管線時，可使用的範例 JSON 定義。 此範例示範如何將資料從內部部署 SAP Business Warehouse 複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。  

> [!IMPORTANT]
> 此範例提供 JSON 程式碼片段。 其中並不包含建立 Data Factory 的逐步指示。 如需逐步指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文。

## <a name="sample-copy-data-from-sap-business-warehouse-to-azure-blob"></a>範例：將資料從 SAP Business Warehouse 複製到 Azure Blob
此範例具有下列 Data Factory 實體：

1. [SapBw](#sap-bw-linked-service) 類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
3. [RelationalTable](#sap-bw-dataset) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [RelationalSource](#sap-bw-source-in-copy-activity) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

範例會每小時將資料從 SAP Business Warehouse 執行個體複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

第一步是設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse 連結服務
此連結服務會將您的 SAP BW 執行個體連結至資料處理站。 Type 屬性設為 **SapBw**。 typeProperties 區段提供 SAP BW 執行個體的連線資訊。 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

### <a name="sap-bw-input-dataset"></a>SAP BW 輸入資料集
此資料集定義 SAP Business Warehouse 資料集。 將資料處理站資料集的類型設定為 **RelationalTable**。 目前，您未指定 SAP BW 資料集的任何類型特定屬性。 複製活動定義中的查詢指定要從 SAP BW 執行個體讀取的資料。 

將 external 屬性設定為 true 會通知 Data Factory 服務，指出資料表是在資料處理站外部，不是由資料處理站中的活動所產生。

頻率和間隔屬性會定義排程。 在此案例中，每小時會從 SAP BW 執行個體讀取資料。 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
此資料集會定義輸出 Azure Blob 資料集。 Type 屬性設為 AzureBlob。 TypeProperties 區段提供從 SAP BW 執行個體複製的資料要儲存在何處。 資料會每小時寫入新的 blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **RelationalSource** (適用於 SAP BW 來源)，**sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的查詢會選取過去一小時內要複製的資料。

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


## <a name="sap-bw-linked-service"></a>SAP BW 連結服務
下表提供 SAP Business Warehouse (BW) 連結服務專屬 JSON 元素的描述。

屬性 | 說明 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
伺服器 | SAP BW 執行個體所在之伺服器的名稱。 | 字串 | 是
systemNumber | SAP BW 系統的系統編號。 | 以字串表示的二位數十進位數字。 | 是
clientId | SAP W 系統中用戶端的用戶端識別碼。 | 以字串表示的三位數十進位數字。 | 是
username | 具有 SAP 伺服器存取權之使用者的名稱 | 字串 | 是
password | 使用者的密碼。 | 字串 | 是
gatewayName | 資料處理站服務應該用來連線至內部部署 SAP BW 執行個體的閘道器名稱。 | 字串 | 是
encryptedCredential | 加密的認證字串。 | string | 否

## <a name="sap-bw-dataset"></a>SAP BW 資料集
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。 **RelationalTable** 類型的 SAP BW 資料集不支援類型專用的屬性。 


## <a name="sap-bw-source-in-copy-activity"></a>複製活動中的 SAP BW 來源
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

而活動的 **typeProperties** 區段中，可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

當複製活動中的來源類型為 **RelationalSource** 時 (包括 SAP BW)，typeProperties 區段中會有下列屬性可用：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| query | 指定 MDX 查詢從 SAP BW 執行個體讀取資料。 | MDX 查詢。 | 是 |

### <a name="type-mapping-for-sap-bw"></a>SAP BW 的類型對應
如 [資料移動活動](data-factory-data-movement-activities.md) 一文所述，複製活動會藉由下列含有兩個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 SAP BW 移動資料時，SAP BW 類型至 .NET 類型會使用下列對應。

ABAP 字典中的資料類型 | .Net 資料類型
-------------------------------- | --------------
ACCP |    int
CHAR | String
CLNT | String
CURR | 十進位
CUKY | String
DEC | 十進位
FLTP | 兩倍
INT1 | 位元組
INT2 | Int16
INT4 | int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | 十進位
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
單位 | String
DATS | String
NUMC | String
TIMS | String

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

