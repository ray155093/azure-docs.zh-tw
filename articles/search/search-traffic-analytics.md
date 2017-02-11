---
title: "Azure 搜尋服務的搜尋流量分析 | Microsoft Docs"
description: "啟用 Azure 搜尋服務 (Microsoft Azure 上裝載的雲端搜尋服務) 的搜尋流量分析，來深入分析您的使用者與資料。"
services: search
documentationcenter: 
author: bernitorres
manager: pablocas
editor: 
ms.assetid: b31d79cf-5924-4522-9276-a1bb5d527b13
ms.service: search
ms.devlang: multiple
ms.workload: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: betorres
translationtype: Human Translation
ms.sourcegitcommit: ebfed89674dc132bd5d93f34a8b5ed5ab12bd73e
ms.openlocfilehash: cd6c1d094f8601a06642a2a84ef1234a29a8f058

---

# <a name="enabling-and-using-search-traffic-analytics"></a>啟用和使用搜尋流量分析
搜尋流量分析是一項 Azure 搜尋服務功能，可讓您掌握您的搜尋服務，並深入分析您的使用者及其行為。 當您啟用此功能時，您的搜尋服務資料會複製到您選擇的儲存體帳戶。 此資料包括搜尋服務記錄檔和彙總作業度量，可供您處理和操作來進一步分析。

## <a name="how-to-enable-search-traffic-analytics"></a>如何啟用搜尋流量分析
儲存體帳戶必須與您的搜尋服務位於相同的區域和訂用帳戶中。

> [!IMPORTANT]
> 標準收費適用此儲存體帳戶
>
>

您可以在入口網站上或透過 PowerShell 啟用搜尋流量分析。 啟用之後，資料會在 5-10 分鐘內開始傳送至您的儲存體帳戶，再傳送至這兩個 Blob 容器：

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a-using-the-portal"></a>A. 使用入口網站
在 [Azure 入口網站](http://portal.azure.com)中開啟 Azure 搜尋服務。 在 [設定] 下方找出 [搜尋流量分析] 選項。

![][1]

將 [狀態] 變更為 [開啟] 、選取要使用的 Azure 儲存體帳戶，然後選擇要複製的資料：記錄檔、度量或兩者。 建議您複製記錄檔和度量。
您可以將資料的保留原則設定為 1 到 365 天。 如果您不想要無限期地保留資料，請將保留 (天數) 設定為 0。

![][2]

### <a name="b-using-powershell"></a>B. 使用 PowerShell
首先，請確定您已安裝最新的 [Azure PowerShell Cmdlet](https://github.com/Azure/azure-powershell/releases) 。

然後，取得您搜尋服務和儲存體帳戶的資源識別碼。 瀏覽至 [設定] -> [屬性] -> [ResourceId]，即可在入口網站中找到它。

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>了解資料
資料會以 JSON 格式儲存於 Azure 儲存體 Blob 中。

每個容器每小時會有一個 Blob。

範例路徑： `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>記錄檔
記錄檔 Blob 包含您的搜尋服務流量記錄檔。
每個 Blob 會一個名為 **記錄** 的根物件，其中包含記錄檔物件的陣列。
在每個 blob 中，同一小時內發生的所有作業都有記錄。

#### <a name="log-schema"></a>記錄檔結構描述
| 名稱 | 類型 | 範例 | 注意事項 |
| --- | --- | --- | --- |
| 分析 |datetime |"2015-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的 ResourceId |
| operationName |string |"Query.Search" |作業的名稱 |
| operationVersion |string |"2016-09-01" |使用的 api-version |
| category |string |"OperationLogs" |常數 |
| resultType |string |"Success" |可能的值：Success 或 Failure |
| resultSignature |int |200 |HTTP 結果碼 |
| durationMS |int |50 |作業的持續時間 (以毫秒為單位) |
| properties |物件 |請參閱下表 |包含作業特定資料的物件 |

#### <a name="properties-schema"></a>屬性結構描述
| 名稱 | 類型 | 範例 | 注意事項 |
| --- | --- | --- | --- |
| 說明 |string |"GET /indexes('content')/docs" |作業的端點 |
| 查詢 |string |"?search=AzureSearch&$count=true&api-version=2016-09-01" |查詢參數 |
| 文件 |int |42 |處理的文件數目 |
| IndexName |string |"testindex" |與作業相關聯的索引名稱 |

### <a name="metrics"></a>度量
度量 Blob 包含您搜尋服務的彙總值。
每個檔案會有一個名為 **記錄** 的根物件，其中包含計量物件的陣列。 這個根物件包含有可用資料的每分鐘計量。

可用的度量：

* SearchLatency︰搜尋服務處理搜尋查詢所需的時間 (每分鐘彙總一次)。
* SearchQueriesPerSecond︰每秒接收的搜尋查詢數 (每分鐘彙總一次)。
* ThrottledSearchQueriesPercentage︰已節流處理的搜尋查詢百分比 (每分鐘彙總一次)。

> [!IMPORTANT]
> 傳送的查詢太多，因而耗盡服務佈建的資源容量時，就會進行節流處理。 請考慮新增更多複本到您的服務。
>
>

#### <a name="metrics-schema"></a>度量結構描述
| 名稱 | 類型 | 範例 | 注意事項 |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的資源識別碼 |
| metricName |string |"Latency" |度量的名稱 |
| 分析 |datetime |"2015-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| average |int |64 |度量時間間隔中原始範例的平均值 |
| minimum |int |37 |度量時間間隔中原始範例的最小值 |
| maximum |int |78 |度量時間間隔中原始範例的最大值 |
| total |int |258 |度量時間間隔中原始範例的總和值 |
| 計數 |int |4 |用來產生度量的原始樣本數 |
| timegrain |string |"PT1M" |採用 ISO 8601 的度量時間粒紋 |

每隔一分鐘就會回報所有計量。 每個度量會顯示每分鐘的最小值、最大值和平均值。

以 SearchQueriesPerSecond 度量來說，最小值是該分鐘內已註冊的每秒搜尋查詢次數最低值。 最大值依此類推。 平均值是一分鐘的彙總。
假設一分鐘內有這種情況：有 1 秒出現極高的負載，這是 SearchQueriesPerSecond 的最大值，接著有 58 秒的平均負載，最後的 1 秒只有一個查詢，而這會是最小值。

對於 ThrottledSearchQueriesPercentage，最小值、最大值、平均值和總計全是相同的值：在一分鐘內的搜尋查詢總數中，已節流處理的搜尋查詢百分比。

## <a name="analyzing-your-data"></a>分析您的資料
資料會在您的儲存體帳戶中，而我們鼓勵您以最適合您狀況的方式來探索此資料。

我們建議您一開始使用 [Power BI](https://powerbi.microsoft.com) 來探索和視覺化資料。 您可以輕鬆地連線到您的 Azure 儲存體帳戶，並快速開始分析您的資料。

#### <a name="power-bi-online"></a>Power BI 線上版
[Power BI 內容套件](https://app.powerbi.com/getdata/services/azure-search)：建立 Power BI 儀表板，及一組 Power BI 報告，且該報告會自動顯示您資料，並以視覺化的方式提供您搜尋服務相關的深入分析。 請參閱 [內容套件說明網頁](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/)。

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop
[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop)：探索您的資料，並將您的資料視覺化。 請參閱下一節的入門查詢︰

1. 開啟新的 PowerBI Desktop 報表
2. 選取 [取得資料] -> [更多...]

    ![][5]
3. 依序選取 [Microsoft Azure Blob 儲存體] 和 [連接]

    ![][6]
4. 輸入您儲存體帳戶的名稱和帳戶金鑰
5. 選取 [insight-logs-operationlogs] 和 [insights-metrics-pt1m]，接著按一下 [編輯]
6. 當 [查詢編輯器] 開啟時，請確定已選取左方的 [insight-logs-operationlogs]。 現在依序選取 [檢視]-> [進階編輯器] 來開啟 [進階編輯器]

    ![][7]
7. 保留前兩行，並使用下列查詢來取代其餘部分：

   > # <a name="insights-logs-operationlogs--sourcenameinsights-logs-operationlogsdata"></a>"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
   > # <a name="sorted-rows--tablesortinsights-logs-operationlogsdate-modified-orderdescending"></a>"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
   > # <a name="kept-first-rows--tablefirstnsorted-rows744"></a>"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # <a name="removed-columns--tableremovecolumnskept-first-rowsname-extension-date-accessed-date-modified-date-created-attributes-folder-path"></a>"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # <a name="parsed-json--tabletransformcolumnsremoved-columnsjsondocument"></a>"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # <a name="expanded-content--tableexpandrecordcolumnparsed-json-content-records-records"></a>"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # <a name="expanded-records--tableexpandlistcolumnexpanded-content-records"></a>"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # <a name="expanded-records1--tableexpandrecordcolumnexpanded-records-records-time-resourceid-operationname-operationversion-category-resulttype-resultsignature-durationms-properties-time-resourceid-operationname-operationversion-category-resulttype-resultsignature-durationms-properties"></a>"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
   > # <a name="expanded-properties--tableexpandrecordcolumnexpanded-records1-properties-description-query-indexname-documents-description-query-indexname-documents"></a>"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
   > # <a name="renamed-columns--tablerenamecolumnsexpanded-propertiestime-datetime-resourceid-resourceid-operationname-operationname-operationversion-operationversion-category-category-resulttype-resulttype-resultsignature-resultsignature-durationms-duration"></a>"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
   > # <a name="added-custom2--tableaddcolumnrenamed-columns-queryparameters-each-uripartshttptmp--query"></a>"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
   > # <a name="expanded-queryparameters--tableexpandrecordcolumnadded-custom2-queryparameters-query-query1"></a>"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
   > # <a name="expanded-query1--tableexpandrecordcolumnexpanded-queryparameters-query1-search-skip-top-count-api-version-searchmode-filter-search-skip-top-count-api-version-searchmode-filter"></a>"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
   > # <a name="removed-columns1--tableremovecolumnsexpanded-query1operationversion"></a>"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
   > # <a name="changed-type--tabletransformcolumntypesremoved-columns1datetime-type-datetimezone-resourceid-type-text-operationname-type-text-category-type-text-resulttype-type-text-resultsignature-type-text-duration-int64type-description-type-text-query-type-text-indexname-type-text-documents-int64type-search-type-text-skip-int64type-top-int64type-count-type-logical-api-version-type-text-searchmode-type-text-filter-type-text"></a>"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
   > # <a name="inserted-date--tableaddcolumnchanged-type-date-each-datetimedatedatetime-type-date"></a>"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
   > # <a name="duplicated-column--tableduplicatecolumninserted-date-resourceid-copy-of-resourceid"></a>"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
   > # <a name="split-column-by-delimiter--tablesplitcolumnduplicated-columncopy-of-resourceidsplittersplittextbyeachdelimiter-null-truecopy-of-resourceid1-copy-of-resourceid2"></a>"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
   > # <a name="changed-type1--tabletransformcolumntypessplit-column-by-delimitercopy-of-resourceid1-type-text-copy-of-resourceid2-type-text"></a>"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
   > # <a name="removed-columns2--tableremovecolumnschanged-type1copy-of-resourceid1"></a>"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
   > # <a name="renamed-columns1--tablerenamecolumnsremoved-columns2copy-of-resourceid2-servicename"></a>"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
   > # <a name="lowercased-text--tabletransformcolumnsrenamed-columns1servicename-textlower"></a>"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
   > # <a name="added-custom--tableaddcolumnlowercased-text-daysfromtoday-each-durationdaysdatetimezoneutcnow---datetime"></a>"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
   > # <a name="changed-type2--tabletransformcolumntypesadded-customdaysfromtoday-int64type"></a>"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
   > 了嗎
   >
   > # <a name="changed-type2"></a>"Changed Type2"
   >
8. 按一下 [完成]
9. 現在從左方最後的查詢選取 [insights-metrics-pt1m]，然後再次開啟 [進階編輯器]。 保留前兩行，並使用下列查詢來取代其餘部分：

   > # <a name="insights-metrics-pt1m1--sourcenameinsights-metrics-pt1mdata"></a>"insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
   > # <a name="sorted-rows--tablesortinsights-metrics-pt1m1date-modified-orderdescending"></a>"Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
   > # <a name="kept-first-rows--tablefirstnsorted-rows744"></a>"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # <a name="removed-columns--tableremovecolumnskept-first-rowsname-extension-date-accessed-date-modified-date-created-attributes-folder-path"></a>"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # <a name="parsed-json--tabletransformcolumnsremoved-columnsjsondocument"></a>"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # <a name="expanded-content--tableexpandrecordcolumnparsed-json-content-records-records"></a>"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # <a name="expanded-records--tableexpandlistcolumnexpanded-content-records"></a>"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # <a name="expanded-records1--tableexpandrecordcolumnexpanded-records-records-resourceid-metricname-time-average-minimum-maximum-total-count-timegrain-resourceid-metricname-time-average-minimum-maximum-total-count-timegrain"></a>"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
   > # <a name="filtered-rows--tableselectrowsexpanded-records1-each-metricname--latency"></a>"Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
   > # <a name="removed-columns1--tableremovecolumnsfiltered-rowstimegrain"></a>"Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
   > # <a name="renamed-columns--tablerenamecolumnsremoved-columns1time-datetime-resourceid-resourceid-metricname-metricname-average-average-minimum-minimum-maximum-maximum-total-total-count-count"></a>"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
   > # <a name="changed-type--tabletransformcolumntypesrenamed-columnsresourceid-type-text-metricname-type-text-datetime-type-datetimezone-average-type-number-minimum-int64type-maximum-int64type-total-int64type-count-int64type"></a>"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
   > Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
   >
   > # <a name="changed-type1--tabletransformcolumntypesroundingaverage-type-number"></a>"Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
   > # <a name="inserted-date--tableaddcolumnchanged-type1-date-each-datetimedatedatetime-type-date"></a>"Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
   > 了嗎
   >
   > # <a name="inserted-date"></a>"Inserted Date"
   >
10. 按一下 [完成]，然後選取 [常用] 索引標籤中的 [關閉並套用]。

11. 您過去 30 天的資料現在已準備好提供取用。 請繼續進行，並建立一些[視覺效果](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/)。

## <a name="next-steps"></a>後續步驟
深入了解搜尋語法和查詢參數。 如需詳細資訊，請參閱 [搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx) 。

深入了解如何建立令人讚嘆的報告。 如需詳細資訊，請參閱[開始使用 Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/)

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png



<!--HONumber=Nov16_HO3-->


