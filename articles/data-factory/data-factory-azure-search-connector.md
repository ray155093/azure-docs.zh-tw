---
title: "使用 Data Factory將資料推送到搜尋索引 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory，將資料推送到 Azure 搜尋服務索引。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 55c988bf74ff0f2e519e895a735dc68f3dc99855
ms.openlocfilehash: e2deed13106db9467eef181f25a0a226034df5a2
ms.lasthandoff: 12/21/2016

---

# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料推送到 Azure 搜尋服務索引
本文說明如何使用「複製活動」，將資料從 Data Factory 服務所支援的內部資料存放區推送到 Azure 搜尋服務索引。 支援的來源資料存放區會列於[支援的來源與接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表格的 [來源] 欄中。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

## <a name="enabling-connectivity"></a>啟用連線
若要讓 Data Factory 服務連接到內部部署資料存放區，您要在內部部署環境中安裝資料管理閘道。 您可以在裝載來源資料存放區的同一部電腦上或個別電腦上安裝閘道，以避免與資料存放區競用資源。

資料管理閘道會透過安全且可管理的方式，將內部部署資料來源連接到雲端服務。 如需資料管理閘道的詳細資訊，請參閱 [在內部部署和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文。

## <a name="copy-data-wizard"></a>複製資料精靈
若要建立管線以將資料從任何支援的來源資料存放區複製到 Azure 搜尋服務，最簡單的方式是使用 [複製資料] 精靈。 如需快速逐步解說，請參閱[教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md)。

以下範例提供可用來使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 建立管線的範例 JSON 定義。 這些範例示範如何將資料從內部部署 SQL Server 複製到 Azure 搜尋服務索引。 不過，您可以在 Azure Data Factory 中使用「複製活動」，將資料複製到[此處](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何內部部署資料存放區。   

## <a name="sample-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>範例：將資料從內部部署 SQL Server 複製到 Azure 搜尋服務索引

下列範例顯示︰

1.    [AzureSearch](#azure-search-linked-service-properties) 類型的連結服務。
2.    [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties)類型的連結服務。
3.    [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.    [AzureSearchIndex](#azure-search-index-dataset-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4.    具有使用 [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) 和 [AzureSearchIndexSink](#azure-search-index-sink-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

這個範例每小時都會將時間序列的資料從內部部署 SQL Server 資料庫複製到 Azure 搜尋服務索引。 範例後面的各節將會說明此範例中使用的 JSON 屬性。

第一步是在內部部署電腦上設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

**Azure 搜尋服務連結的服務：**

```JSON
{
    "name": "AzureSearchLinkedService",
       "properties": {
        "type": "AzureSearch",
           "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
       }
}
```

**SQL Server 連結服務**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server 輸入資料集**

此範例假設您已在 SQL Server 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。 您可以使用單一資料集來查詢相同資料庫內的多個資料表，但針對資料集的 tableName typeProperty 必須使用單一資料表。

設定 “external”: ”true” 可讓 Data Factory 服務知道資料集是在 Data Factory 外部，而不是由 Data Factory 中的活動所產生。

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
```

**Azure 搜尋服務輸出資料集：**

此範例會將資料複製到名為 **products** 的 Azure 搜尋服務索引。 Data Factory 不會建立索引。 若要測試範例，請以此名稱建立索引。 使用與輸入資料集相同的資料行數目建立 Azure 搜尋服務索引。 每小時都會將新項目加入至 Azure 搜尋服務索引。

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
         "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，將 **source** 類型設為 **SqlSource**，並且將 **sink** 類型設為 **AzureSearchIndexSink**。 針對 **SqlReaderQuery** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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
```

如果您從雲端資料存放區將資料複製到 Azure 搜尋服務，則 `executionLocation` 是必要屬性。 以下顯示在複製活動 `typeProperties` 做為範例底下所需的變更。 參閱[在雲端資料存放區之間複製資料](data-factory-data-movement-activities.md#global)一節以取得支援的值和更多詳細資料。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="azure-search-linked-service-properties"></a>Azure 搜尋服務連結的服務屬性

下表提供 Azure 搜尋服務連結服務專屬之 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 類型 | type 屬性必須設為：**AzureSearch**。 | yes |
| URL | Azure 搜尋服務的 URL。 | 是 |
| key | Azure 搜尋服務的系統管理金鑰。 | 是 |

## <a name="azure-search-index-dataset-properties"></a>Azure 搜尋服務索引的資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型。 不同類型資料集的 **typeProperties** 區段不同。 **AzureSearchIndex** 類型資料集的 typeProperties 區段有下列屬性：

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 類型 | type 屬性必須設為 **AzureSearchIndex**。| yes |
| IndexName | Azure 搜尋服務索引的名稱。 Data Factory 不會建立索引。 索引必須存在於 Azure 搜尋服務中。 | yes |


## <a name="azure-search-index-sink-properties"></a>Azure 搜尋服務索引的接收器屬性
如需定義活動的區段和屬性完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及各種原則) 適用於所有類型的活動。 另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

就「複製活動」而言，如果來源的類型為 **AzureSearchIndexSink**，則 typeProperties 區段會有下列可用屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | 指定若文件已經存在於索引中，是否要合併或取代。 請參閱 [WriteBehavior 屬性](#writebehavior-property)。| 合併 (預設值)<br/>上傳| 否 |
| WriteBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料上傳至 Azure 搜尋服務中。 如需詳細資訊，請參閱 [WriteBatchSize 屬性](#writebatchsize-property)。 | 1 到 1000。 預設值為 1000。 | 否 |

### <a name="writebehavior-property"></a>WriteBehavior 屬性
AzureSearchSink 會在寫入資料時更新插入。 換句話說，在撰寫文件時，如果文件索引鍵已經存在於 Azure 搜尋服務索引中，Azure 搜尋服務就會更新現有的文件，而不是擲回衝突例外狀況。

AzureSearchSink (藉由使用 AzureSearch SDK) 提供下列兩種更新插入行為：

- **合併**︰將新文件中的所有資料行與現有的文件相結合。 對於新文件中含有 null 值的資料行，則會保留現有文件中的值。
- **上傳**：新的文件會取代現有的文件。 針對新文件中未指定的資料行，不論現有的文件中是否具有非 null 的值，都會將值設為 null。

預設行為是**合併**。

### <a name="writebatchsize-property"></a>WriteBatchSize 屬性
Azure 搜尋服務支援批次寫入文件。 一個批次可包含 1 到 1,000 個動作。 一個動作可指示一份文件來執行上傳/合併作業。

### <a name="data-type-support"></a>資料類型支援
下表指出是否支援 Azure 搜尋服務資料類型。

| Azure 搜尋服務資料類型 | 在 Azure 搜尋服務接收器中受到支援 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| 兩倍 | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| 字串陣列 | N |
| GeographyPoint | N |

## <a name="copy-from-a-cloud-source"></a>從雲端來源複製
如果您從雲端資料存放區將資料複製到 Azure 搜尋服務，則 `executionLocation` 是必要屬性。 以下顯示在複製活動 `typeProperties` 做為範例底下所需的變更。 參閱[在雲端資料存放區之間複製資料](data-factory-data-movement-activities.md#global)一節以取得支援的值和更多詳細資料。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="specifying-structure-definition-for-rectangular-datasets"></a>指定矩形資料集的結構定義
在資料集 JSON 中的結構區段 (“structure”) 是矩形資料表 (有資料列和資料行) 的**選擇性**區段，並包含該資料表的資料行集合。 使用結構區段來提供類型轉換的類型資訊或執行資料行對應。 下列各節更詳細說明這些功能。

各資料行包含下列屬性：

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 名稱 | 資料行的名稱。 | 是 |
| 類型 | 資料行的資料類型。 如需有關何時應指定類型資訊的詳細資訊，請參閱[類型轉換](#type-conversion-sample)。 | 否 |
| culture | .NET 型文化特性是在已指定類型 (type) 且是 .NET 類型 `Datetime` 或 `Datetimeoffset` 時使用。 預設值為 `en-us`。  | 否 |
| format | 格式字串是在已指定類型且是 .NET 類型 `Datetime` 或 `Datetimeoffset` 時使用。 | 否 |

下列範例示範具有 `userid`、`name` 及 `lastlogindate` 三個資料行的資料表的結構區段 JSON。

```JSON
"structure":
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```
有關何時要包括 “structure” 資訊以及在**結構**區段中要包含哪些資訊，請遵循下列準則。

- **針對結構化的資料來源**，其會將資料結構描述和類型資訊以及資料本身儲存在一起 (例如：SQL Server、Oracle、Azure 資料表等來源)：只有當您將特定來源資料行對應至接收器中的特定資料行且其名稱不相同時，才應指定 “structure” 區段。 請參閱資料行對應區段中的詳細資訊。

    如上所述，“structure” 區段中的類型資訊是選擇性的。 針對結構化的來源，類型資訊可用來做為資料存放區中資料集定義的一部分，因此當您包含 “structure” 區段時不應包含類型資訊。
- **針對讀取的資料來源 (尤其是 Azure Blob) 的結構描述**：您可以選擇儲存資料，而不需將任何結構描述或類型資訊與資料儲存在一起。 針對這些類型的資料來源，在以下兩種案例中應包含 “structure”：
    - 將來源資料行對應到接收資料行。
    - 當資料集為「複製活動」中的來源時，您可以提供在 “structure” 中提供類型資訊。 Data Factory 會使用此類型資訊來轉換為接收器的原生類型。 如需詳細資訊，請參閱[移動資料進出 Azure Blob](data-factory-azure-blob-connector.md) 文章。

### <a name="supported-net-based-types"></a>支援 .NET 型的類型
Data Factory 支援下列符合 CLS 標準的 .NET 型類型值，以利針對讀取的資料來源 (如 Azure Blob) 的結構描述在 “structure” 中提供類型資訊。

- Int16
- Int32
- Int64
- 單一
- 兩倍
- 十進位
- Bool
- String
- Datetime
- Datetimeoffset
- Timespan

針對 Datetime 和 Datetimeoffset，您也可以選擇性地指定 “culture” 和 “format” 字串，以幫助剖析您的自訂 Datetime 字串。 請參閱下一節中的類型轉換範例：


[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>效能和微調  
若要了解影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法，請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)。

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

* [複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，以取得使用「複製活動」來建立管線的逐步指示。

