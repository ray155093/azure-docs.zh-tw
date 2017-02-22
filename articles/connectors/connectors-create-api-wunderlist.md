---
title: Wunderlist | Microsoft Docs
description: "使用 Azure App Service 建立邏輯應用程式。 Wunderlist 提供待辦事項清單和工作管理員，協助使用者完成其工作。  無論是與親愛的人共用購物清單、處理專案，還是計劃假期，Wunderlist 都能讓您輕鬆擷取、共用及完成待辦事項。 Wunderlist 立即同步處理您的電話、平板電腦及電腦，讓您可在任何地方存取工作。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 084ac505003e926e9c4b75352ec96a77f8c3584a


---
# <a name="get-started-with-the-wunderlist-connector"></a>開始使用 Wunderlist 連接器
Wunderlist 提供待辦事項清單和工作管理員，協助使用者完成其工作。  無論是與親愛的人共用購物清單、處理專案，還是計劃假期，Wunderlist 都能讓您輕鬆擷取、共用及完成待辦事項。 Wunderlist 立即同步處理您的電話、平板電腦及電腦，讓您可在任何地方存取工作。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Wunderlist 連接器可當做動作使用，它有觸發程序。 所有連接器都支援 JSON 和 XML 格式的資料。

 Wunderlist 連接器提供下列動作及/或觸發程序：

### <a name="wunderlist-actions"></a>Wunderlist 動作
您可以採取下列動作：

| 動作 | 說明 |
| --- | --- |
| [RetrieveLists](connectors-create-api-wunderlist.md#retrievelists) |擷取與帳戶相關聯的清單。 |
| [CreateList](connectors-create-api-wunderlist.md#createlist) |建立清單。 |
| [ListTasks](connectors-create-api-wunderlist.md#listtasks) |擷取特定清單中的工作。 |
| [CreateTask](connectors-create-api-wunderlist.md#createtask) |建立工作 |
| [ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks) |擷取特定清單或特定工作中的子工作。 |
| [CreateSubTask](connectors-create-api-wunderlist.md#createsubtask) |在特定工作內建立子工作 |
| [ListNotes](connectors-create-api-wunderlist.md#listnotes) |擷取特定清單或特定工作的附註。 |
| [CreateNote](connectors-create-api-wunderlist.md#createnote) |在特定工作中加入附註 |
| [ListComments](connectors-create-api-wunderlist.md#listcomments) |擷取特定清單或特定工作的工作註解。 |
| [CreateComment](connectors-create-api-wunderlist.md#createcomment) |在特定工作中加入註解 |
| [RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders) |擷取特定清單或特定工作的提醒。 |
| [CreateReminder](connectors-create-api-wunderlist.md#createreminder) |設定提醒。 |
| [RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles) |擷取特定清單或特定工作的檔案。 |
| [GetList](connectors-create-api-wunderlist.md#getlist) |擷取特定的清單 |
| [DeleteList](connectors-create-api-wunderlist.md#deletelist) |刪除清單 |
| [UpdateList](connectors-create-api-wunderlist.md#updatelist) |更新特定的清單 |
| [GetTask](connectors-create-api-wunderlist.md#gettask) |擷取特定的工作 |
| [UpdateTask](connectors-create-api-wunderlist.md#updatetask) |更新特定的工作 |
| [DeleteTask](connectors-create-api-wunderlist.md#deletetask) |刪除特定的工作 |
| [GetSubTask](connectors-create-api-wunderlist.md#getsubtask) |擷取特定的子工作 |
| [UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask) |更新特定的子工作 |
| [DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask) |刪除特定的子工作 |
| [GetNote](connectors-create-api-wunderlist.md#getnote) |擷取特定的附註 |
| [UpdateNote](connectors-create-api-wunderlist.md#updatenote) |更新特定的附註 |
| [DeleteNote](connectors-create-api-wunderlist.md#deletenote) |刪除特定的附註 |
| [GetComment](connectors-create-api-wunderlist.md#getcomment) |擷取特定工作的註解 |
| [UpdateReminder](connectors-create-api-wunderlist.md#updatereminder) |更新特定的提醒 |
| [DeleteReminder](connectors-create-api-wunderlist.md#deletereminder) |刪除特定的提醒 |

### <a name="wunderlist-triggers"></a>Wunderlist 觸發程序
您可以接聽下列事件：

| 觸發程序 | 說明 |
| --- | --- |
| 工作到期時 |清單中的工作到期時就會觸發新的流程 |
| 建立新工作時 |在清單中建立新工作時就會觸發新的流程 |
| 提醒出現時 |提醒出現時就會觸發新的流程 |

## <a name="create-a-connection-to-wunderlist"></a>建立 Wunderlist 的連線
若要使用 Wunderlist 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料：

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 Wunderlist 認證 |

建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 
> [!TIP]
> 您可以在其他邏輯應用程式中使用這個連接。
> 
> 

## <a name="reference-for-wunderlist"></a>Wunderlist 的參考
適用的版本：1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
工作到期時：清單中的工作到期時就會觸發新的流程

```GET: /trigger/tasksdue```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |

## <a name="triggertasknew"></a>TriggerTaskNew
建立新工作時︰在清單中建立新工作時就會觸發新的流程

```GET: /trigger/tasksnew```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |

## <a name="triggerreminder"></a>TriggerReminder
提醒出現時：提醒出現時就會觸發新的流程

```GET: /trigger/reminders```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| task_id |integer |no |query |無 |工作識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |

## <a name="retrievelists"></a>RetrieveLists
取得清單：擷取與帳戶相關聯的清單。

```GET: /lists```

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="createlist"></a>CreateList
建立清單︰建立清單。

```POST: /lists```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |無 |要建立的新清單 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 預設值 |作業失敗。 |

## <a name="listtasks"></a>ListTasks
取得工作：擷取特定清單中的工作。

```GET: /tasks```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| 完成 |布林值 |no |query |無 |Completed |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="createtask"></a>CreateTask
建立工作︰建立工作

```POST: /tasks```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |無 |要建立的新工作 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 201 |建立時間 |

## <a name="listsubtasks"></a>ListSubTasks
取得子工作︰擷取特定清單或特定工作中的子工作。

```GET: /subtasks```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| task_id |integer |no |query |無 |工作識別碼 |
| 完成 |布林值 |no |query |無 |Completed |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="createsubtask"></a>CreateSubTask
建立子工作：在特定工作內建立子工作

```POST: /subtasks```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |無 |要建立的新的子工作 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 201 |建立時間 |

## <a name="listnotes"></a>ListNotes
取得附註：擷取特定清單或特定工作的附註。

```GET: /notes```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| task_id |integer |no |query |無 |工作識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="createnote"></a>CreateNote
建立附註︰在特定工作中加入附註

```POST: /notes```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |無 |要建立的新附註 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 201 |建立時間 |

## <a name="listcomments"></a>ListComments
取得工作註解：擷取特定清單或特定工作的工作註解。

```GET: /task_comments```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| task_id |integer |no |query |無 |工作識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="createcomment"></a>CreateComment
加入工作註解：在特定工作中加入註解

```POST: /task_comments```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |無 |要建立的新工作註解 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 201 |建立時間 |

## <a name="retrievereminders"></a>RetrieveReminders
取得提醒：擷取特定清單或特定工作的提醒。

```GET: /reminders```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| task_id |integer |no |query |無 |工作識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="createreminder"></a>CreateReminder
設定提醒︰設定提醒。

```POST: /reminders```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |無 |要建立的新提醒 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 預設值 |作業失敗。 |

## <a name="retrievefiles"></a>RetrieveFiles
取得檔案：擷取特定清單或特定工作的檔案。

```GET: /files```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| task_id |integer |no |query |無 |工作識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="getlist"></a>GetList
取得清單：擷取特定的清單

```GET: /lists/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |清單識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="deletelist"></a>DeleteList
刪除清單︰刪除清單

```DELETE: /lists/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路徑 |無 |清單識別碼 |
| 修訂 |integer |yes |query |無 |修訂 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 204 |沒有內容 |

## <a name="updatelist"></a>UpdateList
更新清單︰更新特定的清單

```PATCH: /lists/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路徑 |無 |清單識別碼 |
| post | |yes |body |無 |清單詳細資料 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="gettask"></a>GetTask
取得工作：擷取特定的工作

```GET: /tasks/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| id |integer |是 |路徑 |無 |工作識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="updatetask"></a>UpdateTask
更新工作︰更新特定的工作

```PATCH: /tasks/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| id |integer |是 |路徑 |無 |工作識別碼 |
| post | |yes |body |無 |作業詳細資料 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="deletetask"></a>DeleteTask
刪除工作：刪除特定的工作

```DELETE: /tasks/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |無 |清單識別碼 |
| id |integer |是 |路徑 |無 |工作識別碼 |
| 修訂 |integer |yes |query |無 |修訂 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 204 |沒有內容 |

## <a name="getsubtask"></a>GetSubTask
取得子工作：擷取特定的子工作

```GET: /subtasks/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |子工作識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="updatesubtask"></a>UpdateSubTask
更新子工作︰更新特定的子工作

```PATCH: /subtasks/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路徑 |無 |子工作識別碼 |
| post | |yes |body |無 |子工作詳細資料 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="deletesubtask"></a>DeleteSubTask
刪除子工作︰刪除特定的子工作

```DELETE: /subtasks/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路徑 |無 |子工作識別碼 |
| 修訂 |integer |yes |query |無 |修訂 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 204 |沒有內容 |

## <a name="getnote"></a>GetNote
取得附註：擷取特定的附註

```GET: /notes/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |附註識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="updatenote"></a>UpdateNote
更新附註︰更新特定的附註

```PATCH: /notes/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路徑 |無 |附註識別碼 |
| post | |yes |body |無 |附註詳細資料 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="deletenote"></a>DeleteNote
刪除附註︰刪除特定的附註

```DELETE: /notes/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路徑 |無 |附註識別碼 |
| 修訂 |integer |yes |query |無 |修訂 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 204 |沒有內容 |

## <a name="getcomment"></a>GetComment
取得工作註解：擷取特定的工作註解

```GET: /task_comments/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |註解識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="updatereminder"></a>UpdateReminder
更新提醒︰更新特定的提醒

```PATCH: /reminders/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路徑 |無 |提醒識別碼 |
| post | |yes |body |無 |提醒詳細資料 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |

## <a name="deletereminder"></a>DeleteReminder
刪除提醒︰更新特定的提醒

```DELETE: /reminders/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路徑 |無 |提醒的識別碼。 |
| 修訂 |integer |yes |query |無 |修訂 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 204 |沒有內容 |

## <a name="object-definitions"></a>物件定義
### <a name="list"></a>列出
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| created_at |string |否 |
| title |string |否 |
| list_type |string |否 |
| 類型 |string |否 |
| 修訂 |integer |否 |

### <a name="createdlist"></a>CreatedList
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| created_at |string |否 |
| title |string |否 |
| 修訂 |integer |否 |
| 類型 |string |否 |

### <a name="task"></a>工作
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| assignee_id |integer |否 |
| assigner_id |integer |否 |
| created_at |string |否 |
| created_by_id |integer |否 |
| due_date |string |否 |
| list_id |integer |否 |
| 修訂 |integer |否 |
| 已加星號 |布林值 |否 |
| title |string |否 |

### <a name="subtask"></a>子工作
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| task_id |integer |否 |
| created_at |string |否 |
| created_by_id |integer |否 |
| 修訂 |string |否 |
| title |string |否 |

### <a name="note"></a>注意
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| task_id |integer |否 |
| 內容 |string |否 |
| created_at |string |否 |
| updated_at |string |否 |
| 修訂 |integer |否 |

### <a name="comment"></a>註解
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| task_id |integer |否 |
| 修訂 |integer |否 |
| 文字 |string |否 |
| 類型 |string |否 |
| created_at |string |否 |

### <a name="reminder"></a>提醒
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| 日期 |string |否 |
| task_id |integer |否 |
| 修訂 |integer |否 |
| 類型 |string |否 |
| created_at |string |否 |
| updated_at |string |否 |

### <a name="createdreminder"></a>CreatedReminder
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| 日期 |string |否 |
| task_id |integer |否 |
| 修訂 |integer |否 |
| created_at |string |否 |
| updated_at |string |否 |

### <a name="file"></a>檔案
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |否 |
| url |string |否 |
| task_id |integer |否 |
| list_id |integer |否 |
| user_id |integer |否 |
| file_name |string |否 |
| content_type |string |否 |
| file_size |integer |否 |
| local_created_at |string |否 |
| created_at |string |否 |
| updated_at |string |否 |
| 類型 |string |否 |
| 修訂 |integer |否 |

### <a name="newtask"></a>NewTask
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| list_id |integer |是 |
| title |string |是 |
| assignee_id |integer |否 |
| 完成 |布林值 |否 |
| recurrence_type |string |否 |
| recurrence_count |integer |否 |
| due_date |string |否 |
| 已加星號 |布林值 |否 |

### <a name="newlist"></a>NewList
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| title |string |是 |

### <a name="newsubtask"></a>NewSubtask
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| list_id |integer |是 |
| task_id |integer |是 |
| title |string |是 |
| 完成 |布林值 |否 |

### <a name="newnote"></a>NewNote
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| list_id |integer |是 |
| task_id |integer |是 |
| 內容 |string |是 |

### <a name="newcomment"></a>NewComment
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| list_id |integer |是 |
| task_id |integer |是 |
| 文字 |string |是 |

### <a name="newreminder"></a>NewReminder
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| list_id |integer |是 |
| task_id |integer |是 |
| 日期 |string |是 |

### <a name="updatetask"></a>UpdateTask
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 修訂 |integer |否 |
| title |string |否 |
| assignee_id |integer |否 |
| 完成 |布林值 |否 |
| recurrence_type |string |否 |
| recurrence_count |integer |否 |
| due_date |string |否 |
| 已加星號 |布林值 |否 |

### <a name="updatelist"></a>UpdateList
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 修訂 |integer |否 |
| title |string |否 |

### <a name="updatesubtask"></a>UpdateSubtask
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 修訂 |integer |否 |
| title |string |否 |
| 完成 |布林值 |否 |

### <a name="updatenote"></a>UpdateNote
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 修訂 |integer |否 |
| 內容 |string |否 |

### <a name="updatereminder"></a>UpdateReminder
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 日期 |string |否 |
| 修訂 |integer |否 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


