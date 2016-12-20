---
title: Trello | Microsoft Azure
description: "使用 Azure App Service 建立邏輯應用程式。 Trello 讓您無論在辦公室及在家都能管理所有專案。  您可以用簡單、免費、具彈性且以視覺的方式來管理專案並組織所有項目。  連線到 Trello 以管理面板、清單和卡片"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d7a8111e299e846eb384f97bdef5a8c214214578


---
# <a name="get-started-with-the-trello-connector"></a>開始使用 Trello 連接器
Trello 讓您無論在辦公室及在家都能管理所有專案。  您可以用簡單、免費、具彈性且以視覺的方式來管理專案並組織所有項目。  連線到 Trello 以管理面板、清單和卡片。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Trello 連接器可當成動作使用，它有觸發程序。 所有連接器都支援 JSON 和 XML 格式的資料。

 Trello 連接器提供下列動作及/或觸發程序：

### <a name="trello-actions"></a>Trello 動作
您可以採取下列動作：

| 動作 | 說明 |
| --- | --- |
| [ListCards](connectors-create-api-trello.md#listcards) |列出面板中的卡片 |
| [GetCard](connectors-create-api-trello.md#getcard) |依識別碼取得卡片 |
| [UpdateCard](connectors-create-api-trello.md#updatecard) |更新卡片 |
| [DeleteCard](connectors-create-api-trello.md#deletecard) |刪除卡片 |
| [CreateCard](connectors-create-api-trello.md#createcard) |在 Trello 帳戶中建立新的卡片 |
| [ListBoards](connectors-create-api-trello.md#listboards) |列出面板 |
| [GetBoard](connectors-create-api-trello.md#getboard) |依識別碼取得面板 |
| [ListLists](connectors-create-api-trello.md#listlists) |列出面板中的卡片清單 |
| [GetList](connectors-create-api-trello.md#getlist) |依識別碼取得清單 |

### <a name="trello-triggers"></a>Trello 觸發程序
您可以接聽下列事件：

| 觸發程序 | 說明 |
| --- | --- |
| 當新的卡片加入面板時 |當新的卡片加入面板時，觸發流程 |
| 當新的卡片加入清單時 |當新的卡片加入清單時，觸發流程 |

## <a name="create-a-connection-to-trello"></a>建立至 Trello 的連線
若要使用 Trello 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料：

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 Trello 認證 |

建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。

> [!INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]
> 
> [!TIP]
> 您可以在其他邏輯應用程式中使用這個連線。
> 
> 

## <a name="reference-for-trello"></a>Trello 的參考
適用的版本：1.0

## <a name="onnewcardinboard"></a>OnNewCardInBoard
當新的卡片加入面板時︰當新的卡片加入面板時，觸發流程

```GET: /trigger/boards/{board_id}/cards```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |是 |路徑 |無 |要在其中擷取卡片的面板唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="onnewcardinlist"></a>OnNewCardInList
當新的卡片加入清單時︰當新的卡片加入清單時，觸發流程

```GET: /trigger/lists/{list_id}/cards```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |無 |要在其中擷取卡片的面板唯一識別碼 |
| list_id |string |是 |路徑 |無 |要在其中擷取卡片的清單唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="listcards"></a>ListCards
列出面板中的卡片︰列出面板中的卡片

```GET: /boards/{board_id}/cards```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |是 |路徑 |無 |要擷取所有卡片的面板識別碼 |
| actions |string |no |query |無 |列出要傳回的動作。 指定 'All' 或有效值的逗號分隔清單 |
| 附件 |布林值 |no |query |無 |顯示附件 |
| attachment_fields |string |no |query |無 |列出要傳回的附件欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 貼紙 |布林值 |no |query |無 |顯示貼紙 |
| members |布林值 |no |query |無 |顯示成員 |
| memeber_fields |string |no |query |無 |列出要傳回的成員欄位。 指定 'All' 或有效值的逗號分隔清單 |
| CheckItemStates |布林值 |no |query |無 |傳回卡片的狀態 |
| 檢查清單 |string |no |query |無 |顯示檢查清單 |
| limit |integer |no |query |無 |要傳回的結果數目上限，介於 1 到 1000 之間。 |
| 自 |string |no |query |無 |擷取此日期之後的所有卡片 |
| 之前 |string |no |query |無 |擷取此日期之前的所有卡片 |
| filter |字串 |no |query |無 |篩選回應 |
| fields |字串 |no |query |無 |列出要傳回的卡片欄位。 指定 'All' 或有效值的逗號分隔清單 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="getcard"></a>GetCard
依識別碼取得卡片︰依識別碼取得卡片

```GET: /cards/{card_id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |無 |要在其中擷取卡片的面板識別碼 |
| card_id |string |是 |路徑 |無 |要擷取卡片的識別碼 |
| actions |string |no |query |無 |列出要傳回的動作。 指定 'All' 或有效值的逗號分隔清單 |
| actions_entities |布林值 |no |query |無 |傳回動作的實體 |
| actions_display |布林值 |no |query |無 |傳回動作會顯示 |
| actions_limit |integer |no |query |無 |要傳回之動作的最大數目 |
| action_fields |string |no |query |無 |每個動作要傳回的動作欄位清單。 指定 'All' 或有效值的逗號分隔清單 |
| action_memberCreator_fields |string |no |query |無 |要傳回的動作成員建立者欄位清單。 指定 'All' 或有效值的逗號分隔清單 |
| 附件 |布林值 |no |query |無 |傳回附件 |
| attachement_fields |string |no |query |無 |每個附件要傳回的附件欄位清單。 指定 'All' 或有效值的逗號分隔清單 |
| members |布林值 |no |query |無 |傳回成員 |
| member_fields |string |no |query |無 |每個成員要傳回的成員欄位清單。 指定 'All' 或有效值的逗號分隔清單 |
| membersVoted |布林值 |no |query |無 |傳回投票的成員 |
| memberVoted_fields |string |no |query |無 |每個投票成員要傳回的投票成員欄位清單。 指定 'All' 或有效值的逗號分隔清單 |
| checkItemStates |布林值 |no |query |無 |傳回卡片的狀態 |
| checkItemState_fields |string |no |query |無 |每個卡片項目狀態要傳回的狀態欄位清單。 指定 'All' 或有效值的逗號分隔清單 |
| 檢查清單 |string |no |query |無 |傳回檢查清單 |
| checklist_fields |string |no |query |無 |每個檢查清單要傳回的檢查清單欄位清單。 指定 'All' 或有效值的逗號分隔清單 |
| 面板 |布林值 |no |query |無 |傳回卡片所屬的面板 |
| board_fields |string |no |query |無 |列出要傳回的面板欄位。 指定 'All' 或有效值的逗號分隔清單 |
| list |布林值 |no |query |無 |傳回卡片所屬的清單 |
| list_fields |string |no |query |無 |列出要傳回的清單欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 貼紙 |布林值 |no |query |無 |傳回貼紙 |
| sticker_fields |string |no |query |無 |列出每個貼紙要傳回的貼紙欄位。 指定 'All' 或有效值的逗號分隔清單 |
| fields |字串 |no |query |無 |列出要傳回的卡片欄位。 指定 'All' 或有效值的逗號分隔清單 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="updatecard"></a>UpdateCard
更新卡片︰更新卡片

```PUT: /cards/{card_id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |無 |要自其中擷取卡片的面板識別碼 |
| card_id |string |是 |路徑 |無 |要更新卡片的識別碼 |
| updateCard | |yes |body |無 |已更新的卡片值 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="deletecard"></a>DeleteCard
刪除卡片：刪除卡片

```DELETE: /cards/{card_id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |無 |要自其中擷取卡片的面板識別碼 |
| card_id |string |是 |路徑 |無 |要刪除卡片的識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="createcard"></a>CreateCard
建立卡片：在 Trello 帳戶中建立新的卡片

```POST: /cards```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |無 |要在其中建立卡片的面板唯一識別碼 |
| newCard | |yes |body |無 |要加入至 Trello 面板的新卡片 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="listboards"></a>ListBoards
列出面版︰列出面板

```GET: /member/me/boards```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| filter |字串 |no |query |無 |列出套用至面板結果的篩選。 指定 'All' 或有效值的逗號分隔清單 |
| fields |字串 |no |query |無 |列出要傳回的面板欄位。 指定 'All' 或有效值的逗號分隔清單 |
| actions |string |no |query |無 |列出要傳回的動作欄位。 指定 'All' 或有效值的逗號分隔清單 |
| actions_entities |布林值 |no |query |無 |傳回動作的實體 |
| actions_limit |integer |no |query |無 |要傳回之動作的最大數目 |
| actions_format |string |no |query |無 |指定要傳回的動作格式 |
| actions_since |string |no |query |無 |傳回指定日期之後的動作 |
| action_fields |string |no |query |無 |列出要傳回的動作欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 成員資格 |string |no |query |無 |指定要傳回的成員資格資訊。 指定 'All' 或有效值的逗號分隔清單 |
| 組織 |布林值 |no |query |無 |指定要傳回的組織資訊 |
| organization_fields |string |no |query |無 |列出要傳回的組織欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 清單 |string |no |query |無 |指定是否要傳回所屬面板的清單 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="getboard"></a>GetBoard
依識別碼取得面板︰依識別碼取得面板

```GET: /boards/{board_id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |是 |路徑 |無 |要取得的面板唯一識別碼 |
| actions |string |no |query |無 |列出要傳回的動作。 指定 'All' 或有效值的逗號分隔清單 |
| action_entities |布林值 |no |query |無 |指定是否要傳回動作實體 |
| actions_display |布林值 |no |query |無 |指定是否要傳回所顯示的動作 |
| actions_format |string |no |query |無 |指定要傳回的動作格式 |
| actions_since |string |no |query |無 |只會傳回在此日期之後的動作 |
| actions_limit |integer |no |query |無 |要傳回之動作的最大數目 |
| action_fields |string |no |query |無 |列出每個欄位要傳回的欄位。 指定 'All' 或有效值的逗號分隔清單 |
| action_memeber |布林值 |no |query |無 |指定是否要傳回動作成員 |
| action_member_fields |string |no |query |無 |列出每個動作成員要傳回的成員欄位。 指定 'All' 或有效值的逗號分隔清單 |
| action_memberCreator |布林值 |no |query |無 |指定是否要傳回動作成員的建立者 |
| action_memberCreator_fields |string |no |query |無 |列出要傳回的動作成員建立者欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 卡片 |string |no |query |無 |指定要傳回的卡片 |
| card_fields |string |no |query |無 |列出每個卡片要傳回的欄位。 指定 'All' 或有效值的逗號分隔清單 |
| card_attachments |布林值 |yes |query |無 |指定是否要在卡片上傳回附件 |
| card_attachment_fields |string |no |query |無 |列出每個附件要傳回的附件欄位。 指定 'All' 或有效值的逗號分隔清單 |
| card_checklists |string |no |query |無 |指定每張卡片要傳回的檢查清單 |
| card_stickers |布林值 |no |query |無 |指定是否要傳回卡片貼紙 |
| boardStarts |string |no |query |無 |指定要傳回的面板顆星 |
| 標籤 |string |no |query |無 |指定要傳回的標籤 |
| label_fields |string |no |query |無 |列出每個標籤要傳回的標籤欄位。 指定 'All' 或有效值的逗號分隔清單 |
| labels_limits |integer |no |query |無 |要傳回之標籤的最大數目 |
| 清單 |string |no |query |無 |指定要傳回的清單 |
| list_fields |string |no |query |無 |列出每個清單要傳回的清單欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 成員資格 |string |no |query |無 |列出要傳回的成員資格。 指定 'All' 或有效值的逗號分隔清單 |
| memberships_member |布林值 |no |query |無 |指定是否要傳回成員資格的成員 |
| memberships_member_fields |string |no |query |無 |列出每個成員資格的成員要傳回的成員欄位。 指定 'All' 或有效值的逗號分隔清單 |
| members |string |no |query |無 |列出要傳回的成員。 |
| member_fields |string |no |query |無 |列出每個成員要傳回的成員欄位。 指定 'All' 或有效值的逗號分隔清單 |
| membersInvited |string |no |query |無 |指定要傳回的受邀成員 |
| membersInvited_fields |string |no |query |無 |列出每個要傳回的欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 檢查清單 |string |no |query |無 |指定要傳回的檢查清單 |
| checklist_fields |string |no |query |無 |列出每個檢查清單要傳回的檢查清單欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 組織 |布林值 |no |query |無 |指定是否要傳回組織資訊 |
| organization_fields |string |no |query |無 |列出每個組織要傳回的組織欄位。 指定 'All' 或有效值的逗號分隔清單 |
| organization_memberships |string |no |query |無 |列出要傳回的組織成員資格。 指定 'All' 或有效值的逗號分隔清單 |
| myPerfs |布林值 |no |query |無 |指定是否要傳回我的 Perfs |
| fields |字串 |no |query |無 |列出要傳回的欄位。 指定 'All' 或有效值的逗號分隔清單 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="listlists"></a>ListLists
列出面板中的卡片清單︰列出在面板中的卡片清單

```GET: /boards/{board_id}/lists```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |是 |路徑 |無 |要擷取清單的面板唯一識別碼 |
| 卡片 |string |no |query |無 |指定要傳回的卡片 |
| card_fields |string |no |query |無 |列出要自其中傳回的卡片欄位。 指定 'All' 或有效值的逗號分隔清單 |
| filter |字串 |no |query |無 |指定清單的篩選屬性 |
| fields |字串 |no |query |無 |列出要傳回的欄位。 指定 'All' 或有效值的逗號分隔清單 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="getlist"></a>GetList
依識別碼取得清單︰依識別碼取得清單

```GET: /lists/{list_id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |無 |要自其中擷取清單的面板唯一識別碼 |
| list_id |string |是 |路徑 |無 |要擷取的清單唯一識別碼 |
| 卡片 |string |no |query |無 |指定要傳回的卡片 |
| card_fields |string |no |query |無 |列出每個卡片要傳回的卡片欄位。 指定 'All' 或有效值的逗號分隔清單 |
| 面板 |布林值 |no |query |無 |指定是否要傳回面板資訊 |
| board_fields |string |no |query |無 |列出要傳回的面板欄位。 指定 'All' 或有效值的逗號分隔清單 |
| fields |字串 |no |query |無 |列出要傳回的清單欄位。 指定 'All' 或有效值的逗號分隔清單 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗 |

## <a name="object-definitions"></a>物件定義
### <a name="card"></a>卡片
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| checkItemStates |array |否 |
| 關閉 |布林值 |否 |
| dateLastActivity |string |否 |
| desc |string |否 |
| idBoard |string |否 |
| idList |string |否 |
| idMembersVoted |array |否 |
| idShort |integer |否 |
| idAttachmentCover |string |否 |
| manualCoverAttachment |布林值 |否 |
| idLabels |array |否 |
| 名稱 |string |否 |
| pos |integer |否 |
| shortLink |string |否 |
| 徽章 |沒有定義 |否 |
| due |string |否 |
| 電子郵件 |string |否 |
| shortUrl |string |否 |
| 已訂閱 |布林值 |否 |
| url |string |否 |

### <a name="badges"></a>徽章
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| (0 人投票) |integer |否 |
| ViewingMemberVoted |布林值 |否 |
| 已訂閱 |布林值 |否 |
| Fogbugz |string |否 |
| CheckItems |integer |否 |
| CheckItemsChecked |integer |否 |
| 註解 |integer |否 |
| 附件 |integer |否 |
| 說明 |布林值 |否 |
| 到期 |string |否 |

### <a name="object"></a>Object
### <a name="createcard"></a>CreateCard
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| idList |string |是 |
| 名稱 |字串 |是 |
| desc |string |否 |
| pos |string |否 |
| idMembers |string |否 |
| idLabels |string |否 |
| urlSource |string |否 |
| fileSource |string |否 |
| idCardSource |string |否 |
| keepFromSource |string |否 |

### <a name="updatecard"></a>UpdateCard
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |string |否 |
| desc |string |否 |
| 關閉 |布林值 |否 |
| idMembers |string |否 |
| idAttachmentCover |string |否 |
| idList |string |否 |
| idBoard |string |否 |
| pos |string |否 |
| due |string |否 |
| 已訂閱 |布林值 |否 |

### <a name="board"></a>面板
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| 關閉 |布林值 |否 |
| dateLastActivity |string |否 |
| dateLastView |string |否 |
| desc |string |否 |
| idOrganization |string |否 |
| 邀請 |array |否 |
| 受邀 |布林值 |否 |
| labelNames |沒有定義 |否 |
| 成員資格 |array |否 |
| 名稱 |string |否 |
| 已釘選 |布林值 |否 |
| powerUps |array |否 |
| perfs |沒有定義 |否 |
| shortLink |string |否 |
| shortUrl |string |否 |
| 已加星號 |string |否 |
| 已訂閱 |string |否 |
| url |string |否 |

### <a name="label"></a>標籤
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 綠色 |string |否 |
| 黃色 |string |否 |
| 橙色 |string |否 |
| 紅色 |string |否 |
| 紫色 |string |否 |
| 藍色 |string |否 |
| 淡藍色 |string |否 |
| 淺綠色 |string |否 |
| 粉紅色 |string |否 |
| 黑色 |string |否 |

### <a name="membership"></a>成員資格
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| idMember |string |否 |
| memberType |string |否 |
| 未確認 |布林值 |否 |

### <a name="perfs"></a>Perfs
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| permissionLevel |string |否 |
| 投票 |string |否 |
| 註解 |string |否 |
| 邀請 |string |否 |
| selfJoin |布林值 |否 |
| cardCovers |布林值 |否 |
| calendarFeedEnabled |布林值 |否 |
| 背景資訊 |string |否 |
| backgroundColor |string |否 |
| backgroundImage |string |否 |
| backgroundImageScaled |string |否 |
| backgroundTile |布林值 |否 |
| backgroundBrightness |string |否 |
| canBePublic |布林值 |否 |
| canBeOrg |布林值 |否 |
| canBePrivate |布林值 |否 |
| canInvite |布林值 |否 |

### <a name="list"></a>列出
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| 名稱 |string |否 |
| 關閉 |布林值 |否 |
| idBoard |string |否 |
| pos |number |否 |
| 已訂閱 |布林值 |否 |
| 卡片 |array |否 |
| 面板 |沒有定義 |否 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


