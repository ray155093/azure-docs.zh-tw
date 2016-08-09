<properties
    pageTitle="在您的 Logic Apps 中新增 Office 365 Outlook 連接器 | Microsoft Azure"
    description="建立具有 Office 365 連接器的邏輯應用程式來啟用與 Office 365 的互動。例如：建立、編輯和更新連絡人及行事曆項目。"
    services=""    
    documentationCenter=""     
    authors="MandiOhlinger"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/26/2016"
ms.author="mandia"/>

# 開始使用 Office 365 Outlook 連接器 

Office 365 Outlook 連接器能夠與 Office 365 中的 Outlook 互動。使用此連接器來建立、編輯和更新連絡人和行事曆項目，也可取得、傳送及回覆電子郵件。

使用 Office 365 Outlook，您可以：

- 使用 Office 365 中的電子郵件和行事曆功能來建置您的工作流程。
- 使用觸發程序，在有新的電子郵件時、行事曆項目更新時等情況啟動您的工作流程。
- 使用傳送電子郵件、建立新的行事曆事件等等的動作。例如，當 Salesforce 中有新的物件時，傳送電子郵件給您的 Office 365 Outlook (動作)。

本主題說明如何在邏輯應用程式中使用 Office 365 Outlook 連接器，並且也列出觸發程序和動作。

>[AZURE.NOTE] 這個版本的文章適用於 Logic Apps 公開上市版本 (GA)。

若要深入瞭解 Logic Apps，請參閱[什麼是邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)以及[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 連接至 Office 365

您必須先建立與服務的「連線」，才能透過邏輯應用程式存取任何服務。連線可讓邏輯應用程式與另一個服務連線。例如，若要連線到 Office 365 Outlook，您必須先有 Office 365「連線」。若要建立連線，請輸入平常用來存取所要連線之服務的認證。因此，在 Office 365 Outlook 中，請在 Office 365 帳戶輸入認證以建立連線。


## 建立連線

>[AZURE.INCLUDE [建立 Office 365 連線的步驟](../../includes/connectors-create-api-office365-outlook.md)]

## 使用觸發程序

觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。觸發程序會以您想要的間隔和頻率「輪詢」服務。[深入了解觸發程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 在邏輯應用程式中，輸入 "office 365" 以取得觸發程序的清單︰

	![](./media/connectors-create-api-office365-outlook/office365-trigger.png)

2. 選取 [Office 365 Outlook - 即將來臨的事件快要開始時]。如果連線已存在，則選取下拉式清單中的行事曆。

	![](./media/connectors-create-api-office365-outlook/sample-calendar.png)

	如果系統提示您登入，則輸入登入詳細資料來建立連線。本主題中的[建立連線](connectors-create-api-office365-outlook.md#create-the-connection)一節會列出步驟。

	> [AZURE.NOTE] 在此範例中，邏輯應用程式會在行事曆事件更新時執行。若要查看此觸發程序的結果，請新增另一個動作，以傳送簡訊給您。例如，加入 Twilio「傳送訊息」動作，以便在行事曆事件於 15 分鐘內開始時傳送簡訊給您。

3. 選取 [編輯] 按鈕，然後設定 [頻率] 和 [間隔] 值。例如，如果您希望觸發程序每隔 15 分鐘輪詢一次，則將 [頻率] 設定為 [分鐘] 並將 [**間隔]** 設定為 [15]。

	![](./media/connectors-create-api-office365-outlook/calendar-settings.png)

4. **儲存**您的變更 (工具列的左上角)。邏輯應用程式將會儲存，而且可能會自動啟用。


## 使用動作

動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 選取加號。您會看到幾個選擇︰[新增動作]、[新增條件] 或其中一個 [其他] 選項。

	![](./media/connectors-create-api-office365-outlook/add-action.png)

2. 選擇 [新增動作]。

3. 在文字方塊中，輸入 “office 365” 以取得所有可用動作的清單。

	![](./media/connectors-create-api-office365-outlook/office365-actions.png)

4. 在本例中，選擇 [Office 365 Outlook - 建立連絡人]。如果連線已存在，則選擇 [資料夾識別碼]、[名字] 和其他屬性︰

	![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)

	如果系統提示您輸入連線資訊，請輸入詳細資料以建立連線。本主題的[建立連線](connectors-create-api-office365-outlook.md#create-the-connection)一節會說明這些屬性。

	> [AZURE.NOTE] 在此範例中，我們會在 Office 365 Outlook 中建立新連絡人。您可以使用另一個觸發程序的輸出來建立連絡人。例如，新增 SalesForce「當物件建立時」 觸發程序。然後新增 Office 365 Outlook「建立連絡人」動作，以使用 SalesForce 欄位在 Office 365 中建立新的新連絡人。

5. **儲存**您的變更 (工具列的左上角)。邏輯應用程式將會儲存，而且可能會自動啟用。


## 技術詳細資料

以下是有關這個連接支援的觸發程序、動作和回應的詳細資料︰

## Office 365 觸發程序

|觸發程序 | 說明|
|--- | ---|
|[即將來臨的事件快要開始時](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon)|這項作業會在即將來臨的行事曆事件開始時觸發流程。|
|[新的電子郵件送達時](connectors-create-api-office365-outlook.md#when-a-new-email-arrives)|這項作業會在新的電子郵件送達時觸發流程。|
|[建立新事件時](connectors-create-api-office365-outlook.md#when-a-new-event-is-created)|當行事曆中有新事件建立時，此作業就會觸發流程。|
|[當事件遭到修改時](connectors-create-api-office365-outlook.md#when-an-event-is-modified)|當行事曆中有事件遭到修改時，此作業就會觸發流程。|


## Office 365 動作

|動作|說明|
|--- | ---|
|[取得電子郵件](connectors-create-api-office365-outlook.md#get-emails)|這項作業會從資料夾取得電子郵件。|
|[傳送電子郵件](connectors-create-api-office365-outlook.md#send-an-email)|這項作業會傳送電子郵件訊息。|
|[刪除電子郵件](connectors-create-api-office365-outlook.md#delete-email)|這項作業會依識別碼刪除電子郵件。|
|[標示為已讀取](connectors-create-api-office365-outlook.md#mark-as-read)|這項作業會將電子郵件標示為已讀取。|
|[回覆電子郵件](connectors-create-api-office365-outlook.md#reply-to-email)|這項作業會回覆電子郵件。|
|[取得附件](connectors-create-api-office365-outlook.md#get-attachment)|這項作業會依識別碼擷取電子郵件附件。|
|[傳回具有選項的電子郵件](connectors-create-api-office365-outlook.md#send-email-with-options)|這項作業會傳送具有多個選項的電子郵件，並等候收件者選擇其中一個選項來回應。|
|[傳送核准電子郵件](connectors-create-api-office365-outlook.md#send-approval-email)|這項作業會傳送核准電子郵件，並等候收件者的回應。|
|[取得行事曆](connectors-create-api-office365-outlook.md#get-calendars)|這項作業會列出可用的行事曆。|
|[取得事件](connectors-create-api-office365-outlook.md#get-events)|這項作業會從行事曆取得事件。|
|[建立事件](connectors-create-api-office365-outlook.md#create-event)|這項作業會在行事曆中建立新的事件。|
|[取得事件](connectors-create-api-office365-outlook.md#get-event)|這項作業會從行事曆取得特定事件。|
|[刪除事件](connectors-create-api-office365-outlook.md#delete-event)|這項作業會刪除行事曆中的事件。|
|[更新事件](connectors-create-api-office365-outlook.md#update-event)|這項作業會更新行事曆中的事件。|
|[取得連絡人資料夾](connectors-create-api-office365-outlook.md#get-contact-folders)|這項作業會列出可用的連絡人資料夾。|
|[取得連絡人](connectors-create-api-office365-outlook.md#get-contacts)|這項作業會取得連絡人資料夾中的連絡人。|
|[建立連絡人](connectors-create-api-office365-outlook.md#create-contact)|這項作業會在連絡人資料夾中建立新的連絡人。|
|[取得連絡人](connectors-create-api-office365-outlook.md#get-contact)|這項作業會從連絡人資料夾中擷取特定連絡人。|
|[刪除連絡人](connectors-create-api-office365-outlook.md#delete-contact)|這項作業會刪除連絡人資料夾中的連絡人。|
|[更新連絡人](connectors-create-api-office365-outlook.md#update-contact)|這項作業會更新連絡人資料夾中的連絡人。|

### 觸發程序和動作詳細資料

在本節中，請查看每個觸發程序和動作的特定詳細資料，包括任何必要或選擇性的輸入屬性，以及任何與連接器相關聯的對應輸出。

#### 即將來臨的事件快要開始時
這項作業會在即將來臨的行事曆事件開始時觸發流程。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|行事曆識別碼|行事曆的唯一識別碼|
|lookAheadTimeInMinutes|倒數時間|即將來臨的事件的倒數時間 (以分鐘為單位)。|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
CalendarItemsList：行事曆項目清單

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|value|array|行事曆項目清單|


#### 取得電子郵件
這項作業會從資料夾取得電子郵件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|folderPath|資料夾路徑|要從中擷取電子郵件的資料夾路徑 (預設值：'Inbox')|
|top|前幾個|要擷取的電子郵件數目 (預設值：10)|
|fetchOnlyUnread|只擷取未讀取的訊息|只擷取未讀取的電子郵件嗎？|
|includeAttachments|包含附件|如果設為 true，附件會和電子郵件一起擷取。|
|searchQuery|搜尋查詢|搜尋 query 來篩選電子郵件|
|skip|Skip|要略過的電子郵件數目 (預設值：0)|
|skipToken|略過權杖|跳過語彙基元來提取新頁面|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
ReceiveMessage：接收電子郵件訊息

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|從|字串|從|
|收件人|字串|收件人|
|主旨|字串|主旨|
|內文|字串|內文|
|重要性|字串|重要性|
|HasAttachment|布林值|具有附件|
|識別碼|字串|訊息識別碼|
|IsRead|布林值|已讀取|
|DateTimeReceived|字串|收到的日期與時間|
|附件|array|附件|
|副本|字串|指定電子郵件地址，並以分號分隔，例如 someone@contoso.com|
|密件副本|字串|指定電子郵件地址，並以分號分隔，例如 someone@contoso.com|
|IsHtml|布林值|是 HTML|


#### 傳送電子郵件
這項作業會傳送電子郵件訊息。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|emailMessage*|電子郵件|電子郵件|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。

#### 刪除電子郵件
這項作業會依識別碼刪除電子郵件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|messageId*|訊息識別碼|要刪除的電子郵件識別碼|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。

#### 標示為已讀取
這項作業會將電子郵件標示為已讀取。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|messageId*|訊息識別碼|要標示為已讀取的電子郵件識別碼|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。


#### 回覆電子郵件
這項作業會回覆電子郵件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|messageId*|訊息識別碼|要回覆的電子郵件識別碼|
|comment*|註解|回覆意見|
|replyAll|全部回覆|回覆所有收件者|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。


#### 取得附件
這項作業會依識別碼擷取電子郵件附件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|messageId*|訊息識別碼|電子郵件識別碼|
|attachmentId*|附件識別碼|要下載的附件的識別碼|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。


#### 新的電子郵件送達時
這項作業會在新的電子郵件送達時觸發流程。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|folderPath|資料夾路徑|要擷取的電子郵件資料夾 (預設值：Inbox)|
|to|收件人|收件者電子郵件地址|
|from|從|寄件者地址|
|importance|重要性|電子郵件的重要性 (High、Normal、Low) (預設值：Normal)|
|fetchOnlyWithAttachment|具有附件|只擷取含有附件的電子郵件|
|includeAttachments|包含附件|包含附件|
|subjectFilter|主旨篩選|要在主旨中尋找的字串|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
TriggerBatchResponse[ReceiveMessage]

| 屬性名稱 | 資料類型 |
|---|---|
|value|array|


#### 傳回具有選項的電子郵件
這項作業會傳送具有多個選項的電子郵件，並等候收件者選擇其中一個選項來回應。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|optionsEmailSubscription*|有選項電子郵件的訂用帳戶要求|有選項電子郵件的訂用帳戶要求|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
SubscriptionResponse：核准電子郵件訂用帳戶的模型

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|id|字串|訂用帳戶的識別碼|
|resource|字串|訂用帳戶要求的資源|
|notificationType|字串|通知類型|
|notificationUrl|字串|通知 Url|


#### 傳送核准電子郵件
這項作業會傳送核准電子郵件，並等候收件者的回應。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|approvalEmailSubscription*|核准電子郵件的訂用帳戶要求|核准電子郵件的訂用帳戶要求|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
SubscriptionResponse：核准電子郵件訂用帳戶的模型

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|id|字串|訂用帳戶的識別碼|
|resource|字串|訂用帳戶要求的資源|
|notificationType|字串|通知類型|
|notificationUrl|字串|通知 Url|


#### 取得行事曆
這項作業會列出可用的行事曆。

這個呼叫沒有參數。

##### 輸出詳細資料
TablesList

| 屬性名稱 | 資料類型 |
|---|---|
|value|array|


#### 取得事件
這項作業會從行事曆取得事件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|行事曆識別碼|選取行事曆|
|$filter|篩選查詢|用來限制傳回項目的 ODATA 篩選查詢|
|$orderby|排序依據|用來指定項目順序的 ODATA orderBy 查詢|
|$skip|略過計數|要略過的項目數目 (預設值 = 0)|
|$top|最大取得計數|要擷取的項目數目上限 (預設值 = 256)|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
CalendarEventList：行事曆項目清單

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|value|array|行事曆項目清單|


#### 建立事件
這項作業會在行事曆中建立新的事件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|行事曆識別碼|選取行事曆|
|項目 *|項目|要建立的事件|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
CalendarEvent︰連接器特定行事曆事件模型類別。

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|識別碼|字串|事件的唯一識別碼。|
|出席者|array|事件的出席者清單。|
|內文|未定義|與事件相關聯的訊息主體。|
|BodyPreview|字串|與事件相關聯的訊息預覽。|
|類別|array|與事件相關聯的類別。|
|ChangeKey|字串|識別事件物件的版本。每次變更事件時，ChangeKey 也會跟著變更。|
|DateTimeCreated|字串|建立事件的日期與時間。|
|DateTimeLastModified|字串|上次修改事件的日期與時間。|
|End|字串|事件的結束時間。|
|EndTimeZone|字串|指定會議結束時間的時區。此值必須如 Windows 中的定義 (範例：太平洋標準時間)。|
|HasAttachments|布林值|如果事件具有附件，則設定為 true。|
|重要性|字串|事件的重要性：低、一般或高。|
|IsAllDay|布林值|如果事件持續整天，則設定為 true。|
|IsCancelled|布林值|如果事件已被取消，則設定為 true。|
|IsOrganizer|布林值|如果訊息傳送者也是組織者，則設定為 true。|
|位置|未定義|事件的位置。|
|組織者|未定義|事件的組織者。|
|週期性|未定義|事件的週期性模式。|
|提醒|integer|事件開始前提醒的時間 (分鐘)。|
|ResponseRequested|布林值|如果寄件者想要在事件被接受或拒絕時得到回應，則設定為 true。|
|ResponseStatus|未定義|表示為了回應事件訊息所傳送的回應類型。|
|SeriesMasterId|字串|「主要系列」事件類型的唯一識別碼。|
|ShowAs|字串|顯示為可用或忙碌中。|
|啟動|字串|事件的開始時間。|
|StartTimeZone|字串|指定會議開始時間的時區。此值必須如 Windows 中的定義 (範例：太平洋標準時間)。|
|主旨|字串|事件主旨。|
|類型|字串|事件類型︰單一執行個體、發生次數、例外狀況或主要系列。|
|WebLink|字串|與事件相關聯的訊息預覽。|


#### 取得事件
這項作業會從行事曆取得特定事件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|行事曆識別碼|選取行事曆|
|識別碼*|項目識別碼|選取事件|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
CalendarEvent︰連接器特定行事曆事件模型類別。

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|識別碼|字串|事件的唯一識別碼。|
|出席者|array|事件的出席者清單。|
|內文|未定義|與事件相關聯的訊息主體。|
|BodyPreview|字串|與事件相關聯的訊息預覽。|
|類別|array|與事件相關聯的類別。|
|ChangeKey|字串|識別事件物件的版本。每次變更事件時，ChangeKey 也會跟著變更。|
|DateTimeCreated|字串|建立事件的日期與時間。|
|DateTimeLastModified|字串|上次修改事件的日期與時間。|
|End|字串|事件的結束時間。|
|EndTimeZone|字串|指定會議結束時間的時區。此值必須如 Windows 中的定義 (範例：太平洋標準時間)。|
|HasAttachments|布林值|如果事件具有附件，則設定為 true。|
|重要性|字串|事件的重要性：低、一般或高。|
|IsAllDay|布林值|如果事件持續整天，則設定為 true。|
|IsCancelled|布林值|如果事件已被取消，則設定為 true。|
|IsOrganizer|布林值|如果訊息傳送者也是組織者，則設定為 true。|
|位置|未定義|事件的位置。|
|組織者|未定義|事件的組織者。|
|週期性|未定義|事件的週期性模式。|
|提醒|integer|事件開始前提醒的時間 (分鐘)。|
|ResponseRequested|布林值|如果寄件者想要在事件被接受或拒絕時得到回應，則設定為 true。|
|ResponseStatus|未定義|表示為了回應事件訊息所傳送的回應類型。|
|SeriesMasterId|字串|「主要系列」事件類型的唯一識別碼。|
|ShowAs|字串|顯示為可用或忙碌中。|
|啟動|字串|事件的開始時間。|
|StartTimeZone|字串|指定會議開始時間的時區。此值必須如 Windows 中的定義 (範例：太平洋標準時間)。|
|主旨|字串|事件主旨。|
|類型|字串|事件類型︰單一執行個體、發生次數、例外狀況或主要系列。|
|WebLink|字串|與事件相關聯的訊息預覽。|


#### 刪除事件
這項作業會刪除行事曆中的事件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|行事曆識別碼|選取行事曆|
|識別碼*|識別碼|選取事件|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。


#### 更新事件
這項作業會更新行事曆中的事件。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|行事曆識別碼|選取行事曆|
|識別碼*|識別碼|選取事件|
|項目 *|項目|要更新的事件|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
CalendarEvent︰連接器特定行事曆事件模型類別。

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|識別碼|字串|事件的唯一識別碼。|
|出席者|array|事件的出席者清單。|
|內文|未定義|與事件相關聯的訊息主體。|
|BodyPreview|字串|與事件相關聯的訊息預覽。|
|類別|array|與事件相關聯的類別。|
|ChangeKey|字串|識別事件物件的版本。每次變更事件時，ChangeKey 也會跟著變更。|
|DateTimeCreated|字串|建立事件的日期與時間。|
|DateTimeLastModified|字串|上次修改事件的日期與時間。|
|End|字串|事件的結束時間。|
|EndTimeZone|字串|指定會議結束時間的時區。此值必須如 Windows 中的定義 (範例：太平洋標準時間)。|
|HasAttachments|布林值|如果事件具有附件，則設定為 true。|
|重要性|字串|事件的重要性：低、一般或高。|
|IsAllDay|布林值|如果事件持續整天，則設定為 true。|
|IsCancelled|布林值|如果事件已被取消，則設定為 true。|
|IsOrganizer|布林值|如果訊息傳送者也是組織者，則設定為 true。|
|位置|未定義|事件的位置。|
|組織者|未定義|事件的組織者。|
|週期性|未定義|事件的週期性模式。|
|提醒|integer|事件開始前提醒的時間 (分鐘)。|
|ResponseRequested|布林值|如果寄件者想要在事件被接受或拒絕時得到回應，則設定為 true。|
|ResponseStatus|未定義|表示為了回應事件訊息所傳送的回應類型。|
|SeriesMasterId|字串|「主要系列」事件類型的唯一識別碼。|
|ShowAs|字串|顯示為可用或忙碌中。|
|啟動|字串|事件的開始時間。|
|StartTimeZone|字串|指定會議開始時間的時區。此值必須如 Windows 中的定義 (範例：太平洋標準時間)。|
|主旨|字串|事件主旨。|
|類型|字串|事件類型︰單一執行個體、發生次數、例外狀況或主要系列。|
|WebLink|字串|與事件相關聯的訊息預覽。|


#### 建立新事件時
當行事曆中有新事件建立時，此作業就會觸發流程。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|行事曆識別碼|選取行事曆|
|$filter|篩選查詢|用來限制傳回項目的 ODATA 篩選查詢|
|$orderby|排序依據|用來指定項目順序的 ODATA orderBy 查詢|
|$skip|略過計數|要略過的項目數目 (預設值 = 0)|
|$top|最大取得計數|要擷取的項目數目上限 (預設值 = 256)|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
CalendarItemsList：行事曆項目清單

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|value|array|行事曆項目清單|


#### 當事件遭到修改時
當行事曆中有事件遭到修改時，此作業就會觸發流程。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|行事曆識別碼|選取行事曆|
|$filter|篩選查詢|用來限制傳回項目的 ODATA 篩選查詢|
|$orderby|排序依據|用來指定項目順序的 ODATA orderBy 查詢|
|$skip|略過計數|要略過的項目數目 (預設值 = 0)|
|$top|最大取得計數|要擷取的項目數目上限 (預設值 = 256)|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
CalendarItemsList：行事曆項目清單


| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|value|array|行事曆項目清單|


#### 取得連絡人資料夾
這項作業會列出可用的連絡人資料夾。

這個呼叫沒有參數。

##### 輸出詳細資料
TablesList

| 屬性名稱 | 資料類型 |
|---|---|
|value|array|


#### 取得連絡人
這項作業會取得連絡人資料夾中的連絡人。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料夾識別碼|要擷取的連絡人資料夾的唯一識別碼|
|$filter|篩選查詢|用來限制傳回項目的 ODATA 篩選查詢|
|$orderby|排序依據|用來指定項目順序的 ODATA orderBy 查詢|
|$skip|略過計數|要略過的項目數目 (預設值 = 0)|
|$top|最大取得計數|要擷取的項目數目上限 (預設值 = 256)|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
ContactList︰連絡人清單

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|value|array|連絡人清單|


#### 建立連絡人
這項作業會在連絡人資料夾中建立新的連絡人。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料夾識別碼|選取連絡人資料夾|
|項目 *|項目|要建立的連絡人|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
連絡人：連絡人

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|識別碼|字串|連絡人的唯一識別碼。|
|ParentFolderId|字串|連絡人的父資料夾識別碼|
|Birthday|字串|連絡人的生日。|
|FileAs|字串|將連絡人歸檔的名稱。|
|DisplayName|字串|連絡人的顯示名稱。|
|GivenName|字串|連絡人的名字。|
|Initials|字串|連絡人的縮寫。|
|MiddleName|字串|連絡人的中間名。|
|NickName|字串|連絡人的暱稱。|
|Surname|字串|連絡人的姓氏。|
|頭銜|字串|連絡人職稱。|
|世代|字串|連絡人的世代。|
|EmailAddresses|array|連絡人的電子郵件地址。|
|ImAddresses|array|連絡人的立即訊息 (IM) 地址。|
|JobTitle|字串|連絡人職稱。|
|CompanyName|字串|連絡人的公司名稱。|
|部門|字串|連絡人的部門。|
|OfficeLocation|字串|連絡人的辦公室位置。|
|Profession|字串|連絡人的職業。|
|BusinessHomePage|字串|連絡人的公司首頁。|
|AssistantName|字串|連絡人的助理名稱。|
|Manager|字串|連絡人的經理名稱。|
|HomePhones|array|連絡人的住家電話號碼。|
|BusinessPhones|array|連絡人的公司電話號碼。|
|MobilePhone1|字串|連絡人的行動電話號碼。|
|HomeAddress|未定義|連絡人的住家地址。|
|BusinessAddress|未定義|連絡人的公司地址。|
|OtherAddress|未定義|連絡人的其他地址。|
|YomiCompanyName|字串|連絡人的公司名稱日文拼音。|
|YomiGivenName|字串|連絡人的名字日文拼音。|
|YomiSurname|字串|連絡人的姓氏日文拼音。|
|類別|array|與連絡人相關聯的類別。|
|ChangeKey|字串|識別事件物件的版本。|
|DateTimeCreated|字串|建立連絡人的時間。|
|DateTimeLastModified|字串|修改連絡人的時間。|


#### 取得連絡人
這項作業會從連絡人資料夾中擷取特定連絡人。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料夾識別碼|選取連絡人資料夾|
|識別碼*|項目識別碼|要擷取的連絡人的唯一識別碼|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
連絡人：連絡人

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|識別碼|字串|連絡人的唯一識別碼。|
|ParentFolderId|字串|連絡人的父資料夾識別碼|
|Birthday|字串|連絡人的生日。|
|FileAs|字串|將連絡人歸檔的名稱。|
|DisplayName|字串|連絡人的顯示名稱。|
|GivenName|字串|連絡人的名字。|
|Initials|字串|連絡人的縮寫。|
|MiddleName|字串|連絡人的中間名。|
|NickName|字串|連絡人的暱稱。|
|Surname|字串|連絡人的姓氏。|
|課程名稱|字串|連絡人職稱。|
|世代|字串|連絡人的世代。|
|EmailAddresses|array|連絡人的電子郵件地址。|
|ImAddresses|array|連絡人的立即訊息 (IM) 地址。|
|JobTitle|字串|連絡人職稱。|
|CompanyName|字串|連絡人的公司名稱。|
|部門|字串|連絡人的部門。|
|OfficeLocation|字串|連絡人的辦公室位置。|
|Profession|字串|連絡人的職業。|
|BusinessHomePage|字串|連絡人的公司首頁。|
|AssistantName|字串|連絡人的助理名稱。|
|Manager|字串|連絡人的經理名稱。|
|HomePhones|array|連絡人的住家電話號碼。|
|BusinessPhones|array|連絡人的公司電話號碼。|
|MobilePhone1|字串|連絡人的行動電話號碼。|
|HomeAddress|未定義|連絡人的住家地址。|
|BusinessAddress|未定義|連絡人的公司地址。|
|OtherAddress|未定義|連絡人的其他地址。|
|YomiCompanyName|字串|連絡人的公司名稱日文拼音。|
|YomiGivenName|字串|連絡人的名字日文拼音。|
|YomiSurname|字串|連絡人的姓氏日文拼音。|
|類別|array|與連絡人相關聯的類別。|
|ChangeKey|字串|識別事件物件的版本。|
|DateTimeCreated|字串|建立連絡人的時間。|
|DateTimeLastModified|字串|修改連絡人的時間。|


#### 刪除連絡人
這項作業會刪除連絡人資料夾中的連絡人。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料夾識別碼|選取連絡人資料夾|
|識別碼*|識別碼|要刪除的連絡人的唯一識別碼|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。


#### 更新連絡人
這項作業會更新連絡人資料夾中的連絡人。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料夾識別碼|選取連絡人資料夾|
|識別碼*|識別碼|要更新的連絡人的唯一識別碼|
|項目 *|項目|要更新的連絡人項目|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
連絡人：連絡人

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|識別碼|字串|連絡人的唯一識別碼。|
|ParentFolderId|字串|連絡人的父資料夾識別碼|
|Birthday|字串|連絡人的生日。|
|FileAs|字串|將連絡人歸檔的名稱。|
|DisplayName|字串|連絡人的顯示名稱。|
|GivenName|字串|連絡人的名字。|
|Initials|字串|連絡人的縮寫。|
|MiddleName|字串|連絡人的中間名。|
|NickName|字串|連絡人的暱稱。|
|Surname|字串|連絡人的姓氏。|
|課程名稱|字串|連絡人職稱。|
|世代|字串|連絡人的世代。|
|EmailAddresses|array|連絡人的電子郵件地址。|
|ImAddresses|array|連絡人的立即訊息 (IM) 地址。|
|JobTitle|字串|連絡人職稱。|
|CompanyName|字串|連絡人的公司名稱。|
|部門|字串|連絡人的部門。|
|OfficeLocation|字串|連絡人的辦公室位置。|
|Profession|字串|連絡人的職業。|
|BusinessHomePage|字串|連絡人的公司首頁。|
|AssistantName|字串|連絡人的助理名稱。|
|Manager|字串|連絡人的經理名稱。|
|HomePhones|array|連絡人的住家電話號碼。|
|BusinessPhones|array|連絡人的公司電話號碼。|
|MobilePhone1|字串|連絡人的行動電話號碼。|
|HomeAddress|未定義|連絡人的住家地址。|
|BusinessAddress|未定義|連絡人的公司地址。|
|OtherAddress|未定義|連絡人的其他地址。|
|YomiCompanyName|字串|連絡人的公司名稱日文拼音。|
|YomiGivenName|字串|連絡人的名字日文拼音。|
|YomiSurname|字串|連絡人的姓氏日文拼音。|
|類別|array|與連絡人相關聯的類別。|
|ChangeKey|字串|識別事件物件的版本。|
|DateTimeCreated|字串|建立連絡人的時間。|
|DateTimeLastModified|字串|修改連絡人的時間。|



## HTTP 回應

上述動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰

|名稱|說明|
|---|---|
|200|OK|
|202|已接受|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。請到我們的 [API 清單](apis-list.md)探索 Logic Apps 中其他可用的連接器。

<!---HONumber=AcomDC_0727_2016-->