---
title: "將 Azure 搜尋服務的 DocumentDB 資料來源編製索引 | Microsoft Docs"
description: "本文說明如何以 DocumentDB 作為資料來源建立 Azure 搜尋服務索引子。"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: documentdb
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 04/11/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 5f657ed128103d4bf1304dfc5fae8d86ef950d87
ms.lasthandoff: 04/12/2017


---
# <a name="connecting-documentdb-with-azure-search-using-indexers"></a>使用索引子連接 DocumentDB 與 Azure 搜尋

如果您想在 DocumentDB 資料上實作絕佳的搜尋體驗，可以使用 Azure 搜尋服務索引子將資料提取到 Azure 搜尋服務索引子中。 在本文中，我們將說明如何整合 Azure DocumentDB 與 Azure 搜尋服務，但不需要撰寫任何程式碼來維護索引基礎結構。

若要設定 DocumentDB 索引子，您必須擁有 [Azure 搜尋服務](search-create-service-portal.md)，並建立索引、資料來源，最後再建立索引子。 您可以使用 [入口網站](search-import-data-portal.md)、[.NET SDK](/dotnet/api/microsoft.azure.search)、或適用於所有非 .NET 語言的 [REST API](/rest/api/searchservice/) 建立這些物件。 

如果您選擇使用入口網站，[匯入資料精靈](search-import-data-portal.md)會引導您建立所有這些資源。

> [!NOTE]
> 您可以從 DocumentDB 儀表板啟動**匯入資料**精靈，以簡化該資料來源的索引建立作業。 在左側導覽中，移至 [集合] > [新增 Azure 搜尋服務] 以便開始使用。

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Azure 搜尋服務索引子概念
Azure 搜尋服務支援建立與管理資料來源 (包括 DocumentDB) 和操作這些資料來源的索引子。

**資料來源**指定要編製索引的資料、認證，以及可識別資料是否變更 (例如修改或刪除集合內的文件) 的原則。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

「 **索引子** 」說明資料如何從資料來源流動到目標搜尋索引。 索引子可用來：

* 執行資料的一次性複製以填入索引。
* 依照排程將索引與資料來源中的變更同步。 排程是索引子定義的一部分。
* 視需要叫用索引的隨選更新。

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>步驟 1：建立資料來源
若要建立資料來源，執行：

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

要求的主體包含資料來源定義，其中應包含下列欄位：

* **名稱**：選擇任何名稱，以代表您的 DocumentDB 資料庫。
* **type**：必須是 `documentdb`。
* **認證**：
  
  * **connectionString**：必要。 以下列格式指定 Azure DocumentDB 資料庫的連接資訊： `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`
* **容器**：
  
  * **名稱**：必要。 指定要編製索引的 DocumentDB 集合的識別碼。
  * **查詢**：選擇性。 您可以指定查詢將任意 JSON 文件簡維成 Azure 搜尋服務可以編製索引的一般結構描述。
* **dataChangeDetectionPolicy**：建議使用。 請參閱[索引變更的文件](#DataChangeDetectionPolicy)小節。
* **dataDeletionDetectionPolicy**：選擇性。 請參閱[索引刪除的文件](#DataDeletionDetectionPolicy)小節。

### <a name="using-queries-to-shape-indexed-data"></a>使用查詢來形塑索引的資料
您可以指定 DocumentDB 查詢來壓平合併巢狀屬性或陣列、投影 JSON 屬性，以及篩選要編製索引的資料。 

範例文件︰

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

篩選查詢：

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark

壓平合併查詢︰

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark
    
    
投影查詢：

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark


陣列壓平合併查詢︰

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>步驟 2：建立索引
建立目標 Azure 搜尋服務索引 (如果您尚未建立)。 您可以使用 [Azure 入口網站 UI](search-create-index-portal.md)、[建立索引 REST API](/rest/api/searchservice/create-index) 或[索引類別](/dotnet/api/microsoft.azure.search.models.index)建立索引。

下列範例會建立包含識別碼和描述欄位的索引：

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

請確定目標索引的結構描述會與來源 JSON 文件的結構描述或自訂查詢投射的輸出相容。

> [!NOTE]
> 對於資料分割後的集合，預設文件索引鍵是 DocumentDB 的 `_rid` 屬性，它在 Azure 搜尋服務中重新命名為 `rid`。 此外，DocumentDB 的 `_rid` 值包含 Azure 搜尋服務索引鍵中無效的字元。 因此，`_rid` 值採用 Base64 編碼。
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON 資料類型與 Azure 搜尋服務資料類型之間的對應
| JSON 資料類型 | 相容的目標索引欄位類型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 看起來像是整數的數字 |Edm.Int32、Edm.Int64、Edm.String |
| 看起來像是浮點的數字 |Edm.Double、Edm.String |
| String |Edm.String |
| 基本類型的陣列，例如 ["a", "b", "c"] |Collection(Edm.String) |
| 看起來像是日期的字串 |Edm.DateTimeOffset、Edm.String |
| GeoJSON 物件，例如 { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 其他 JSON 物件 |N/A |

<a name="CreateIndexer"></a>
## <a name="step-3-create-an-indexer"></a>步驟 3：建立索引子

建立索引和資料來源之後，您就可以開始建立索引子︰

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

這個索引子每隔兩小時就會執行一次 (已將排程間隔設為 "PT2H")。 若每隔 30 分鐘就要執行索引子，可將間隔設為 "PT30M"。 支援的最短間隔為 5 分鐘。 排程為選擇性 - 如果省略，索引子只會在建立時執行一次。 不過，您隨時都可依需求執行索引子。   

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>視需要執行索引子
除了依照排程定期執行以外，您也可以視需要叫用索引子：

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> 當執行 API 成功傳回時，索引子叫用已經排程，但實際處理不會同步發生。 

您可以在入口網站中監視索引子狀態，或使用取得索引子狀態 API進行監視，我們接下將說明此 API。 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>取得索引子狀態
您可以擷取索引子的狀態和執行歷程記錄：

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

回應包含整體索引子的狀態、最後 (或進行中) 的索引子叫用，以及最新的索引子叫用歷程記錄。

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

執行歷程記錄包含多達 50 個最近完成的執行，以倒序的方式進行儲存 (因此最新的執行會排在回應中的第一位)。

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>索引已變更的文件
資料變更偵測原則是用來有效識別已變更的資料項目。 目前，唯一支援的原則是使用 DocumentDB 所提供之 `_ts` (時間戳記) 屬性的 `High Water Mark` 原則，指定方式如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

為確保索引子效能良好，強烈建議使用此原則。 

如果您使用自訂查詢，請確定查詢有投射 `_ts` 屬性。 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>索引已刪除的文件
從集合中刪除資料列時，通常也會想刪除搜尋索引內的那些資料列。 資料刪除偵測原則可用來有效識別刪除的資料項目。 目前，唯一支援的原則是「 `Soft Delete` 」原則 (刪除會標示為某種形式的旗標)，指定方式如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

如果您使用自訂查詢，請確定查詢有投射到 `softDeleteColumnName` 參考的屬性。

下列範例會建立包含虛刪除原則的資料來源：

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>接續步驟
恭喜！ 您已了解如何使用 DocumentDB 的索引子來整合 Azure DocumentDB 與 Azure 搜尋服務。

* 若要深入了解 Azure DocumentDB，請參閱 [DocumentDB 服務頁面](https://azure.microsoft.com/services/documentdb/)。
* 若要深入了解 Azure 搜尋服務，請參閱 [搜尋服務頁面](https://azure.microsoft.com/services/search/)。
