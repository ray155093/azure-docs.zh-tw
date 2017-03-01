---
title: "使用索引子將 Azure SQL Database 連接至 Azure 搜尋服務 | Microsoft Docs"
description: "了解如何使用索引子將資料從 Azure SQL Database 提取至 Azure 搜尋服務索引。"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 0841744b806f3dba38dddee21fb7fe881e07134f
ms.openlocfilehash: 51c9d9afb6c2ed460abd4c47a6afbc404b97a85e
ms.lasthandoff: 02/16/2017

---

# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>使用索引子將 Azure SQL Database 連接至 Azure 搜尋服務
Azure 搜尋服務是託管的雲端搜尋服務，讓提供絕佳的搜尋體驗變得更容易。 可以搜尋之前，您需要使用您的資料填入 Azure 搜尋服務索引。 如果 Azure SQL Database 中有您的資料，全新**適用於 Azure SQL Database 的 Azure 搜尋服務索引子** (或簡稱 **Azure SQL 索引子**) 可自動化編製索引的程序。 這表示您可以減少編寫程式碼的工作，並且減少需要處理的基礎結構。

本文不僅介紹使用索引子的機制，也會說明只在 Azure SQL Database 上出現的功能 (例如，整合變更追蹤)。 Azure 搜尋服務也支援其他資料來源，例如 Azure DocumentDB、 Blob 儲存體和表格儲存體。 如果您想參閱其他資料來源的支援，請您在 [搜尋服務意見反應論壇](https://feedback.azure.com/forums/263029-azure-search/)上提供寶貴意見。

## <a name="indexers-and-data-sources"></a>索引子和資料來源
您可以使用下列方式安裝及設定 Azure SQL 索引子︰

* [Azure 入口網站](https://portal.azure.com)中的匯入資料精靈
* Azure 搜尋服務 [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)
* Azure 搜尋服務 [REST API](http://go.microsoft.com/fwlink/p/?LinkID=528173)

在本文中，我們會使用 REST API 來示範如何建立和管理**索引子**與**資料來源**。

**資料來源**能指定哪項資料要編製索引、存取資料需要哪些認證，以及哪些政策能有效識別資料變更 (新增、修改或刪除的資料列)。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

**索引子** 是一種用來連接資料來源與目標搜尋索引的資源。 索引子的使用方式如下：

* 執行資料的一次性複製以填入索引。
* 依照排程使用資料來源中的變更來更新索引。
* 視需要執行隨選作業更新索引。

## <a name="when-to-use-azure-sql-indexer"></a>使用 Azure SQL 索引子的時機
使用 Azure SQL 索引子適當與否，取決於一些資料相關因素。 如果您的資料符合下列要求，您就可以使用 Azure SQL 索引子：

* 所有資料皆來自單一資料表或檢視
  * 如果資料散布在多個資料表中，您可以建立一個檢視，並且將該檢視與索引子搭配使用。 但是，若您使用檢視，您將不能使用 SQL Server 的整合變更偵測。 如需詳細資訊，請參閱[本節](#CaptureChangedRows)。
* 索引子支援資料來源中所使用的資料類型。 支援大部分但並非所有的 SQL 類型。 如需詳細資料，請參閱 [對應 Azure 搜尋服務內的資料來源](http://go.microsoft.com/fwlink/p/?LinkID=528105)。
* 當資料列變更時，您不需要幾近即時索引更新。
  * 索引子可以最多每隔 5 分鐘重新索引您的資料表。 若您經常變更資料，且變更需要在幾秒或幾分鐘內反映到索引中，我們建議您直接使用 [Azure 搜尋服務索引 API](https://msdn.microsoft.com/library/azure/dn798930.aspx) 。
* 若您擁有大量資料集且計畫按照排程執行索引子，您的結構描述讓我們能夠有效識別已變更 (和已刪除 (若適用)) 的資料列。 如需更多詳細資料，請參閱下列的「擷取變更及刪除的資料列」。
* 資料列中的索引欄位大小不得超過 Azure 搜尋服務索引要求的大小上限 (16 MB)。

## <a name="create-and-use-an-azure-sql-indexer"></a>建立和使用 Azure SQL 索引子
首先，建立資料來源：

    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


您可以從 [Azure 傳統入口網站](https://portal.azure.com)取得連接字串；使用 `ADO.NET connection string` 選項。

然後，建立目標 Azure 搜尋服務索引 (如果您尚未建立)。 您可以使用[入口網站 UI](https://portal.azure.com) 或[建立索引 API](https://msdn.microsoft.com/library/azure/dn798941.aspx) 來建立索引。 請確定目標索引的結構描述可與來源資料表的結構描述相容 - 請參閱 [SQL 和 Azure 搜尋服務資料類型之間的對應](#TypeMapping)。

最後，利用命名及參考資料來源和目標索引建立索引子。

    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

以這種方式建立索引子不需依照排程。 索引子一旦建立好就會自動執行。 您可以在任何時候使用 **執行索引子** 要求再執行一次：

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

您可以自訂數個層面的索引子行為，例如，批次處理大小，以及在索引子執行失敗前可略過多少份文件。 如需詳細資訊，請參閱[建立索引子 API](https://msdn.microsoft.com/library/azure/dn946899.aspx)。

在您可能需要允許 Azure 服務以連線您的資料庫的時候。 如需瞭解如何執行連線的指示，請參閱 [從 Azure連線](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 。

若要監視索引子狀態及執行歷程紀錄 (項目索引編製數量、失敗等)，請使用 **索引子狀態** 要求：

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

回應看起來應該如下所示：

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

執行歷程記錄包含多達 50 個最近完成的執行，以倒序的方式進行排序 (因此最新的執行會排在回應中的第一位)。
有關回應的的其他資訊可在 [取得索引子狀態](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>依照排程執行索引子
您也可以排列索引子，依照排程定期執行。 若要執行此工作，請在建立或更新索引子時新增**排程**屬性。 下方範例顯示以 PUT 要求更新索引子：

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**間隔** 參數是必需的。 間隔指兩個連續索引子開始執行的時間。 允許的最小間隔為 5 分鐘；最長間隔為一天。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 間隔的模式為： `P(nD)(T(nH)(nM))`。 範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 個小時。

選用的 **startTime** 表示排定執行應開始的時間。 如果省略選用時間，則會使用您目前的 UTC 時間。 開始時間可以設定為過去的時間 (在這種情況下，可以假設索引子從 startTime 已持續執行一段時間，以排定第一次執行的時間)。  

索引子一次僅能執行一個。 如果索引子在排定執行的時間已在運作，其執行會延遲到下一個排定的時間。

讓我們參考一個範例，讓您更具體了解詳情。 我們假設下列排程已設定為每小時執行：

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

排程狀況如下：

1. 第一次索引子執行開始時間約於 2015年 3 月 1 日 上午 12:00 UTC。
2. 假設此執行需要 20 分鐘 (或任何小於 1 小時的時間)。
3. 第二次執行開始時間約於 2015 年 3 月 1 日 上午 1:00。
4. 現在，假設此執行需要超過一小時 (例如 70 分鐘)，因此會在上午 2:10 左右完成。
5. 目前為上午 2:00，為第三個執行的開始時間。 不過，因為從上午 1 點的第二次執行 仍在執行，會略過第三次執行。 第三次執行時間改於上午 3 點開始。

您可以使用 **PUT 索引子** 要求，加入、 變更或刪除現有之索引子的排程。

<a name="CaptureChangedRows"></a>

## <a name="capturing-new-changed-and-deleted-rows"></a>擷取新增、變更和刪除的資料列
如果資料表有許多資料列，您應該使用資料變更偵測原則。 變更偵測能夠僅擷取新增或變更的資料列，而不必重新建立整份資料表的索引，是極有效率的方式。

### <a name="sql-integrated-change-tracking-policy"></a>SQL 整合變更追蹤原則
如果您的 SQL 資料庫支援 [變更追蹤](https://msdn.microsoft.com/library/bb933875.aspx)，則建議您使用 **SQL 整合式變更追蹤原則**。 這是最有效率的原則。 此外，它可讓 Azure 搜尋服務識別出已刪除的資料列，而不需在資料表中加入明確的「虛刪除」資料行。

下列的 SQL Server 資料庫版本支援整合變更追蹤：

* SQL Server 2008 R2 和更高版本 (若您使用 Azure VM 上 SQL Server )。
* Azure SQL Database  V12 (若您使用 Azure SQL Database )。

使用 SQL 整合式變更追蹤原則時，請不要指定個別的資料刪除偵測原則，因為本原則已內建支援刪除資料列的識別。

這項原則僅可用於資料表，無法用於檢視表。 您必須先啟用正在使用之資料表的變更追蹤，才能使用這項原則。 如需指示，請參閱 [啟用和停用變更追蹤](https://msdn.microsoft.com/library/bb964713.aspx) 。

若要使用此原則，請以下列方式建立或更新您的資料來源：

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>上限標準變更偵測原則
雖然建議使用 SQL 整合變更追蹤原則，但它只能搭配資料表使用，不能搭配檢視。 若您使用檢視，請考慮使用上限標準原則。 您可以使用此原則，若您的資料表或檢視包含之資料行符合下列準則：

* 所有插入都有指定資料行的值。
* 所有項目更新變更資料行的值。
* 每次插入或更新都會增加此資料行的值。
* 具有下列 WHERE 和 ORDER BY 子句的查詢可以有效率地執行︰`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`。

> [!IMPORTANT] 
> 我們強烈建議使用 **rowversion** 資料行來變更追蹤。 如果使用其他任何資料類型，就無法保證變更追蹤會擷取與索引子查詢同時執行之交易中發生的所有變更。

若要使用高標原則，請以下列方式建立或更新您的資料來源：

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> 如果來源資料表沒有上限標準資料行的索引，SQL 索引子所使用的查詢可能會逾時。 特別是，當資料表包含許多資料列時，`ORDER BY [High Water Mark Column]` 子句需要索引才能有效率地執行。
>
>

如果您遇到逾時錯誤，您可以使用 `queryTimeout` 索引子組態設定，將查詢逾時設定為高於預設逾時 (5 分鐘) 的值。 例如，若要將逾時設定為 10 分鐘，請使用下列組態建立或更新索引子︰

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

您也可以停用 `ORDER BY [High Water Mark Column]` 子句。 不過不建議您這麼做，因為如果索引子的執行因發生錯誤而中斷，索引子必須在稍後執行時重新處理所有資料列，即便索引子在中斷發生當時已幾乎處理好所有資料列，也是如此。 若要停用 `ORDER BY` 子句，請在索引子定義中使用 `disableOrderByHighWaterMarkColumn` 設定︰  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>虛刪除資料行刪除偵測原則
當從來源資料表中刪除資料列時，您應該也想刪除在搜尋索引內的那些資料列。 若您使用 SQL 整合變更追蹤原則，就能幫您處理這件工作。 但是，上限標準變更追蹤原則無法幫助您刪除資料列。 怎麼辦？

若資料列已實際從資料表內移除，Azure 搜尋服務便無法推斷出不復存在的記錄。  不過，您可以使用「虛刪除」技術，以邏輯方式刪除資料列，而不需從資料表加以移除。 在資料表或檢視中新增資料行，並使用該資料行將資料列標記為已刪除。

當您使用虛刪除技術時，可以在建立或升級資料來源時，按照下列方式指定虛刪除原則：

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**softDeleteMarkerValue** 必須為一個字串 (使用代表實際值的字串)。 例如，如果您有整數資料行，且其中的已刪除資料列標記為值 1，請使用 `"1"`。 如果您有 BIT 資料行，且其中的已刪除資料列標記為布林值 true，則請使用 `"True"`。

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>SQL 資料類型與 Azure 搜尋服務資料類型之間的對應
| SQL 資料類型 | 允許的目標索引欄位類型 | 注意事項 |
| --- | --- | --- |
| bit |Edm.Boolean、Edm.String | |
| int、smallint、tinyint |Edm.Int32、Edm.Int64、Edm.String | |
| bigint |Edm.Int64、Edm.String | |
| real、float |Edm.Double、Edm.String | |
| smallmoney、money 十進位數值 |Edm.String |Azure 搜尋服務不支援將十進位類型轉換為 Edm.Double，因為這麼做會降低準確度。 |
| char、nchar、varchar、nvarchar |Edm.String<br/>Collection(Edm.String) |如果 SQL 字串代表下列 JSON 字串陣列，該字串可用來填入 Collection(Edm.String) 欄位：`["red", "white", "blue"]` |
| smalldatetime、datetime、datetime2、date、datetimeoffset |Edm.DateTimeOffset、Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |僅支援使用 SRID 4326 (預設) 之 POINT 類型的 geography 執行個體。 |
| rowversion |N/A |資料列版本的資料行無法儲存在搜尋索引中，但可用於追蹤變更。 |
| time、timespan、binary、varbinary、image、xml、geometry、CLR 類型 |N/A |不支援 |

## <a name="configuration-settings"></a>組態設定
SQL 索引子公開數個組態設定︰

| 設定 | 資料類型 | 目的 | 預設值 |
| --- | --- | --- | --- |
| queryTimeout |字串 |設定 SQL 查詢執行的逾時 |5 分鐘 ("00:05:00") |
| disableOrderByHighWaterMarkColumn |布林 |導致上限標準原則所使用的 SQL 查詢省略 ORDER BY 子句。 請參閱[上限標準原則](#HighWaterMarkPolicy) |false |

這些設定會用於索引子定義中的 `parameters.configuration` 物件。 例如，若要將查詢逾時設定為 10 分鐘，請使用下列組態建立或更新索引子︰

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>常見問題集
**問：** 在 Azure 服務的 IaaS 模擬器上執行 SQL 資料庫時，能夠使用 Azure SQL 索引子嗎？

答： 會。 不過，您需要允許搜尋服務連接到資料庫。 如需詳細資訊，請參閱[在 Azure VM 上設定從 Azure 搜尋服務索引子到 SQL Server 的連線](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。

**問：** 在內部部署執行 SQL 資料庫時，能夠使用 Azure SQL 索引子嗎？

答：我們不建議或不支援此功能，因為使用此功能時，需要您的網際網路流量開啟資料庫。

**問：** 若不使用 Azure 服務的 IaaS 模擬器執行 SQL Server ，能夠使用其他資料庫的 Azure SQL 索引子嗎？

答：我們並不支援這樣的案例，因為我們尚未測試 SQL Server 以外之資料庫的索引子。  

**問：** 可以建立多個依照排程執行的索引子嗎？

答： 會。 但是一次只能在一個節點上執行一個索引子。 如果您需要多個同時執行的索引子，請考慮將搜尋服務調整大於一個搜尋單位。

**問：** 執行一個索引子會影響我的查詢工作負載嗎？

答： 會。 索引子會在您搜尋服務中的其中一個節點執行，且節點上的資源會在索引及服務查詢流量和其他 API 要求之間共用。 如果您密集執行索引及查詢工作負載，且經常遇到 503 錯誤或回應次數增加，請考慮調整您的搜尋服務。

