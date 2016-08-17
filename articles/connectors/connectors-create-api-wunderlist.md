<properties
pageTitle="Wunderlist | Microsoft Azure"
description="使用 Azure App Service 建立邏輯應用程式。Wunderlist 提供待辦事項清單和工作管理員，協助使用者完成其工作。無論是與親愛的人共用購物清單、處理專案，還是計劃假期，Wunderlist 都能讓您輕鬆擷取、共用及完成待辦事項。Wunderlist 立即同步處理您的電話、平板電腦及電腦，讓您可在任何地方存取工作。"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# 開始使用 Wunderlist 連接器



您可從下列位置使用 Wunderlist 連接器︰

- [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flow](http://flows.microsoft.com)

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以從立即建立邏輯應用程式開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

Wunderlist 連接器可當做動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 Wunderlist 連接器提供下列動作及/或觸發程序：

### Wunderlist 動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|擷取與帳戶相關聯的清單。|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|建立清單。|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|擷取特定清單中的工作。|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|建立工作|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|擷取特定清單或特定工作中的子工作。|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|在特定工作內建立子工作|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|擷取特定清單或特定工作的附註。|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|在特定工作中加入附註|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|擷取特定清單或特定工作的工作註解。|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|在特定工作中加入註解|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|擷取特定清單或特定工作的提醒。|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|設定提醒。|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|擷取特定清單或特定工作的檔案。|
|[GetList](connectors-create-api-wunderlist.md#getlist)|擷取特定的清單|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|刪除清單|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|更新特定的清單|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|擷取特定的工作|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|更新特定的工作|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|刪除特定的工作|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|擷取特定的子工作|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|更新特定的子工作|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|刪除特定的子工作|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|擷取特定的附註|
|[UpdateNote](connectors-create-api-wunderlist.md#updatenote)|更新特定的附註|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|刪除特定的附註|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|擷取特定工作的註解|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|更新特定的提醒|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|刪除特定的提醒|
### Wunderlist 觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|
|工作到期時|清單中的工作到期時就會觸發新的流程|
|建立新工作時|在清單中建立新工作時就會觸發新的流程|
|提醒出現時|提醒出現時就會觸發新的流程|


## 建立 Wunderlist 的連線
若要使用 Wunderlist 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 Wunderlist 認證|
建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。


>[AZURE.INCLUDE [建立至 Wunderlist 連線的步驟](../../includes/connectors-create-api-wunderlist.md)]


>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## Wunderlist 的參考
適用的版本：1.0

## TriggerTaskDue
工作到期時：清單中的工作到期時就會觸發新的流程

```GET: /trigger/tasksdue```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|


## TriggerTaskNew
建立新工作時︰在清單中建立新工作時就會觸發新的流程

```GET: /trigger/tasksnew```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|


## TriggerReminder
提醒出現時：提醒出現時就會觸發新的流程

```GET: /trigger/reminders```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|task\_id|integer|no|query|無|工作識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|


## RetrieveLists
取得清單：擷取與帳戶相關聯的清單。

```GET: /lists```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|不正確的要求|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateList
建立清單︰建立清單。

```POST: /lists```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|post| |yes|body|無|要建立的新清單|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|預設值|作業失敗。|


## ListTasks
取得工作：擷取特定清單中的工作。

```GET: /tasks```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|完成|布林值|no|query|無|Completed|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|不正確的要求|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateTask
建立工作︰建立工作

```POST: /tasks```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|post| |yes|body|無|要建立的新工作|

#### Response

|名稱|說明|
|---|---|
|201|建立時間|


## ListSubTasks
取得子工作︰擷取特定清單或特定工作中的子工作。

```GET: /subtasks```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|task\_id|integer|no|query|無|工作識別碼|
|完成|布林值|no|query|無|Completed|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|不正確的要求|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateSubTask
建立子工作：在特定工作內建立子工作

```POST: /subtasks```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|post| |yes|body|無|要建立的新的子工作|

#### Response

|名稱|說明|
|---|---|
|201|建立時間|


## ListNotes
取得附註：擷取特定清單或特定工作的附註。

```GET: /notes```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|task\_id|integer|no|query|無|工作識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|不正確的要求|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateNote
建立附註︰在特定工作中加入附註

```POST: /notes```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|post| |yes|body|無|要建立的新附註|

#### Response

|名稱|說明|
|---|---|
|201|建立時間|


## ListComments
取得工作註解：擷取特定清單或特定工作的工作註解。

```GET: /task_comments```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|task\_id|integer|no|query|無|工作識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|不正確的要求|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateComment
加入工作註解：在特定工作中加入註解

```POST: /task_comments```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|post| |yes|body|無|要建立的新工作註解|

#### Response

|名稱|說明|
|---|---|
|201|建立時間|


## RetrieveReminders
取得提醒：擷取特定清單或特定工作的提醒。

```GET: /reminders```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|task\_id|integer|no|query|無|工作識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|不正確的要求|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateReminder
設定提醒︰設定提醒。

```POST: /reminders```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|post| |yes|body|無|要建立的新提醒|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|預設值|作業失敗。|


## RetrieveFiles
取得檔案：擷取特定清單或特定工作的檔案。

```GET: /files```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|task\_id|integer|no|query|無|工作識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|不正確的要求|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## GetList
取得清單：擷取特定的清單

```GET: /lists/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|清單識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## DeleteList
刪除清單︰刪除清單

```DELETE: /lists/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|integer|yes|路徑|無|清單識別碼|
|修訂|integer|yes|query|無|修訂|

#### Response

|名稱|說明|
|---|---|
|204|沒有內容|


## UpdateList
更新清單︰更新特定的清單

```PATCH: /lists/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|integer|yes|路徑|無|清單識別碼|
|post| |yes|body|無|清單詳細資料|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## GetTask
取得工作：擷取特定的工作

```GET: /tasks/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|id|integer|yes|路徑|無|工作識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## UpdateTask
更新工作︰更新特定的工作

```PATCH: /tasks/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|id|integer|yes|路徑|無|工作識別碼|
|post| |yes|body|無|作業詳細資料|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## DeleteTask
刪除工作：刪除特定的工作

```DELETE: /tasks/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|無|清單識別碼|
|id|integer|yes|路徑|無|工作識別碼|
|修訂|integer|yes|query|無|修訂|

#### Response

|名稱|說明|
|---|---|
|204|沒有內容|


## GetSubTask
取得子工作：擷取特定的子工作

```GET: /subtasks/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|子工作識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## UpdateSubTask
更新子工作︰更新特定的子工作

```PATCH: /subtasks/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|integer|yes|路徑|無|子工作識別碼|
|post| |yes|body|無|子工作詳細資料|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## DeleteSubTask
刪除子工作︰刪除特定的子工作

```DELETE: /subtasks/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|integer|yes|路徑|無|子工作識別碼|
|修訂|integer|yes|query|無|修訂|

#### Response

|名稱|說明|
|---|---|
|204|沒有內容|


## GetNote
取得附註：擷取特定的附註

```GET: /notes/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|附註識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## UpdateNote
更新附註︰更新特定的附註

```PATCH: /notes/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|integer|yes|路徑|無|附註識別碼|
|post| |yes|body|無|附註詳細資料|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## DeleteNote
刪除附註︰刪除特定的附註

```DELETE: /notes/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|integer|yes|路徑|無|附註識別碼|
|修訂|integer|yes|query|無|修訂|

#### Response

|名稱|說明|
|---|---|
|204|沒有內容|


## GetComment
取得工作註解：擷取特定的工作註解

```GET: /task_comments/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|註解識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## UpdateReminder
更新提醒︰更新特定的提醒

```PATCH: /reminders/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|integer|yes|路徑|無|提醒識別碼|
|post| |yes|body|無|提醒詳細資料|

#### Response

|名稱|說明|
|---|---|
|200|OK|


## DeleteReminder
刪除提醒︰更新特定的提醒

```DELETE: /reminders/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|integer|yes|路徑|無|提醒的識別碼。|
|修訂|integer|yes|query|無|修訂|

#### Response

|名稱|說明|
|---|---|
|204|沒有內容|


## 物件定義 

### 列出


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|created\_at|字串|否 |
|title|字串|否 |
|list\_type|字串|否 |
|類型|字串|否 |
|修訂|integer|否 |



### CreatedList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|created\_at|字串|否 |
|title|字串|否 |
|修訂|integer|否 |
|類型|字串|否 |



### 工作


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|assignee\_id|integer|否 |
|assigner\_id|integer|否 |
|created\_at|字串|否 |
|created\_by\_id|integer|否 |
|due\_date|字串|否 |
|list\_id|integer|否 |
|修訂|integer|否 |
|已加星號|布林值|否 |
|title|字串|否 |



### 子工作


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|task\_id|integer|否 |
|created\_at|字串|否 |
|created\_by\_id|integer|否 |
|修訂|字串|否 |
|title|字串|否 |



### 注意


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|task\_id|integer|否 |
|內容|字串|否 |
|created\_at|字串|否 |
|updated\_at|字串|否 |
|修訂|integer|否 |



### 註解


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|task\_id|integer|否 |
|修訂|integer|否 |
|文字|字串|否 |
|類型|字串|否 |
|created\_at|字串|否 |



### 提醒


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|日期|字串|否 |
|task\_id|integer|否 |
|修訂|integer|否 |
|類型|字串|否 |
|created\_at|字串|否 |
|updated\_at|字串|否 |



### CreatedReminder


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|日期|字串|否 |
|task\_id|integer|否 |
|修訂|integer|否 |
|created\_at|字串|否 |
|updated\_at|字串|否 |



### 檔案


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|integer|否 |
|url|字串|否 |
|task\_id|integer|否 |
|list\_id|integer|否 |
|user\_id|integer|否 |
|file\_name|字串|否 |
|content\_type|字串|否 |
|file\_size|integer|否 |
|local\_created\_at|字串|否 |
|created\_at|字串|否 |
|updated\_at|字串|否 |
|類型|字串|否 |
|修訂|integer|否 |



### NewTask


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|list\_id|integer|是 |
|title|字串|是 |
|assignee\_id|integer|否 |
|完成|布林值|否 |
|recurrence\_type|字串|否 |
|recurrence\_count|integer|否 |
|due\_date|字串|否 |
|已加星號|布林值|否 |



### NewList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|title|字串|是 |



### NewSubtask


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|list\_id|integer|是 |
|task\_id|integer|是 |
|title|字串|是 |
|完成|布林值|否 |



### NewNote


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|list\_id|integer|是 |
|task\_id|integer|是 |
|內容|字串|是 |



### NewComment


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|list\_id|integer|是 |
|task\_id|integer|是 |
|文字|字串|是 |



### NewReminder


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|list\_id|integer|是 |
|task\_id|integer|是 |
|日期|字串|是 |



### UpdateTask


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|修訂|integer|否 |
|title|字串|否 |
|assignee\_id|integer|否 |
|完成|布林值|否 |
|recurrence\_type|字串|否 |
|recurrence\_count|integer|否 |
|due\_date|字串|否 |
|已加星號|布林值|否 |



### UpdateList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|修訂|integer|否 |
|title|字串|否 |



### UpdateSubtask


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|修訂|integer|否 |
|title|字串|否 |
|完成|布林值|否 |



### UpdateNote


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|修訂|integer|否 |
|內容|字串|否 |



### UpdateReminder


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|日期|字串|否 |
|修訂|integer|否 |


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->