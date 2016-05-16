<properties
pageTitle="Outlook.com | Microsoft Azure"
description="使用 Azure App Service 建立邏輯應用程式。Outlook.com 連接器可讓您管理您的郵件、行事曆和連絡人。您可以執行各種動作，例如傳送郵件、排程會議、新增連絡人等等。"
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# 開始使用 Outlook.com 連接器

Outlook.com 連接器可讓您管理您的郵件、行事曆和連絡人。您可以執行各種動作，例如傳送郵件、排程會議、新增連絡人等等。

您可從下列位置使用 Outlook.com 連接器︰

- [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flow](http://flow.microsoft.com)  

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

Outlook.com 連接器可當成動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 Outlook.com 連接器提供下列動作及/或觸發程序：

### Outlook.com 動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|從資料夾擷取電子郵件|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|傳送電子郵件|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|依識別碼刪除電子郵件|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|將電子郵件標示為已讀取|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|回覆電子郵件|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|依識別碼擷取電子郵件附件|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|傳送具有多個選項的電子郵件，並等候收件者選擇其中一個選項來回應。|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|傳送核准電子郵件，並等候收件者的回應。|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|擷取行事曆|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|從行事曆擷取項目|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|建立新的事件|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|從行事曆擷取特定項目|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|刪除行事曆項目|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|部分更新行事曆項目|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|擷取連絡人資料夾|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|從連絡人資料夾中擷取連絡人|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|建立新的連絡人|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|從連絡人資料夾中擷取特定連絡人|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|刪除連絡人|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|部分更新連絡人|
### Outlook.com 觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|
|事件快要開始時|即將來臨的行事曆事件啟動時觸發流程|
|有新的電子郵件時|新的電子郵件送達時觸發流程|
|有新的項目時|建立新的行事曆項目時觸發|
|更新項目時|修改行事曆項目時觸發|


## 建立 Outlook.com 的連線
若要使用 Outlook.com 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 Outlook.com 認證|
建立連接後，您就可以用它執行動作，並接聽本文所述的觸發程序。

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## Outlook.com 的參考
適用的版本：1.0

## OnUpcomingEvents
事件快要開始時：即將來臨的行事曆事件啟動時會觸發流程

```GET: /Events/OnUpcomingEvents```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|query|無|行事曆的唯一識別碼|
|lookAheadTimeInMinutes|integer|no|query|15|即將來臨的事件的倒數時間 (以分鐘為單位)。|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|202|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## GetEmails
取得電子郵件：從資料夾擷取電子郵件

```GET: /Mail```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderPath|字串|no|query|Inbox|要從中擷取電子郵件的資料夾路徑 (預設值：'Inbox')|
|top|integer|no|query|10|要擷取的電子郵件數目 (預設值：10)|
|fetchOnlyUnread|布林值|no|query|true|只擷取未讀取的電子郵件嗎？|
|includeAttachments|布林值|no|query|false|如果設為 true，附件會和電子郵件一起擷取。|
|searchQuery|字串|no|query|無|搜尋 query 來篩選電子郵件|
|skip|integer|no|query|0|要略過的電子郵件數目 (預設值：0)|
|skipToken|字串|no|query|無|跳過語彙基元來提取新頁面|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## SendEmail
傳送電子郵件︰傳送電子郵件

```POST: /Mail```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|無|電子郵件|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## DeleteEmail
刪除電子郵件：依識別碼刪除電子郵件

```DELETE: /Mail/{messageId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|messageId|字串|yes|路徑|無|要刪除的電子郵件識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## MarkAsRead
標示為已讀取：將電子郵件標示為已讀取

```POST: /Mail/MarkAsRead/{messageId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|messageId|字串|yes|路徑|無|要標示為已讀取的電子郵件識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## ReplyTo
回覆電子郵件︰回覆電子郵件

```POST: /Mail/ReplyTo/{messageId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|messageId|字串|yes|路徑|無|要回覆的電子郵件識別碼|
|comment|字串|yes|query|無|回覆意見|
|replyAll|布林值|no|query|false|回覆所有收件者|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## GetAttachment
取得附件：依識別碼擷取電子郵件附件

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|messageId|字串|yes|路徑|無|電子郵件識別碼|
|attachmentId|字串|yes|路徑|無|要下載的附件的識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## OnNewEmail
有新的電子郵件時：新的電子郵件送達時就會觸發流程

```GET: /Mail/OnNewEmail```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderPath|字串|no|query|Inbox|要擷取的電子郵件資料夾 (預設值：Inbox)|
|to|字串|no|query|無|收件者電子郵件地址|
|from|字串|no|query|無|寄件者地址|
|importance|字串|no|query|正常|電子郵件的重要性 (High、Normal、Low) (預設值：Normal)|
|fetchOnlyWithAttachment|布林值|no|query|false|只擷取含有附件的電子郵件|
|includeAttachments|布林值|no|query|false|包含附件|
|subjectFilter|字串|no|query|無|要在主旨中尋找的字串|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|202|已接受|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## SendMailWithOptions
傳送有選項的電子郵件：傳送具有多個選項的電子郵件，並等候收件者回應其中一個選項。

```POST: /mailwithoptions/$subscriptions```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |yes|body|無|有選項電子郵件的訂用帳戶要求|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|201|已建立訂用帳戶|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## SendApprovalMail
傳送核准電子郵件：傳送核准電子郵件，並等候收件者的回應。

```POST: /approvalmail/$subscriptions```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |yes|body|無|核准電子郵件的訂用帳戶要求|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|201|已建立訂用帳戶|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## CalendarGetTables
取得行事曆︰擷取行事曆

```GET: /datasets/calendars/tables```

這個呼叫沒有參數
#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## CalendarGetItems
取得事件：從行事曆擷取項目

```GET: /datasets/calendars/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|要擷取的行事曆的唯一識別碼|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## CalendarPostItem
建立事件︰建立新的事件

```POST: /datasets/calendars/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|item| |yes|body|無|要建立的行事曆項目|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## CalendarGetItem
取得事件：從行事曆擷取特定項目

```GET: /datasets/calendars/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|id|字串|yes|路徑|無|要擷取的行事曆項目的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## CalendarDeleteItem
刪除事件：刪除行事曆項目

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|id|字串|yes|路徑|無|要刪除的行事曆項目的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## CalendarPatchItem
更新事件：部分更新行事曆項目

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|id|字串|yes|路徑|無|要更新的行事曆項目的唯一識別碼|
|item| |yes|body|無|要更新的行事曆項目|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## CalendarGetOnNewItems
有新的項目時：建立新的行事曆項目時會觸發

```GET: /datasets/calendars/tables/{table}/onnewitems```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## CalendarGetOnUpdatedItems
有更新的項目時：修改行事曆項目時會觸發

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## ContactGetTables
取得連絡人資料夾︰擷取連絡人資料夾

```GET: /datasets/contacts/tables```

這個呼叫沒有參數
#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## ContactGetItems
取得連絡人：從連絡人資料夾中擷取連絡人

```GET: /datasets/contacts/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|要擷取的連絡人資料夾的唯一識別碼|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## ContactPostItem
建立連絡人︰建立新的連絡人

```POST: /datasets/contacts/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|item| |yes|body|無|要建立的連絡人|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## ContactGetItem
取得連絡人：從連絡人資料夾中擷取特定連絡人

```GET: /datasets/contacts/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|id|字串|yes|路徑|無|要擷取的連絡人的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## ContactDeleteItem
刪除連絡人︰刪除連絡人

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|id|字串|yes|路徑|無|要刪除的連絡人的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## ContactPatchItem
更新連絡人：部分更新連絡人

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|id|字串|yes|路徑|無|要更新的連絡人的唯一識別碼|
|item| |yes|body|無|要更新的連絡人項目|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## 物件定義 

### TriggerBatchResponse[IDictionary[String,Object]]


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### Object


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|



### SendMessage


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|附件|array|否 |
|從|字串|否 |
|副本|字串|否 |
|密件副本|字串|否 |
|主旨|字串|是 |
|內文|字串|是 |
|重要性|字串|否 |
|IsHtml|布林值|否 |
|收件人|字串|是 |



### SendAttachment


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|@odata.type|字串|否 |
|名稱|字串|是 |
|ContentBytes|字串|是 |



### ReceiveMessage


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|識別碼|字串|否 |
|IsRead|布林值|否 |
|HasAttachment|布林值|否 |
|DateTimeReceived|字串|否 |
|附件|array|否 |
|從|字串|否 |
|副本|字串|否 |
|密件副本|字串|否 |
|主旨|字串|是 |
|內文|字串|是 |
|重要性|字串|否 |
|IsHtml|布林值|否 |
|收件人|字串|是 |



### ReceiveAttachment


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|識別碼|字串|是 |
|ContentType|字串|是 |
|@odata.type|字串|否 |
|名稱|字串|是 |
|ContentBytes|字串|是 |



### DigestMessage


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|主旨|字串|是 |
|內文|字串|否 |
|重要性|字串|否 |
|Digest|array|是 |
|附件|array|否 |
|收件人|字串|是 |



### TriggerBatchResponse[ReceiveMessage]


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### DataSetsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|表格式|沒有定義|否 |
|blob|沒有定義|否 |



### TabularDataSetsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|來源|字串|否 |
|displayName|字串|否 |
|urlEncoding|字串|否 |
|tableDisplayName|字串|否 |
|tablePluralName|字串|否 |



### BlobDataSetsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|來源|字串|否 |
|displayName|字串|否 |
|urlEncoding|字串|否 |



### TableMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|否 |
|title|字串|否 |
|x-ms-permission|字串|否 |
|x-ms-capabilities|沒有定義|否 |
|結構描述|沒有定義|否 |



### TableCapabilitiesMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|sortRestrictions|沒有定義|否 |
|filterRestrictions|沒有定義|否 |
|filterFunctions|array|否 |



### TableSortRestrictionsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|sortable|布林值|否 |
|unsortableProperties|array|否 |
|ascendingOnlyProperties|array|否 |



### TableFilterRestrictionsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|filterable|布林值|否 |
|nonFilterableProperties|array|否 |
|requiredProperties|array|否 |



### OptionsEmailSubscription


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|NotificationUrl|字串|否 |
|訊息|沒有定義|否 |



### MessageWithOptions


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|主旨|字串|是 |
|選項|字串|是 |
|內文|字串|否 |
|重要性|字串|否 |
|附件|array|否 |
|收件人|字串|是 |



### SubscriptionResponse


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|字串|否 |
|resource|字串|否 |
|notificationType|字串|否 |
|notificationUrl|字串|否 |



### ApprovalEmailSubscription


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|NotificationUrl|字串|否 |
|訊息|沒有定義|否 |



### ApprovalMessage


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|主旨|字串|是 |
|選項|字串|是 |
|內文|字串|否 |
|重要性|字串|否 |
|附件|array|否 |
|收件人|字串|是 |



### ApprovalEmailResponse


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|SelectedOption|字串|否 |



### TablesList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### 資料表


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|否 |
|DisplayName|字串|否 |



### 項目


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|ItemInternalId|字串|否 |



### CalendarItemsList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### CalendarItem


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|ItemInternalId|字串|否 |



### ContactItemsList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### ContactItem


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|ItemInternalId|字串|否 |



### DataSetsList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### DataSet


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|否 |
|DisplayName|字串|否 |


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->