<properties
	pageTitle="使用要求和回應動作 | Microsoft Azure"
	description="Azure 邏輯應用程式中要求和回應之觸發程序與動作的概觀"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# 開始使用要求和回應元件

透過邏輯應用程式中的要求和回應元件，您可以即時回應事件，像是︰

- 透過邏輯應用程式使用內部部署資料庫中的資料回應 HTTP 要求。
- 從外部 Webhook 事件觸發邏輯應用程式。
- 從另一個邏輯應用程式內使用要求和回應動作呼叫邏輯應用程式

若要使用邏輯應用程式中的要求和回應動作來開始作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

---

## 使用 HTTP 要求觸發程序

觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。[深入了解觸發程序](connectors-overview.md)。

以下是如何在邏輯應用程式設計工具中設定 HTTP 要求的範例順序。

1. 將 [要求 - 收到 HTTP 要求時] 觸發程序新增到您的邏輯應用程式
	- 您可以選擇性地提供 JSON 結構描述 (使用 [jsonschema.net](http://jsonschema.net) 之類的工具) 來做為要求本文。這可讓設計工具在 HTTP 要求中產生屬性的權杖。
1. 新增另一個動作以儲存邏輯應用程式
1. 在儲存之後，您可以從要求卡片取得 HTTP 要求 URL
1. 針對 URL 的 HTTP POST (您可以使用 [Postman](https://www.getpostman.com/) 之類的工具) 將會觸發邏輯應用程式

>[AZURE.NOTE] 如果您未定義回應動作，系統會立即將 `202 ACCEPTED` 回應傳回給呼叫者。您可以使用回應動作以自訂回應。

![回應觸發程序](./media/connectors-native-reqres/using-trigger.png)

## 使用 HTTP 回應動作
	
HTTP 回應動作只適用於在 HTTP 要求所觸發的工作流程中使用時。如果您未定義回應動作，系統會立即將 `202 ACCEPTED` 回應傳回給呼叫者。回應動作可在工作流程的任何步驟新增。邏輯應用程式只會讓連入要求針對回應開放 1 分鐘。1 分鐘後，如果工作流程未傳送任何回應 (且定義中存在回應動作)，則系統會將 `504 GATEWAY TIMEOUT` 傳回給呼叫者。以下是新增 HTTP 回應動作的方法︰

1. 選取 [新增步驟] 按鈕
1. 選擇 [新增動作]
1. 在動作搜尋方塊中，輸入「Response」以列出回應動作

	![選取回應動作](./media/connectors-native-reqres/using-action-1.png)

1. 新增 HTTP 回應訊息所需的任何參數

	![完成回應動作](./media/connectors-native-reqres/using-action-2.png)

1. 按一下工具列左上角的 [儲存]，邏輯應用程式便會儲存並發佈 (啟動)

---

## 技術詳細資訊

以下是此連接器支援的觸發程序和動作的詳細資料。

## 要求觸發程序

觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。[深入了解觸發程序](connectors-overview.md)。 只有一個要求觸發程序。

|觸發程序|說明|
|---|---|
|要求|收到 HTTP 要求時|

## 回應動作

動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作。](connectors-overview.md) 只有一個回應動作，此動作只能在伴隨要求觸發程序時使用。

|動作|說明|
|---|---|
|Response|傳回相互關聯的 HTTP 要求的回應|

### 動作詳細資料

下面是關於每個動作、其必要和選擇性輸入欄位，以及與其使用方式相關聯的對應輸出詳細資料的資訊。

#### 要求觸發程序
連入 HTTP 要求的觸發程序。* 代表必要欄位。

|顯示名稱|屬性名稱|說明|
|---|---|---|
|JSON 結構描述|結構描述|HTTP 要求本文的 JSON 結構描述|
<br>

**輸出詳細資料**

要求

|屬性名稱|資料類型|說明|
|---|---|---|
|標頭|物件|要求標頭|
|內文|物件|要求物件|

#### 回應動作

HTTP 回應。* 代表必要欄位。

|顯示名稱|屬性名稱|說明|
|---|---|---|
|狀態碼 *|StatusCode|HTTP 狀態碼|
|標頭|headers|要包含的任何回應標頭的 JSON 物件|
|內文|body|回應本文|

## 後續步驟

以下是有關如何推動邏輯應用程式的詳細資料和我們的社群。

## 建立邏輯應用程式

立即試用平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。您可以查看我們的 [API 清單](apis-list.md)，以探索邏輯應用程式中其他可用的連接器。

<!---HONumber=AcomDC_0720_2016-->