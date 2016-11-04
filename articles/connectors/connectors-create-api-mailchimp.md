---
title: MailChimp | Microsoft Docs
description: 使用 Azure App Service 建立邏輯應用程式。MailChimp 是一項 SaaS 服務，可讓企業管理和自動化電子郵件行銷活動，包括傳送行銷電子郵件、自動化的訊息和有目標的行銷活動。
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# 開始使用 MailChimp 連接器
MailChimp 是一項 SaaS 服務，可讓企業管理和自動化電子郵件行銷活動，包括傳送行銷電子郵件、自動化的訊息和有目標的行銷活動。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
MailChimp 連接器可當成動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 MailChimp 連接器提供下列動作及/或觸發程序：

### MailChimp 動作
您可以採取下列動作：

| 動作 | 說明 |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |根據活動類型、收件者清單和活動設定 (主旨列、標題、from\_name 和 reply\_to) 建立新的活動 |
| [newlist](connectors-create-api-mailchimp.md#newlist) |在 MailChimp 帳戶中建立新的清單 |
| [addmember](connectors-create-api-mailchimp.md#addmember) |加入或更新清單成員 |
| [removemember](connectors-create-api-mailchimp.md#removemember) |從清單中刪除成員。 |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |更新特定清單成員的資訊 |

### MailChimp 觸發程序
您可以接聽下列事件：

| 觸發程序 | 說明 |
| --- | --- |
| 成員加入清單後 |新成員加入清單後就會觸發工作流程 |
| 建立新清單時 |建立新清單時就會觸發工作流程 |

## 建立 MailChimp 的連線
若要使用 MailChimp 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 MailChimp 認證 |

> [!INCLUDE [建立 MailChimp 連線的步驟](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> 您可以在其他邏輯應用程式中使用這個連接。
> 
> 

## MailChimp 的參考
適用的版本：1.0

## newcampaign
新活動：根據活動類型、收件者清單和活動設定 (主旨列、標題、from\_name 和 reply\_to) 建立新的活動

```POST: /campaigns```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |yes |body |無 |隨新活動要求參數在本文中傳送的 JSON 物件 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤 |
| 預設值 |作業失敗。 |

## newlist
新清單：在 MailChimp 帳戶中建立新的清單

```POST: /lists```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |yes |body |無 |隨新活動要求參數在本文中傳送的 JSON 物件 |

#### Response
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤 |
| 預設值 |作業失敗。 |

## addmember
將成員加入清單中︰加入或更新清單成員

```POST: /lists/{list_id}/members```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |路徑 |無 |清單的唯一識別碼 |
| newMemberInList | |yes |body |無 |隨新成員資訊在本文中傳送的 JSON 物件 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤 |
| 預設值 |作業失敗。 |

## removemember
從清單中移除成員︰從清單中刪除成員。

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |路徑 |無 |清單的唯一識別碼 |
| member\_email |string |yes |路徑 |無 |要刪除的成員電子郵件地址 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤 |
| 預設值 |作業失敗。 |

## updatemember
更新成員資訊：更新特定清單成員的資訊

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |路徑 |無 |清單的唯一識別碼 |
| member\_email |string |yes |路徑 |無 |要更新成員的唯一電子郵件地址 |
| updateMemberInListRequest | |yes |body |無 |隨更新的成員資訊在本文中傳送的 JSON 物件 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤 |
| 預設值 |作業失敗。 |

## OnMemberSubscribed
成員加入清單後：新成員加入清單後就會觸發工作流程

```GET: /trigger/lists/{list_id}/members```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |路徑 |無 |清單的唯一識別碼 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤 |
| 預設值 |作業失敗。 |

## OnCreateList
建立新清單時：建立新清單時就會觸發工作流程

```GET: /trigger/lists```

這個呼叫沒有參數

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤 |
| 預設值 |作業失敗。 |

## 物件定義
### NewCampaignRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 類型 |string |是 |
| 收件者 |沒有定義 |是 |
| settings |沒有定義 |是 |
| variate\_settings |沒有定義 |否 |
| tracking |沒有定義 |否 |
| rss\_opts |沒有定義 |否 |
| social\_card |沒有定義 |否 |

### 收件者
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| list\_id |string |是 |
| segment\_opts |沒有定義 |否 |

### Settings
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| subject\_line |string |是 |
| title |string |否 |
| from\_name |string |是 |
| reply\_to |string |是 |
| use\_conversation |布林值 |否 |
| to\_name |string |否 |
| folder\_id |integer |否 |
| authenticate |布林值 |否 |
| auto\_footer |布林值 |否 |
| inline\_css |布林值 |否 |
| auto\_tweet |布林值 |否 |
| auto\_fb\_post |array |否 |
| fb\_comments |布林值 |否 |

### Variate\_Settings
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| winner\_criteria |string |否 |
| wait\_time |integer |否 |
| test\_size |integer |否 |
| subject\_lines |array |否 |
| send\_times |array |否 |
| from\_names |array |否 |
| reply\_to\_addresses |array |否 |

### 追蹤
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| opens |布林值 |否 |
| html\_clicks |布林值 |否 |
| text\_clicks |布林值 |否 |
| goal\_tracking |布林值 |否 |
| ecomm360 |布林值 |否 |
| google\_analytics |string |否 |
| clicktale |string |否 |
| salesforce |沒有定義 |否 |
| highrise |沒有定義 |否 |
| capsule |未定義 |否 |

### RSS\_Opts
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| feed\_url |string |否 |
| frequency |string |否 |
| constrain\_rss\_img |string |否 |
| schedule |沒有定義 |否 |

### Social\_Card
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| image\_url |string |否 |
| 說明 |string |否 |
| title |string |否 |

### Segment\_Opts
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| saved\_segment\_id |integer |否 |
| match |string |否 |

### Salesforce
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| campaign |布林值 |否 |
| 版本 |布林值 |否 |

### Highrise
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| campaign |布林值 |否 |
| 版本 |布林值 |否 |

### Capsule
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 版本 |布林值 |否 |

### 排程
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| hour |integer |否 |
| daily\_send |沒有定義 |否 |
| weekly\_send\_day |string |否 |
| monthly\_send\_date |number |否 |

### Daily\_Send
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 星期日 |布林值 |否 |
| 星期一 |布林值 |否 |
| 星期二 |布林值 |否 |
| 星期三 |布林值 |否 |
| 星期四 |布林值 |否 |
| 星期五 |布林值 |否 |
| 星期六 |布林值 |否 |

### CampaignResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| 類型 |string |否 |
| create\_time |string |否 |
| archive\_url |string |否 |
| status |string |否 |
| emails\_sent |integer |否 |
| send\_time |string |否 |
| content\_type |string |否 |
| 收件者 |array |否 |
| settings |沒有定義 |否 |
| variate\_settings |沒有定義 |否 |
| tracking |沒有定義 |否 |
| rss\_opts |沒有定義 |否 |
| ab\_split\_opts |沒有定義 |否 |
| social\_card |沒有定義 |否 |
| report\_summary |沒有定義 |否 |
| delivery\_status |沒有定義 |否 |
| \_links |array |否 |

### AB\_Split\_Opts
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| split\_test |string |否 |
| pick\_winner |string |否 |
| wait\_units |string |否 |
| wait\_time |integer |否 |
| split\_size |integer |否 |
| from\_name\_a |string |否 |
| from\_name\_b |string |否 |
| reply\_email\_a |string |否 |
| reply\_email\_b |string |否 |
| subject\_a |string |否 |
| subject\_b |string |否 |
| send\_time\_a |string |否 |
| send\_time\_b |string |否 |
| send\_time\_winner |string |否 |

### Report\_Summary
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| opens |integer |否 |
| unique\_opens |integer |否 |
| open\_rate |number |否 |
| clicks |integer |否 |
| subscriber\_clicks |number |否 |
| click\_rate |number |否 |

### Delivery\_Status
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 啟用 |布林值 |否 |
| can\_cancel |布林值 |否 |
| status |string |否 |
| emails\_sent |integer |否 |
| emails\_canceled |integer |否 |

### 連結
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| rel |string |否 |
| href |string |否 |
| 方法 |string |否 |
| targetSchema |string |否 |
| 結構描述 |string |否 |

### NewListRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |string |是 |
| 連絡人 |沒有定義 |是 |
| permission\_reminder |string |是 |
| use\_archive\_bar |布林值 |否 |
| campaign\_defaults |沒有定義 |是 |
| notify\_on\_subscribe |string |否 |
| notify\_on\_unsubscribe |string |否 |
| email\_type\_option |布林值 |是 |
| 可見性 |string |否 |

### 連絡人
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| company |string |是 |
| address1 |string |是 |
| address2 |string |否 |
| city |string |是 |
| state |string |是 |
| zip |string |是 |
| country |string |是 |
| 電話 |string |是 |

### Campaign\_Defaults
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| from\_name |string |是 |
| from\_email |string |是 |
| 主旨 |string |否 |
| 語言 |string |是 |

### CreateNewListResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |是 |
| 名稱 |string |是 |
| 連絡人 |沒有定義 |是 |
| permission\_reminder |string |是 |
| use\_archive\_bar |布林值 |否 |
| campaign\_defaults |沒有定義 |是 |
| notify\_on\_subscribe |string |否 |
| notify\_on\_unsubscribe |string |否 |
| date\_created |string |否 |
| list\_rating |integer |否 |
| email\_type\_option |布林值 |是 |
| subscribe\_url\_short |string |否 |
| subscribe\_url\_long |string |否 |
| beamer\_address |string |否 |
| 可見性 |string |否 |
| 模組 |array |否 |
| stats |沒有定義 |否 |
| \_links |array |否 |

### 狀態
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| member\_count |integer |否 |
| unsubscribe\_count |integer |否 |
| cleaned\_count |integer |否 |
| member\_count\_since\_send |integer |否 |
| unsubscribe\_count\_since\_send |integer |否 |
| cleaned\_count\_since\_send |integer |否 |
| campaign\_count |integer |否 |
| campaign\_last\_sent |integer |否 |
| merge\_field\_count |integer |否 |
| avg\_sub\_rate |number |否 |
| avg\_unsub\_rate |number |否 |
| target\_sub\_rate |number |否 |
| open\_rate |number |否 |
| click\_rate |number |否 |
| last\_sub\_date |string |否 |
| last\_unsub\_date |string |否 |

### GetListsResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 清單 |array |否 |
| total\_items |integer |否 |

### NewMemberInListRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| email\_type |string |否 |
| status |string |是 |
| merge\_fields |沒有定義 |否 |
| interests |string |否 |
| 語言 |string |否 |
| vip |布林值 |否 |
| location |沒有定義 |否 |
| email\_address |string |是 |

### FirstAndLastName
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| FNAME |string |否 |
| LNAME |string |否 |

### 位置
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 緯度 |number |否 |
| 經度 |number |否 |

### MemberResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| email\_address |string |否 |
| unique\_email\_id |string |否 |
| email\_type |string |否 |
| status |string |否 |
| merge\_fields |沒有定義 |否 |
| interests |string |否 |
| stats |沒有定義 |否 |
| ip\_signup |string |否 |
| timestamp\_signup |string |否 |
| ip\_opt |string |否 |
| timestamp\_opt |string |否 |
| member\_rating |integer |否 |
| last\_changed |string |否 |
| 語言 |string |否 |
| vip |布林值 |否 |
| email\_client |string |否 |
| location |沒有定義 |否 |
| last\_note |沒有定義 |否 |
| list\_id |string |否 |
| \_links |array |否 |

### Last\_Note
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| note\_id |integer |否 |
| created\_at |string |否 |
| created\_by |string |否 |
| 注意 |string |否 |

### GetAllMembersResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| members |array |否 |
| list\_id |string |否 |
| total\_items |integer |否 |

### Object
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
|  | | |

### UpdateMemberInListRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| email\_address |string |否 |
| email\_type |string |否 |
| status |string |是 |
| merge\_fields |沒有定義 |否 |
| interests |string |否 |
| 語言 |string |否 |
| vip |布林值 |否 |
| location |沒有定義 |否 |

### GetMembersResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| members |array |否 |
| list\_id |string |否 |
| total\_items |integer |否 |

### AddUserResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |是 |
| email\_address |string |是 |
| unique\_email\_id |string |否 |
| email\_type |string |否 |
| status |string |否 |
| merge\_fields |沒有定義 |是 |
| interests |string |否 |
| stats |沒有定義 |否 |
| ip\_signup |string |否 |
| timestamp\_signup |string |否 |
| ip\_opt |string |否 |
| timestamp\_opt |string |否 |
| member\_rating |integer |否 |
| last\_changed |string |否 |
| 語言 |string |否 |
| vip |布林值 |否 |
| email\_client |string |否 |
| location |沒有定義 |否 |
| last\_note |沒有定義 |否 |
| list\_id |string |否 |
| \_links |array |否 |

## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->