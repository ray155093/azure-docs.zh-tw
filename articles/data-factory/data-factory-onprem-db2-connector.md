---
title: "使用 Azure Data Factory 從 DB2 移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從 DB2 資料庫移動資料。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: dd8a68029449ad013c4df9a46c558efaefd20e96
ms.openlocfilehash: 78adfcb8431305a94982b11586fce5d183fe35ec


---
# <a name="move-data-from-db2-using-azure-data-factory"></a>使用 Azure Data Factory 從 DB2 移動資料
本文將概述如何使用 Azure 資料處理站中的複製活動將資料從 DB2 移動到另一個資料存放區。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

資料處理站支援使用資料管理閘道器連接至內部部署 DB2 來源。 請參閱 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文來了解資料管理閘道和設定閘道的逐步指示。

> [!NOTE]
> 即使 DB2 裝載於 Azure IaaS VM 中，仍請使用閘道器與其連接。 如果您正嘗試連接到裝載於雲端中的 DB2 執行個體，您也可以在 IaaS VM 中安裝閘道器執行個體。
>
>

資料處理站目前只支援將資料從 DB2 移至其他資料存放區，而不支援從其他資料存放區移至 DB2

## <a name="supported-versions"></a>支援的版本
此 DB2 連接器支援以下 IBM DB2 平台和版本，以及支援分散式關聯資料庫架構 (DRDA) SQL 存取管理員 (SQLAM) 版本 9、10 和 11：

* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

資料管理閘道提供內建的 DB2 驅動程式，因此從 DB2 複製資料時，您不需要手動安裝任何驅動程式。

> [!NOTE]
> 如需連接/閘道器相關問題的疑難排解秘訣，請參閱 [針對閘道問題進行疑難排解](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) 。
>
>

## <a name="copy-data-wizard"></a>複製資料精靈
要建立從 DB2 資料庫複製資料的管線，最簡單的方法是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

以下範例提供可用來使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 建立管線的範例 JSON 定義。 這些範例示範如何將資料從 DB2 資料庫複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動，將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>範例：從 DB2 複製資料到 Azure Blob
此範例示範如何將資料從內部部署 DB2 資料庫複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。  

此範例具有下列 Data Factory 實體：

1. [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
3. [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每個小時將資料從 DB2 資料庫中的查詢結果複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

第一個步驟是安裝和設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

**DB2 連結服務：**

```JSON
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure Blob 儲存體連結服務：**

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**DB2 輸入資料集：**

此範例假設您已在 DB2 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestamp")。

設定 “external”: true 可讓 Data Factory 服務知道此資料集是在 Data Factory 外部，而不是由 Data Factory 中的活動所產生。 請注意，**類型**需設為 **RelationalTable**。

```JSON
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```JSON
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，已將 **source** 類型設為 **RelationalSource**，並將 **sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的 SQL 查詢會從 Orders 資料表選取資料。

```JSON
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="db2-linked-service-properties"></a>DB2 連結服務屬性
下表提供 DB2 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| 類型 |類型屬性必須設為： **OnPremisesDB2** |是 |
| 伺服器 |DB2 伺服器的名稱。 |是 |
| 資料庫 |DB2 資料庫的名稱。 |是 |
| 結構描述 |在資料庫中的結構描述名稱。 結構描述名稱會區分大小寫。 |否 |
| authenticationType |用來連接到 DB2 資料庫的驗證類型。 可能的值為：匿名、基本和 Windows。 |是 |
| username |如果您使用基本或 Windows 驗證，請指定使用者名稱。 |否 |
| password |指定您為使用者名稱所指定之使用者帳戶的密碼。 |否 |
| gatewayName |Data Factory 服務應該用來連接到內部部署 DB2 資料庫的閘道器名稱。 |是 |

如需設定內部部署 DB2 資料來源認證的詳細資訊，請參閱[利用資料管理閘道在內部部署來源和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。 

## <a name="db2-dataset-type-properties"></a>DB2 資料集類型屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。 RelationalTable 資料集類型的 typeProperties 區段 (包含 DB2 資料集) 具有下列屬性。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| tableName |DB2 資料庫執行個體中連結服務所參照的資料表名稱。 tableName 會區分大小寫。 |否 (如果已指定 **RelationalSource** 的 **query**) |

## <a name="db2-copy-activity-type-properties"></a>DB2 複製活動類型屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

在複製活動中，如果來源類型為 **RelationalSource** (包含 DB2)，則 typeProperties 區段可使用下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| query |使用自訂查詢來讀取資料。 |SQL 查詢字串。 例如： `"query": "select * from "MySchema"."MyTable""`。 |否 (如果已指定 **dataset** 的 **tableName**) |

> [!NOTE]
> 結構描述和資料表名稱會區分大小寫。 在查詢中以 "" (雙引號) 括住名稱。  
>
>

**範例：**

```JSON
"query": "select * from "DB2ADMIN"."Customers""
```


[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>DB2 的類型對應
如同 [資料移動活動](data-factory-data-movement-activities.md) 一文所述，「複製活動」會藉由含有下列 2 個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

將資料移到 DB2 時，從 DB2 類型到 .NET 類型會使用下列對應。

| DB2 資料庫類型 | .NET Framework 類型 |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |單一 |
| 兩倍 |兩倍 |
| Float |兩倍 |
| 十進位 |十進位 |
| DecimalFloat |十進位 |
| 數值 |十進位 |
| Date |Datetime |
| 時間 |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| 圖形 |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |單一 |
| 兩倍 |兩倍 |
| Float |兩倍 |
| 十進位 |十進位 |
| DecimalFloat |十進位 |
| 數值 |十進位 |
| Date |Datetime |
| 時間 |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。



<!--HONumber=Jan17_HO4-->


