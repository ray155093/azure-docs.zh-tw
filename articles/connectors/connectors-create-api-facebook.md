---
title: "在您的 Logic Apps 中新增 Facebook 連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Facebook 連接器的概觀"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: b8a66308c4f4f1df610cdacd092ef133bd605665


---
# <a name="get-started-with-the-facebook-connector"></a>開始使用 Facebook 連接器
連線到 Facebook 並張貼在動態時報上、取得頁面摘要等等。 

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以利用 Facebook 來：

* 根據您從 Facebook 所取得的資料，來建置您的商務流程。 
* 在接收到新貼文時使用觸發程序。
* 使用會張貼到您的動態時報、取得頁面摘要等等的動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當您的動態時報上有新貼文時，您可以取得該貼文，然後把它推送到您的 Twitter 摘要。 

如果要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Facebook 連接器包含下列觸發程序及動作。 

| 觸發程序 | 動作 |
| --- | --- |
| <ul><li>當我的動態時報上有新貼文時</li></ul> |<ul><li>取得我動態時報的摘要</li><li>張貼到我的動態時報上</li><li>當我的動態時報上有新貼文時</li><li>取得頁面摘要</li><li>取得使用者的動態時報</li><li>張貼到頁面上</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。

## <a name="create-a-connection-to-facebook"></a>建立至 Facebook 的連線
當您將這個連接器新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Facebook。

1. 登入您的 Facebook 帳戶。
2. 選取 [授權] ，然後允許您的邏輯應用程式連線並使用您的 Facebook。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> 您可以在其他的邏輯應用程式中，使用這個相同的 Facebook 連線。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參考
適用的版本：1.0。

### <a name="get-feed-from-my-timeline"></a>取得我動態時報的摘要
取得已登入使用者之動態時報的摘要。  
```GET: /me/feed```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| fields |字串 |no |query |無 |指定要傳回的欄位。 例如：識別碼、名稱、圖片。 |
| limit |integer |no |query |無 |要擷取的貼文數目上限 |
| 取代為 |字串 |no |query |無 |將貼文清單限制為只包含擁有位置資訊的貼文。 |
| filter |字串 |no |query |無 |只擷取符合特定串流篩選器的貼文。 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="post-to-my-timeline"></a>張貼到我的動態時報上
將近況訊息張貼到已登入使用者的動態時報上。  
```POST: /me/feed```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| post |字串 |是 |body |無 |要張貼的新訊息 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>當我的動態時報上有新貼文時
當已登入使用者的動態時報上有新貼文時，就會觸發某個新流程。  
```GET: /trigger/me/feed```

沒有參數。 

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="get-page-feed"></a>取得頁面摘要
取得指定頁面摘要中的貼文。  
```GET: /{pageId}/feed```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| pageId |字串 |是 |路徑 |無 |要擷取之貼文所在頁面的識別碼。 |
| limit |integer |no |query |無 |要擷取的貼文數目上限 |
| include_hidden |布林值 |no |query |無 |是否要包含頁面所隱藏的貼文 |
| fields |字串 |no |query |無 |指定要傳回的欄位。 例如：識別碼、名稱、圖片。 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="get-user-timeline"></a>取得使用者的動態時報
取得使用者的動態時報上的貼文。  
```GET: /{userId}/feed```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| userId |字串 |是 |路徑 |無 |要遭到擷取之動態時報擁有者的使用者識別碼。 |
| limit |integer |no |query |無 |要擷取的貼文數目上限 |
| 取代為 |字串 |no |query |無 |將貼文清單限制為只包含擁有位置資訊的貼文。 |
| filter |字串 |no |query |無 |只擷取符合特定串流篩選器的貼文。 |
| fields |字串 |no |query |無 |指定要傳回的欄位。 例如：識別碼、名稱、圖片。 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="post-to-page"></a>張貼到頁面上
以已登入使用者的身分，將訊息張貼在 Facebook 頁面上。  
```POST: /{pageId}/feed```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| pageId |字串 |是 |路徑 |無 |要張貼貼文之頁面的識別碼。 |
| post |多種 |是 |body |無 |要張貼的新訊息。 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
#### <a name="getfeedresponse"></a>GetFeedResponse
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| data |array |no |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| data |array |no |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem：個人檔案摘要中的單一項目
個人檔案可能是使用者、頁面、應用程式或群組。 

| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| admin_creator |array |no |
| caption |字串 |no |
| created_time |string |no |
| 說明 |字串 |no |
| feed_targeting |沒有定義 |no |
| from |沒有定義 |no |
| icon |字串 |no |
| is_hidden |布林值 |no |
| is_published |布林值 |no |
| link |字串 |no |
| 訊息 |字串 |no |
| 名稱 |字串 |no |
| object_id |string |no |
| picture |字串 |no |
| place |沒有定義 |no |
| privacy |沒有定義 |no |
| 屬性 |array |no |
| 來源 |字串 |no |
| status_type |string |no |
| story |字串 |no |
| targeting |沒有定義 |no |
| to |array |no |
| 類型 |字串 |no |
| updated_time |string |no |
| with_tags |沒有定義 |no |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem：個人檔案摘要中的單一項目
個人檔案可能是使用者、頁面、應用程式或群組。

| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| created_time |string |no |
| from |沒有定義 |no |
| 訊息 |字串 |no |
| 類型 |字串 |no |

#### <a name="adminitem"></a>AdminItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| link |字串 |no |

#### <a name="propertyitem"></a>PropertyItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |字串 |no |
| 文字 |字串 |no |
| href |字串 |no |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 訊息 |字串 |yes |
| link |字串 |no |
| picture |字串 |no |
| 名稱 |字串 |no |
| caption |字串 |no |
| 說明 |字串 |no |
| place |字串 |no |
| tags |字串 |no |
| privacy |沒有定義 |no |
| object_attachment |string |no |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 訊息 |字串 |yes |
| link |字串 |no |
| picture |字串 |no |
| 名稱 |字串 |no |
| caption |字串 |no |
| 說明 |字串 |no |
| 動作 |array |no |
| place |字串 |no |
| tags |字串 |no |
| object_attachment |string |no |
| targeting |沒有定義 |no |
| feed_targeting |沒有定義 |no |
| published |布林值 |no |
| scheduled_publish_time |string |no |
| backdated_time |string |no |
| backdated_time_granularity |string |no |
| child_attachments |array |no |
| multi_share_end_card |布林值 |no |

#### <a name="postfeedresponse"></a>PostFeedResponse
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |

#### <a name="profilecollection"></a>ProfileCollection
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| data |array |no |

#### <a name="useritem"></a>UserItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| first_name |string |no |
| last_name |string |no |
| 名稱 |字串 |no |
| gender |字串 |no |
| about |字串 |no |

#### <a name="actionitem"></a>ActionItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |字串 |no |
| link |字串 |no |

#### <a name="targetitem"></a>TargetItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| countries |array |no |
| locales |array |no |
| regions |array |no |
| cities |array |no |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem：控制以此貼文為目標之新聞摘要的物件
這些群組中所有人看到此貼文的機會比較高，其他人看到的機會則比較低。 僅適用於頁面。

| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| countries |array |no |
| regions |array |no |
| cities |array |no |
| age_min |integer |no |
| age_max |integer |no |
| genders |array |no |
| relationship_statuses |array |no |
| interested_in |array |no |
| college_years |array |no |
| interests |array |no |
| relevant_until |integer |no |
| education_statuses |array |no |
| locales |array |no |

#### <a name="placeitem"></a>PlaceItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| 名稱 |字串 |no |
| overall_rating |number |no |
| location |沒有定義 |no |

#### <a name="locationitem"></a>LocationItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| city |字串 |no |
| country |字串 |no |
| 緯度 |number |no |
| located_in |string |no |
| 經度 |number |no |
| 名稱 |字串 |no |
| region |字串 |no |
| state |字串 |no |
| street |字串 |no |
| zip |字串 |no |

#### <a name="privacyitem"></a>PrivacyItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 說明 |字串 |no |
| value |字串 |yes |
| allow |字串 |no |
| deny |字串 |no |
| friends |字串 |no |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| link |字串 |no |
| picture |字串 |no |
| image_hash |string |no |
| 名稱 |字串 |no |
| 說明 |字串 |no |

#### <a name="postphotorequest"></a>PostPhotoRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| url |字串 |yes |
| caption |字串 |no |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |yes |
| post_id |string |yes |

#### <a name="postvideorequest"></a>PostVideoRequest
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| videoData |字串 |yes |
| 說明 |字串 |yes |
| title |字串 |yes |
| uploadedVideoName |字串 |no |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| data |沒有定義 |yes |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| url |字串 |yes |
| is_silhouette |布林值 |yes |
| height |字串 |no |
| width |字串 |no |

#### <a name="geteventresponse"></a>GetEventResponse
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| data |array |yes |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |yes |
| 名稱 |字串 |yes |
| start_time |string |no |
| end_time |string |no |
| timezone |字串 |no |
| location |字串 |no |
| 說明 |字串 |no |
| ticket_uri |string |no |
| rsvp_status |string |是 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。




<!--HONumber=Jan17_HO3-->


