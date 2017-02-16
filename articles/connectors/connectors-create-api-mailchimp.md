---
title: MailChimp | Microsoft Docs
description: "使用 Azure App Service 建立邏輯應用程式。 MailChimp 是一項 SaaS 服務，可讓企業管理和自動化電子郵件行銷活動，包括傳送行銷電子郵件、自動化的訊息和有目標的行銷活動。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: cee2c702c705e1451c0a6d01b140e6291b1e5ce1


---
# <a name="get-started-with-the-mailchimp-connector"></a>開始使用 MailChimp 連接器
MailChimp 是一項 SaaS 服務，可讓企業管理和自動化電子郵件行銷活動，包括傳送行銷電子郵件、自動化的訊息和有目標的行銷活動。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
MailChimp 連接器可當成動作使用，它有觸發程序。 所有連接器都支援 JSON 和 XML 格式的資料。

 MailChimp 連接器提供下列動作及/或觸發程序：

### <a name="mailchimp-actions"></a>MailChimp 動作
您可以採取下列動作：

| 動作 | 說明 |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |根據活動類型、收件者清單和活動設定 (主旨列、標題、from_name 和 reply_to) 建立新的活動 |
| [newlist](connectors-create-api-mailchimp.md#newlist) |在 MailChimp 帳戶中建立新的清單 |
| [addmember](connectors-create-api-mailchimp.md#addmember) |加入或更新清單成員 |
| [removemember](connectors-create-api-mailchimp.md#removemember) |從清單中刪除成員。 |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |更新特定清單成員的資訊 |

### <a name="mailchimp-triggers"></a>MailChimp 觸發程序
您可以接聽下列事件：

| 觸發程序 | 說明 |
| --- | --- |
| 成員加入清單後 |新成員加入清單後就會觸發工作流程 |
| 建立新清單時 |建立新清單時就會觸發工作流程 |

## <a name="create-a-connection-to-mailchimp"></a>建立 MailChimp 的連線
若要使用 MailChimp 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 MailChimp 認證 |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> 您可以在其他邏輯應用程式中使用這個連接。
> 
> 

## <a name="reference-for-mailchimp"></a>MailChimp 的參考
適用的版本：1.0

## <a name="newcampaign"></a>newcampaign
新活動：根據活動類型、收件者清單和活動設定 (主旨列、標題、from_name 和 reply_to) 建立新的活動

```POST: /campaigns```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |yes |body |無 |隨新活動要求參數在本文中傳送的 JSON 物件 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="newlist"></a>newlist
新清單：在 MailChimp 帳戶中建立新的清單

```POST: /lists```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |yes |body |無 |隨新活動要求參數在本文中傳送的 JSON 物件 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="addmember"></a>addmember
將成員加入清單中︰加入或更新清單成員

```POST: /lists/{list_id}/members```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |string |是 |路徑 |無 |清單的唯一識別碼 |
| newMemberInList | |yes |body |無 |隨新成員資訊在本文中傳送的 JSON 物件 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="removemember"></a>removemember
從清單中移除成員︰從清單中刪除成員。

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |string |是 |路徑 |無 |清單的唯一識別碼 |
| member_email |string |是 |路徑 |無 |要刪除的成員電子郵件地址 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="updatemember"></a>updatemember
更新成員資訊：更新特定清單成員的資訊

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |string |是 |路徑 |無 |清單的唯一識別碼 |
| member_email |string |是 |路徑 |無 |要更新成員的唯一電子郵件地址 |
| updateMemberInListRequest | |yes |body |無 |隨更新的成員資訊在本文中傳送的 JSON 物件 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="onmembersubscribed"></a>OnMemberSubscribed
成員加入清單後：新成員加入清單後就會觸發工作流程

```GET: /trigger/lists/{list_id}/members```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| list_id |string |是 |路徑 |無 |清單的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="oncreatelist"></a>OnCreateList
建立新清單時：建立新清單時就會觸發工作流程

```GET: /trigger/lists```

這個呼叫沒有參數

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
### <a name="newcampaignrequest"></a>NewCampaignRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 類型 |string |是 |
| 收件者 |沒有定義 |是 |
| settings |沒有定義 |是 |
| variate_settings |沒有定義 |否 |
| tracking |沒有定義 |否 |
| rss_opts |沒有定義 |否 |
| social_card |沒有定義 |否 |

### <a name="recipient"></a>收件者
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| list_id |string |是 |
| segment_opts |沒有定義 |否 |

### <a name="settings"></a>Settings
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| subject_line |string |是 |
| title |string |否 |
| from_name |string |是 |
| reply_to |string |是 |
| use_conversation |布林值 |否 |
| to_name |string |否 |
| folder_id |integer |否 |
| authenticate |布林值 |否 |
| auto_footer |布林值 |否 |
| inline_css |布林值 |否 |
| auto_tweet |布林值 |否 |
| auto_fb_post |array |否 |
| fb_comments |布林值 |否 |

### <a name="variatesettings"></a>Variate_Settings
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| winner_criteria |string |否 |
| wait_time |integer |否 |
| test_size |integer |否 |
| subject_lines |array |否 |
| send_times |array |否 |
| from_names |array |否 |
| reply_to_addresses |array |否 |

### <a name="tracking"></a>追蹤
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| opens |布林值 |否 |
| html_clicks |布林值 |否 |
| text_clicks |布林值 |否 |
| goal_tracking |布林值 |否 |
| ecomm360 |布林值 |否 |
| google_analytics |string |否 |
| clicktale |string |否 |
| salesforce |沒有定義 |否 |
| highrise |沒有定義 |否 |
| capsule |未定義 |否 |

### <a name="rssopts"></a>RSS_Opts
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| feed_url |string |否 |
| frequency |string |否 |
| constrain_rss_img |string |否 |
| schedule |沒有定義 |否 |

### <a name="socialcard"></a>Social_Card
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| image_url |string |否 |
| 說明 |string |否 |
| title |string |否 |

### <a name="segmentopts"></a>Segment_Opts
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| saved_segment_id |integer |否 |
| match |string |否 |

### <a name="salesforce"></a>Salesforce
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| campaign |布林值 |否 |
| 版本 |布林值 |否 |

### <a name="highrise"></a>Highrise
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| campaign |布林值 |否 |
| 版本 |布林值 |否 |

### <a name="capsule"></a>Capsule
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 版本 |布林值 |否 |

### <a name="schedule"></a>排程
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| hour |integer |否 |
| daily_send |沒有定義 |否 |
| weekly_send_day |string |否 |
| monthly_send_date |number |否 |

### <a name="dailysend"></a>Daily_Send
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 星期日 |布林值 |否 |
| 星期一 |布林值 |否 |
| 星期二 |布林值 |否 |
| 星期三 |布林值 |否 |
| 星期四 |布林值 |否 |
| 星期五 |布林值 |否 |
| 星期六 |布林值 |否 |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| 類型 |string |否 |
| create_time |string |否 |
| archive_url |string |否 |
| status |string |否 |
| emails_sent |integer |否 |
| send_time |string |否 |
| content_type |string |否 |
| 收件者 |array |否 |
| settings |沒有定義 |否 |
| variate_settings |沒有定義 |否 |
| tracking |沒有定義 |否 |
| rss_opts |沒有定義 |否 |
| ab_split_opts |沒有定義 |否 |
| social_card |沒有定義 |否 |
| report_summary |沒有定義 |否 |
| delivery_status |沒有定義 |否 |
| _links |array |否 |

### <a name="absplitopts"></a>AB_Split_Opts
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| split_test |string |否 |
| pick_winner |string |否 |
| wait_units |string |否 |
| wait_time |integer |否 |
| split_size |integer |否 |
| from_name_a |string |否 |
| from_name_b |string |否 |
| reply_email_a |string |否 |
| reply_email_b |string |否 |
| subject_a |string |否 |
| subject_b |string |否 |
| send_time_a |string |否 |
| send_time_b |string |否 |
| send_time_winner |string |否 |

### <a name="reportsummary"></a>Report_Summary
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| opens |integer |否 |
| unique_opens |integer |否 |
| open_rate |number |否 |
| clicks |integer |否 |
| subscriber_clicks |number |否 |
| click_rate |number |否 |

### <a name="deliverystatus"></a>Delivery_Status
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 啟用 |布林值 |否 |
| can_cancel |布林值 |否 |
| status |string |否 |
| emails_sent |integer |否 |
| emails_canceled |integer |否 |

### <a name="link"></a>連結
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| rel |string |否 |
| href |string |否 |
| 方法 |string |否 |
| targetSchema |string |否 |
| 結構描述 |string |否 |

### <a name="newlistrequest"></a>NewListRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |字串 |是 |
| 連絡人 |沒有定義 |是 |
| permission_reminder |string |是 |
| use_archive_bar |布林值 |否 |
| campaign_defaults |沒有定義 |是 |
| notify_on_subscribe |string |否 |
| notify_on_unsubscribe |string |否 |
| email_type_option |布林值 |是 |
| 可見性 |string |否 |

### <a name="contact"></a>連絡人
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

### <a name="campaigndefaults"></a>Campaign_Defaults
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| from_name |string |是 |
| from_email |string |是 |
| 主旨 |string |否 |
| 語言 |string |是 |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |是 |
| 名稱 |字串 |是 |
| 連絡人 |沒有定義 |是 |
| permission_reminder |string |是 |
| use_archive_bar |布林值 |否 |
| campaign_defaults |沒有定義 |是 |
| notify_on_subscribe |string |否 |
| notify_on_unsubscribe |string |否 |
| date_created |string |否 |
| list_rating |integer |否 |
| email_type_option |布林值 |是 |
| subscribe_url_short |string |否 |
| subscribe_url_long |string |否 |
| beamer_address |string |否 |
| 可見性 |string |否 |
| 模組 |array |否 |
| stats |沒有定義 |否 |
| _links |array |否 |

### <a name="stats"></a>狀態
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| member_count |integer |否 |
| unsubscribe_count |integer |否 |
| cleaned_count |integer |否 |
| member_count_since_send |integer |否 |
| unsubscribe_count_since_send |integer |否 |
| cleaned_count_since_send |integer |否 |
| campaign_count |integer |否 |
| campaign_last_sent |integer |否 |
| merge_field_count |integer |否 |
| avg_sub_rate |number |否 |
| avg_unsub_rate |number |否 |
| target_sub_rate |number |否 |
| open_rate |number |否 |
| click_rate |number |否 |
| last_sub_date |string |否 |
| last_unsub_date |string |否 |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 清單 |array |否 |
| total_items |integer |否 |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| email_type |string |否 |
| status |string |是 |
| merge_fields |沒有定義 |否 |
| interests |string |否 |
| 語言 |string |否 |
| vip |布林值 |否 |
| location |沒有定義 |否 |
| email_address |string |是 |

### <a name="firstandlastname"></a>FirstAndLastName
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| FNAME |string |否 |
| LNAME |string |否 |

### <a name="location"></a>位置
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 緯度 |number |否 |
| 經度 |number |否 |

### <a name="memberresponsemodel"></a>MemberResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| email_address |string |否 |
| unique_email_id |string |否 |
| email_type |string |否 |
| status |string |否 |
| merge_fields |沒有定義 |否 |
| interests |string |否 |
| stats |沒有定義 |否 |
| ip_signup |string |否 |
| timestamp_signup |string |否 |
| ip_opt |string |否 |
| timestamp_opt |string |否 |
| member_rating |integer |否 |
| last_changed |string |否 |
| 語言 |string |否 |
| vip |布林值 |否 |
| email_client |string |否 |
| location |沒有定義 |否 |
| last_note |沒有定義 |否 |
| list_id |string |否 |
| _links |array |否 |

### <a name="lastnote"></a>Last_Note
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| note_id |integer |否 |
| created_at |string |否 |
| created_by |string |否 |
| 注意 |string |否 |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| members |array |否 |
| list_id |string |否 |
| total_items |integer |否 |

### <a name="object"></a>Object
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| email_address |string |否 |
| email_type |string |否 |
| status |string |是 |
| merge_fields |沒有定義 |否 |
| interests |string |否 |
| 語言 |string |否 |
| vip |布林值 |否 |
| location |沒有定義 |否 |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| members |array |否 |
| list_id |string |否 |
| total_items |integer |否 |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |是 |
| email_address |string |是 |
| unique_email_id |string |否 |
| email_type |string |否 |
| status |string |否 |
| merge_fields |沒有定義 |是 |
| interests |string |否 |
| stats |沒有定義 |否 |
| ip_signup |string |否 |
| timestamp_signup |string |否 |
| ip_opt |string |否 |
| timestamp_opt |string |否 |
| member_rating |integer |否 |
| last_changed |string |否 |
| 語言 |string |否 |
| vip |布林值 |否 |
| email_client |string |否 |
| location |沒有定義 |否 |
| last_note |沒有定義 |否 |
| list_id |string |否 |
| _links |array |否 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


