<properties
pageTitle="Trello | Microsoft Azure"
description="使用 Azure App Service 建立邏輯應用程式。Trello 讓您無論在辦公室及在家都能管理所有專案。您可以用簡單、免費、具彈性且以視覺的方式來管理專案並組織所有項目。連線到 Trello 以管理面板、清單和卡片"
services="app-servicelogic"	
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
ms.date="05/18/2016"
ms.author="deonhe"/>

# 開始使用 Trello 連接器



您可從下列位置使用 Trello 連接器︰

- [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flow](http://flows.microsoft.com)

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以從立即建立邏輯應用程式開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

Trello 連接器可當成動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 Trello 連接器提供下列動作及/或觸發程序：

### Trello 動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|列出面板中的卡片|
|[GetCard](connectors-create-api-trello.md#getcard)|依識別碼取得卡片|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|更新卡片|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|刪除卡片|
|[CreateCard](connectors-create-api-trello.md#createcard)|在 Trello 帳戶中建立新的卡片|
|[ListBoards](connectors-create-api-trello.md#listboards)|列出面板|
|[GetBoard](connectors-create-api-trello.md#getboard)|依識別碼取得面板|
|[ListLists](connectors-create-api-trello.md#listlists)|列出面板中的卡片清單|
|[GetList](connectors-create-api-trello.md#getlist)|依識別碼取得清單|
### Trello 觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|
|當新的卡片加入面板時|當新的卡片加入面板時，觸發流程|
|當新的卡片加入清單時|當新的卡片加入清單時，觸發流程|


## 建立至 Trello 的連線
若要使用 Trello 建立邏輯應用程式，您必須先建立**連線**，然後提供下列屬性的詳細資料︰

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 Trello 認證|
建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。

>[AZURE.INCLUDE [建立至 Trello 連線的步驟](../../includes/connectors-create-api-trello.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## Trello 的參考
適用的版本：1.0

## OnNewCardInBoard
當新的卡片加入面板時︰當新的卡片加入面板時，觸發流程

```GET: /trigger/boards/{board_id}/cards```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|路徑|無|要在其中擷取卡片的面板唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## OnNewCardInList
當新的卡片加入清單時︰當新的卡片加入清單時，觸發流程

```GET: /trigger/lists/{list_id}/cards```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|query|無|要在其中擷取卡片的面板唯一識別碼|
|list\_id|字串|yes|路徑|無|要在其中擷取卡片的清單唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## ListCards
列出面板中的卡片︰列出面板中的卡片

```GET: /boards/{board_id}/cards```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|路徑|無|要擷取所有卡片的面板識別碼|
|actions|字串|no|query|無|列出要傳回的動作。指定 'All' 或有效值的逗號分隔清單|
|附件|布林值|no|query|無|顯示附件|
|attachment\_fields|字串|no|query|無|列出要傳回的附件欄位。指定 'All' 或有效值的逗號分隔清單|
|貼紙|布林值|no|query|無|顯示貼紙|
|members|布林值|no|query|無|顯示成員|
|memeber\_fields|字串|no|query|無|列出要傳回的成員欄位。指定 'All' 或有效值的逗號分隔清單|
|CheckItemStates|布林值|no|query|無|傳回卡片的狀態|
|檢查清單|字串|no|query|無|顯示檢查清單|
|limit|integer|no|query|無|要傳回的結果數目上限，介於 1 到 1000 之間。|
|自|字串|no|query|無|擷取此日期之後的所有卡片|
|之前|字串|no|query|無|擷取此日期之前的所有卡片|
|filter|字串|no|query|無|篩選回應|
|fields|字串|no|query|無|列出要傳回的卡片欄位。指定 'All' 或有效值的逗號分隔清單|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## GetCard
依識別碼取得卡片︰依識別碼取得卡片

```GET: /cards/{card_id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|query|無|要在其中擷取卡片的面板識別碼|
|card\_id|字串|yes|路徑|無|要擷取卡片的識別碼|
|actions|字串|no|query|無|列出要傳回的動作。指定 'All' 或有效值的逗號分隔清單|
|actions\_entities|布林值|no|query|無|傳回動作的實體|
|actions\_display|布林值|no|query|無|傳回動作會顯示|
|actions\_limit|integer|no|query|無|要傳回之動作的最大數目|
|action\_fields|字串|no|query|無|每個動作要傳回的動作欄位清單。指定 'All' 或有效值的逗號分隔清單|
|action\_memberCreator\_fields|字串|no|query|無|要傳回的動作成員建立者欄位清單。指定 'All' 或有效值的逗號分隔清單|
|附件|布林值|no|query|無|傳回附件|
|attachement\_fields|字串|no|query|無|每個附件要傳回的附件欄位清單。指定 'All' 或有效值的逗號分隔清單|
|members|布林值|no|query|無|傳回成員|
|member\_fields|字串|no|query|無|每個成員要傳回的成員欄位清單。指定 'All' 或有效值的逗號分隔清單|
|membersVoted|布林值|no|query|無|傳回投票的成員|
|memberVoted\_fields|字串|no|query|無|每個投票成員要傳回的投票成員欄位清單。指定 'All' 或有效值的逗號分隔清單|
|checkItemStates|布林值|no|query|無|傳回卡片的狀態|
|checkItemState\_fields|字串|no|query|無|每個卡片項目狀態要傳回的狀態欄位清單。指定 'All' 或有效值的逗號分隔清單|
|檢查清單|字串|no|query|無|傳回檢查清單|
|checklist\_fields|字串|no|query|無|每個檢查清單要傳回的檢查清單欄位清單。指定 'All' 或有效值的逗號分隔清單|
|面板|布林值|no|query|無|傳回卡片所屬的面板|
|board\_fields|字串|no|query|無|列出要傳回的面板欄位。指定 'All' 或有效值的逗號分隔清單|
|list|布林值|no|query|無|傳回卡片所屬的清單|
|list\_fields|字串|no|query|無|列出要傳回的清單欄位。指定 'All' 或有效值的逗號分隔清單|
|貼紙|布林值|no|query|無|傳回貼紙|
|sticker\_fields|字串|no|query|無|列出每個貼紙要傳回的貼紙欄位。指定 'All' 或有效值的逗號分隔清單|
|fields|字串|no|query|無|列出要傳回的卡片欄位。指定 'All' 或有效值的逗號分隔清單|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## UpdateCard
更新卡片︰更新卡片

```PUT: /cards/{card_id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|query|無|要自其中擷取卡片的面板識別碼|
|card\_id|字串|yes|路徑|無|要更新卡片的識別碼|
|updateCard| |yes|body|無|已更新的卡片值|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## DeleteCard
刪除卡片：刪除卡片

```DELETE: /cards/{card_id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|query|無|要自其中擷取卡片的面板識別碼|
|card\_id|字串|yes|路徑|無|要刪除卡片的識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## CreateCard
建立卡片：在 Trello 帳戶中建立新的卡片

```POST: /cards```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|query|無|要在其中建立卡片的面板唯一識別碼|
|newCard| |yes|body|無|要加入至 Trello 面板的新卡片|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## ListBoards
列出面版︰列出面板

```GET: /member/me/boards```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|filter|字串|no|query|無|列出套用至面板結果的篩選。指定 'All' 或有效值的逗號分隔清單|
|fields|字串|no|query|無|列出要傳回的面板欄位。指定 'All' 或有效值的逗號分隔清單|
|actions|字串|no|query|無|列出要傳回的動作欄位。指定 'All' 或有效值的逗號分隔清單|
|actions\_entities|布林值|no|query|無|傳回動作的實體|
|actions\_limit|integer|no|query|無|要傳回之動作的最大數目|
|actions\_format|字串|no|query|無|指定要傳回的動作格式|
|actions\_since|字串|no|query|無|傳回指定日期之後的動作|
|action\_fields|字串|no|query|無|列出要傳回的動作欄位。指定 'All' 或有效值的逗號分隔清單|
|成員資格|字串|no|query|無|指定要傳回的成員資格資訊。指定 'All' 或有效值的逗號分隔清單|
|組織|布林值|no|query|無|指定要傳回的組織資訊|
|organization\_fields|字串|no|query|無|列出要傳回的組織欄位。指定 'All' 或有效值的逗號分隔清單|
|清單|字串|no|query|無|指定是否要傳回所屬面板的清單|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## GetBoard
依識別碼取得面板︰依識別碼取得面板

```GET: /boards/{board_id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|路徑|無|要取得的面板唯一識別碼|
|actions|字串|no|query|無|列出要傳回的動作。指定 'All' 或有效值的逗號分隔清單|
|action\_entities|布林值|no|query|無|指定是否要傳回動作實體|
|actions\_display|布林值|no|query|無|指定是否要傳回所顯示的動作|
|actions\_format|字串|no|query|無|指定要傳回的動作格式|
|actions\_since|字串|no|query|無|只會傳回在此日期之後的動作|
|actions\_limit|integer|no|query|無|要傳回之動作的最大數目|
|action\_fields|字串|no|query|無|列出每個欄位要傳回的欄位。指定 'All' 或有效值的逗號分隔清單|
|action\_memeber|布林值|no|query|無|指定是否要傳回動作成員|
|action\_member\_fields|字串|no|query|無|列出每個動作成員要傳回的成員欄位。指定 'All' 或有效值的逗號分隔清單|
|action\_memberCreator|布林值|no|query|無|指定是否要傳回動作成員的建立者|
|action\_memberCreator\_fields|字串|no|query|無|列出要傳回的動作成員建立者欄位。指定 'All' 或有效值的逗號分隔清單|
|卡片|字串|no|query|無|指定要傳回的卡片|
|card\_fields|字串|no|query|無|列出每個卡片要傳回的欄位。指定 'All' 或有效值的逗號分隔清單|
|card\_attachments|布林值|yes|query|無|指定是否要在卡片上傳回附件|
|card\_attachment\_fields|字串|no|query|無|列出每個附件要傳回的附件欄位。指定 'All' 或有效值的逗號分隔清單|
|card\_checklists|字串|no|query|無|指定每張卡片要傳回的檢查清單|
|card\_stickers|布林值|no|query|無|指定是否要傳回卡片貼紙|
|boardStarts|字串|no|query|無|指定要傳回的面板顆星|
|標籤|字串|no|query|無|指定要傳回的標籤|
|label\_fields|字串|no|query|無|列出每個標籤要傳回的標籤欄位。指定 'All' 或有效值的逗號分隔清單|
|labels\_limits|integer|no|query|無|要傳回之標籤的最大數目|
|清單|字串|no|query|無|指定要傳回的清單|
|list\_fields|字串|no|query|無|列出每個清單要傳回的清單欄位。指定 'All' 或有效值的逗號分隔清單|
|成員資格|字串|no|query|無|列出要傳回的成員資格。指定 'All' 或有效值的逗號分隔清單|
|memberships\_member|布林值|no|query|無|指定是否要傳回成員資格的成員|
|memberships\_member\_fields|字串|no|query|無|列出每個成員資格的成員要傳回的成員欄位。指定 'All' 或有效值的逗號分隔清單|
|members|字串|no|query|無|列出要傳回的成員。|
|member\_fields|字串|no|query|無|列出每個成員要傳回的成員欄位。指定 'All' 或有效值的逗號分隔清單|
|membersInvited|字串|no|query|無|指定要傳回的受邀成員|
|membersInvited\_fields|字串|no|query|無|列出每個要傳回的欄位。指定 'All' 或有效值的逗號分隔清單|
|檢查清單|字串|no|query|無|指定要傳回的檢查清單|
|checklist\_fields|字串|no|query|無|列出每個檢查清單要傳回的檢查清單欄位。指定 'All' 或有效值的逗號分隔清單|
|組織|布林值|no|query|無|指定是否要傳回組織資訊|
|organization\_fields|字串|no|query|無|列出每個組織要傳回的組織欄位。指定 'All' 或有效值的逗號分隔清單|
|organization\_memberships|字串|no|query|無|列出要傳回的組織成員資格。指定 'All' 或有效值的逗號分隔清單|
|myPerfs|布林值|no|query|無|指定是否要傳回我的 Perfs|
|fields|字串|no|query|無|列出要傳回的欄位。指定 'All' 或有效值的逗號分隔清單|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## ListLists
列出面板中的卡片清單︰列出在面板中的卡片清單

```GET: /boards/{board_id}/lists```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|路徑|無|要擷取清單的面板唯一識別碼|
|卡片|字串|no|query|無|指定要傳回的卡片|
|card\_fields|字串|no|query|無|列出要自其中傳回的卡片欄位。指定 'All' 或有效值的逗號分隔清單|
|filter|字串|no|query|無|指定清單的篩選屬性|
|fields|字串|no|query|無|列出要傳回的欄位。指定 'All' 或有效值的逗號分隔清單|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## GetList
依識別碼取得清單︰依識別碼取得清單

```GET: /lists/{list_id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|board\_id|字串|yes|query|無|要自其中擷取清單的面板唯一識別碼|
|list\_id|字串|yes|路徑|無|要擷取的清單唯一識別碼|
|卡片|字串|no|query|無|指定要傳回的卡片|
|card\_fields|字串|no|query|無|列出每個卡片要傳回的卡片欄位。指定 'All' 或有效值的逗號分隔清單|
|面板|布林值|no|query|無|指定是否要傳回面板資訊|
|board\_fields|字串|no|query|無|列出要傳回的面板欄位。指定 'All' 或有效值的逗號分隔清單|
|fields|字串|no|query|無|列出要傳回的清單欄位。指定 'All' 或有效值的逗號分隔清單|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗|


## 物件定義 

### 卡片


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|字串|否 |
|checkItemStates|array|否 |
|關閉|布林值|否 |
|dateLastActivity|字串|否 |
|desc|字串|否 |
|idBoard|字串|否 |
|idList|字串|否 |
|idMembersVoted|array|否 |
|idShort|integer|否 |
|idAttachmentCover|字串|否 |
|manualCoverAttachment|布林值|否 |
|idLabels|array|否 |
|名稱|字串|否 |
|pos|integer|否 |
|shortLink|字串|否 |
|徽章|沒有定義|否 |
|due|字串|否 |
|電子郵件|字串|否 |
|shortUrl|字串|否 |
|已訂閱|布林值|否 |
|url|字串|否 |



### 徽章


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|(0 人投票)|integer|否 |
|ViewingMemberVoted|布林值|否 |
|已訂閱|布林值|否 |
|Fogbugz|字串|否 |
|CheckItems|integer|否 |
|CheckItemsChecked|integer|否 |
|註解|integer|否 |
|附件|integer|否 |
|說明|布林值|否 |
|到期|字串|否 |



### Object


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|



### CreateCard


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|idList|字串|是 |
|名稱|字串|是 |
|desc|字串|否 |
|pos|字串|否 |
|idMembers|字串|否 |
|idLabels|字串|否 |
|urlSource|字串|否 |
|fileSource|字串|否 |
|idCardSource|字串|否 |
|keepFromSource|字串|否 |



### UpdateCard


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|否 |
|desc|字串|否 |
|關閉|布林值|否 |
|idMembers|字串|否 |
|idAttachmentCover|字串|否 |
|idList|字串|否 |
|idBoard|字串|否 |
|pos|字串|否 |
|due|字串|否 |
|已訂閱|布林值|否 |



### 面板


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|字串|否 |
|關閉|布林值|否 |
|dateLastActivity|字串|否 |
|dateLastView|字串|否 |
|desc|字串|否 |
|idOrganization|字串|否 |
|邀請|array|否 |
|受邀|布林值|否 |
|labelNames|沒有定義|否 |
|成員資格|array|否 |
|名稱|字串|否 |
|已釘選|布林值|否 |
|powerUps|array|否 |
|perfs|沒有定義|否 |
|shortLink|字串|否 |
|shortUrl|字串|否 |
|已加星號|字串|否 |
|已訂閱|字串|否 |
|url|字串|否 |



### 標籤


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|綠色|字串|否 |
|黃色|字串|否 |
|橙色|字串|否 |
|紅色|字串|否 |
|紫色|字串|否 |
|藍色|字串|否 |
|淡藍色|字串|否 |
|淺綠色|字串|否 |
|粉紅色|字串|否 |
|黑色|字串|否 |



### 成員資格


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|字串|否 |
|idMember|字串|否 |
|memberType|字串|否 |
|未確認|布林值|否 |



### Perfs


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|permissionLevel|字串|否 |
|投票|字串|否 |
|註解|字串|否 |
|邀請|字串|否 |
|selfJoin|布林值|否 |
|cardCovers|布林值|否 |
|calendarFeedEnabled|布林值|否 |
|背景資訊|字串|否 |
|backgroundColor|字串|否 |
|backgroundImage|字串|否 |
|backgroundImageScaled|字串|否 |
|backgroundTile|布林值|否 |
|backgroundBrightness|字串|否 |
|canBePublic|布林值|否 |
|canBeOrg|布林值|否 |
|canBePrivate|布林值|否 |
|canInvite|布林值|否 |



### 列出


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|字串|否 |
|名稱|字串|否 |
|關閉|布林值|否 |
|idBoard|字串|否 |
|pos|number|否 |
|已訂閱|布林值|否 |
|卡片|array|否 |
|面板|沒有定義|否 |


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->