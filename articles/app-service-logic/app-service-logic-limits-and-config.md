<properties
	pageTitle="邏輯應用程式的限制和設定 | Microsoft Azure"
	description="適用於 Logic Apps 之服務限制和設定值的概觀。"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="jeffhollan"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="jehollan"/>

# 邏輯應用程式的限制和設定

以下是 Azure Logic Apps 目前的限制和設定詳細資料的相關資訊。

## 限制

### HTTP 要求限制

以下是單一 HTTP 要求和/或連接器呼叫的限制

#### 逾時

|名稱|限制|注意事項|
|----|----|----|
|要求逾時|1 分鐘|[非同步模式](app-service-logic-create-api-app.md)或 [Until 迴圈](app-service-logic-loops-and-scopes.md)可以視需要抵銷|

#### 訊息大小

|名稱|限制|注意事項|
|----|----|----|
|訊息大小|50 MB|某些連接器和 API 可能不支援 50 MB。要求觸發程序最多可支援 25 MB|
|運算式評估限制|131,072 個字元|`@concat()`、`@base64()` 和 `string` 的長度不能超過此限制|

#### 重試原則

|名稱|限制|注意事項|
|----|----|----|
|重試次數|4|可以使用[重試原則參數](https://msdn.microsoft.com/zh-TW/library/azure/mt643939.aspx)進行設定|
|重試延遲上限|1 小時|可以使用[重試原則參數](https://msdn.microsoft.com/zh-TW/library/azure/mt643939.aspx)進行設定|
|重試延遲下限|20 分鐘|可以使用[重試原則參數](https://msdn.microsoft.com/zh-TW/library/azure/mt643939.aspx)進行設定|

### 執行持續時間和保留期

以下是單一邏輯應用程式的執行限制。

|名稱|限制|注意事項|
|----|----|----|
|執行持續時間|90 天||
|儲存體保留期|90 天|從執行開始時間算起|
|最小循環間隔|15 秒||
|最大循環間隔|500 天||


### 迴圈和解除批次處理限制

以下是單一邏輯應用程式的執行限制。

|名稱|限制|注意事項|
|----|----|----|
|ForEach 項目|10,000|您可以視需要使用[查詢動作](../connectors/connectors-native-query.md)篩選較大的陣列|
|反覆運算之前|10,000||
|SplitOn 項目|10,000||
|ForEach 平行處理原則|20|您可以藉由在 `foreach` 動作新增 `"operationOptions": "Sequential"` 以設定為循序 foreach|


### 輸送量限制

以下是單一邏輯應用程式執行個體的限制。

|名稱|限制|注意事項|
|----|----|----|
|每秒的觸發程序數目|100|可以視需要將工作流程分散在多個應用程式|

### 定義限制

以下是單一邏輯應用程式定義的限制。

|名稱|限制|注意事項|
|----|----|----|
|ForEach 中的動作|1|您可以視需要新增巢狀工作流程以擴充此項目|
|每個工作流程的動作數目|60|您可以視需要新增巢狀工作流程以擴充此項目|
|允許的動作巢狀深度|5|您可以視需要新增巢狀工作流程以擴充此項目|
|每個訂用帳戶每個區域的流程數目|1000||
|每個工作流程的觸發程序數目|10||
|每個運算式的字元數上限|8,192||
|`trackedProperties` 大小上限 (以字元為單位)|16,000|
|`action`/`trigger` 名稱限制|80||
|`description` 長度限制|256||
|`parameters` 限制|50||
|`outputs` 限制|10||

## 組態

### IP 位址

來自[連接器](../connectors/apis-list.md)的呼叫將會來自下面指定的 IP 位址。

直接來自邏輯應用程式的呼叫 (也就是透過 [HTTP](../connectors/connectors-native-http.md) 或 [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) 可能來自任何 [Azure 資料中心 IP 範圍](https://www.microsoft.com/zh-TW/download/details.aspx?id=41653)。

|邏輯應用程式區域|輸出 IP|
|-----|----|
|澳洲東部|40\.126.251.213|
|澳大利亞東南部|40\.127.80.34|
|巴西南部|191\.232.38.129|
|印度中部|104\.211.98.164|
|美國中部|40\.122.49.51|
|東亞|23\.99.116.181|
|美國東部|191\.237.41.52|
|美國東部 2|104\.208.233.100|
|日本東部|40\.115.186.96|
|日本西部|40\.74.130.77|
|美國中北部|65\.52.218.230|
|北歐|104\.45.93.9|
|美國中南部|104\.214.70.191|
|東南亞|13\.76.231.68|
|印度南部|104\.211.227.225|
|西歐|40\.115.50.13|
|印度西部|104\.211.161.203|
|美國西部|104\.40.51.248|


## 後續步驟  

- 若要開始使用 Logic Apps，請遵循[建立 Logic Apps ](app-service-logic-create-a-logic-app.md)教學課程。
- [檢視常見的範例和案例](app-service-logic-examples-and-scenarios.md)
- [您可以使用 Logic Apps 自動化商務程序](http://channel9.msdn.com/Events/Build/2016/T694)
- [了解如何整合您的系統與 Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0727_2016-->