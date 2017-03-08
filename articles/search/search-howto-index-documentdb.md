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
ms.date: 02/08/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: d19a85e127b548e5f8979358879e8b9354934904
ms.openlocfilehash: ca09ac90dfcf125291bc0b312b16e28160a18527
ms.lasthandoff: 02/09/2017


---
# <a name="connecting-documentdb-with-azure-search-using-indexers"></a>使用索引子連接 DocumentDB 與 Azure 搜尋

如果您想在 DocumentDB 資料上實作絕佳的搜尋體驗，您可以設定和執行 Azure 搜尋服務索引子，以擷取資料並提取到 Azure 搜尋服務索引子中。 在本文中，我們將說明如何整合 Azure DocumentDB 與 Azure 搜尋服務，但不需要撰寫任何程式碼來維護索引基礎結構。

若要如此設定，您必須有 [Azure 搜尋服務](search-create-service-portal.md)，還有索引、索引子和資料來源。 您可以使用下列其中一種方法建立這些物件︰[入口網站](search-import-data-portal.md)、[.NET SDK](/dotnet/api/microsoft.azure.search)，或適用於所有非 .NET 語言的 [REST API](/rest/api/searchservice/)。 

如果您選擇使用入口網站，[匯入資料精靈](search-import-data-portal.md)會引導您建立所有這些物件。 通常會為您產生預設索引。

> [!NOTE]
> 您可以從 DocumentDB 儀表板啟動**匯入資料**精靈，以簡化該資料來源的索引建立作業。 在左側導覽中，移至 [集合] > [新增 Azure 搜尋服務] 以便開始使用。

## <a id="Concepts"></a>Azure 搜尋服務索引子概念
Azure 搜尋服務支援建立與管理資料來源 (包括 DocumentDB) 和操作這些資料來源的索引子。

**資料來源**指定要編製索引的資料、認證，以及可識別資料是否變更 (例如修改或刪除集合內的文件) 的原則。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

「 **索引子** 」說明資料如何從資料來源流動到目標搜尋索引。 您應該規劃為每個目標索引和資料來源組合建立一個索引子。 雖然您可以擁有寫入相同索引的多個索引子，但一個索引子只能寫入單一索引。 索引子可用來：

* 執行資料的一次性複製以填入索引。
* 依照排程將索引與資料來源中的變更同步。 排程是索引子定義的一部分。
* 視需要叫用索引的隨選更新。

## <a id="CreateDataSource"></a>步驟 1：建立資料來源
發出 HTTP POST 要求，在您的 Azure 搜尋服務中建立新的資料來源，需包含下列要求標頭。

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

`api-version` 為必要項目。 有效值包括 `2015-02-28` 或更新版本。 請瀏覽 [Azure 搜尋服務中的 API 版本](search-api-versions.md) ，查看所有支援的搜尋服務 API 版本。

要求的主體包含資料來源定義，其中應包含下列欄位：

* **名稱**：選擇任何名稱，以代表您的 DocumentDB 資料庫。
* **類型：**使用 `documentdb`。
* **認證**：
  
  * **connectionString**：必要。 以下列格式指定 Azure DocumentDB 資料庫的連接資訊： `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`
* **容器**：
  
  * **名稱**：必要。 指定要編製索引的 DocumentDB 集合的識別碼。
  * **查詢**：選擇性。 您可以指定查詢將任意 JSON 文件簡維成 Azure 搜尋服務可以編製索引的一般結構描述。
* **dataChangeDetectionPolicy**：選擇性。 請參閱以下的 [資料變更偵測原則](#DataChangeDetectionPolicy) 。
* **dataDeletionDetectionPolicy**：選擇性。 請參閱以下的 [資料刪除偵測原則](#DataDeletionDetectionPolicy) 。

請參閱下面的 [要求本文範例](#CreateDataSourceExample)。

### <a id="DataChangeDetectionPolicy"></a>擷取已變更的文件
資料變更偵測原則是用來有效識別已變更的資料項目。 目前，唯一支援的原則是使用 DocumentDB 所提供之 `_ts` 上次修改時間戳記屬性的 `High Water Mark` 原則，指定方式如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

您還必須為查詢在投射中加入 `_ts` 和 `WHERE` 子句。 例如：

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark

### <a id="DataDeletionDetectionPolicy"></a>擷取已刪除的文件
當從來源資料表中刪除資料列時，您也應該在搜尋索引中刪除這些資料列。 資料刪除偵測原則可用來有效識別刪除的資料項目。 目前，唯一支援的原則是「 `Soft Delete` 」原則 (刪除會標示為某種形式的旗標)，指定方式如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [!NOTE]
> 如果您使用自訂投影，則應該在 SELECT 子句中包含 softDeleteColumnName 屬性。
> 
> 

### <a id="LeveagingQueries"></a>利用查詢
除了擷取已變更和刪除的文件之外，指定 DocumentDB 查詢也可用來簡維巢狀屬性、回溯陣列、投影 JSON 屬性，以及篩選要編製索引的資料。 處理要編製索引的資料可以改善「Azure 搜尋服務」索引子的效能。

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


簡維查詢︰

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark
    
    
投影查詢：

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark


回溯陣列查詢︰

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark
    
    
篩選查詢：

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark


### <a id="CreateDataSourceExample"></a>要求本文範例
下列範例會建立包含自訂查詢和原則提示的資料來源：

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
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

### <a name="response"></a>Response
如果已成功建立該資料來源，您將會收到一則 HTTP 201 已建立的回應。

## <a id="CreateIndex"></a>步驟 2：建立索引
建立目標 Azure 搜尋服務索引 (如果您尚未建立)。 在做法上可以從 [Azure 入口網站 UI](search-create-index-portal.md)，或使用[建立索引 REST API](/rest/api/searchservice/create-index) 或[索引類別](/dotnet/api/microsoft.azure.search.models.index)。

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


請確定目標索引的結構描述會與來源 JSON 文件的結構描述或自訂查詢投射的輸出相容。

> [!NOTE]
> 對於資料分割後的集合，預設文件索引鍵是 DocumentDB 的 `_rid` 屬性，它在 Azure 搜尋服務中重新命名為 `rid`。 此外，DocumentDB 的 `_rid` 值包含 Azure 搜尋服務索引鍵中無效的字元。 因此，`_rid` 值採用 Base64 編碼。
> 
> 

### <a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>圖 A：JSON 資料類型與 Azure 搜尋服務資料類型之間的對應
| JSON 資料類型 | 相容的目標索引欄位類型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 看起來像是整數的數字 |Edm.Int32、Edm.Int64、Edm.String |
| 看起來像是浮點的數字 |Edm.Double、Edm.String |
| String |Edm.String |
| 基本類型的陣列，例如 "a"、"b"、"c" |Collection(Edm.String) |
| 看起來像是日期的字串 |Edm.DateTimeOffset、Edm.String |
| GeoJSON 物件，例如 { "type": "Point"、"coordinates": [ long, lat ] } |Edm.GeographyPoint |
| 其他 JSON 物件 |N/A |

### <a id="CreateIndexExample"></a>要求本文範例
下列範例會建立包含識別碼和描述欄位的索引：

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

### <a name="response"></a>Response
如果已成功建立該索引，您將會收到一則 [HTTP 201 已建立] 的回應。

## <a id="CreateIndexer"></a>步驟 3：建立索引子
您可以使用包含下列標頭的 HTTP POST 要求，在 Azure 搜尋服務內建立新的索引子。

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

要求的本文包含索引子定義，其中應包含下列欄位：

* **名稱**：必要。 索引子的名稱。
* **dataSourceName**：必要。 現有資料來源的名稱。
* **targetIndexName**：必要。 現有索引的名稱。
* **排程**：選擇性。 請參閱以下的 [索引排程](#IndexingSchedule) 。

### <a id="IndexingSchedule"></a>依照排程執行索引子
索引子可以選擇性地指定排程。 如有排程，索引子將會依照排程定期執行。 排程具有下列屬性：

* **間隔**：必要。 可用以指定索引子執行間隔或期間的持續時間值。 允許的最小間隔為 5 分鐘；最長間隔為一天。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 間隔的模式為： `P(nD)(T(nH)(nM))`。 範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 個小時。
* **startTime**：必要。 指定索引子應該開始執行的 UTC 日期時間。

### <a id="CreateIndexerExample"></a>要求本文範例
下列範例會建立索引子，可將資料從 `myDocDbDataSource` 資料來源所參考的集合，依照排程 (從 2015 年 1 月 1 日 UTC 開始，每小時執行一次) 複製到 `mySearchIndex` 索引。

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

### <a name="response"></a>Response
如果已成功建立該索引子，您將會收到一則 [HTTP 201 已建立] 的回應。

## <a id="RunIndexer"></a>步驟 4：執行索引子
為了依照排程定期執行，您也可以發出下列 HTTP POST 要求，視需要叫用索引子：

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Response
如果已成功叫用該索引子，您將會收到一則 [HTTP 202 已接受] 的回應。

## <a name="GetIndexerStatus"></a>步驟 5：取得索引子狀態
您可以發出 HTTP GET 要求來擷取索引子的目前狀態和執行記錄：

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Response
您將會看到隨著回應本文傳回的一則 [HTTP 200 確定] 的回應，內容包括整體索引子健全狀況狀態、最後索引子引動過程，以及最新索引子引動過程 (如果有的話) 的歷程記錄等相關資訊。

回應看起來應該如下所示：

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

## <a name="NextSteps"></a>接續步驟
恭喜！ 您剛剛了解如何使用 DocumentDB 的索引子來整合 Azure DocumentDB 與 Azure 搜尋服務。

* 若要深入了解 Azure DocumentDB，請參閱 [DocumentDB 服務頁面](https://azure.microsoft.com/services/documentdb/)。
* 若要深入了解 Azure 搜尋服務，請參閱 [搜尋服務頁面](https://azure.microsoft.com/services/search/)。
