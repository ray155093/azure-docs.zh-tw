---
title: "在您的 Logic Apps 中新增 Yammer 連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Yammer 連接器的概觀"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 104edc61f964f5e0b6c2144a39201283b71145a2


---
# <a name="get-started-with-the-yammer-connector"></a>開始使用 Yammer 連接器
連線到 Yammer 來存取您企業網路中的交談。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以利用 Yammer 來：

* 根據您從 Yammer 所取得的資料，來建置您的商務流程。 
* 在群組或您追蹤的摘要中有新訊息時使用觸發程序。
* 使用動作來張貼訊息、取得所有訊息等等。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當新訊息出現時，您可以利用 Office 365 來傳送電子郵件。

如要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Yammer 包含下列觸發程序及動作。 

| 觸發程序 | 動作 |
| --- | --- |
| <ul><li>群組中有新訊息時</li><li>我的追蹤摘要中有新訊息時</li></ul> |<ul><li>取得所有訊息</li><li>取得群組中的訊息</li><li>從 [追蹤] 摘要取得訊息</li><li>張貼訊息</li><li>群組中有新訊息時</li><li>我的追蹤摘要中有新訊息時</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。 

## <a name="create-a-connection-to-yammer"></a>建立至 Yammer 的連線
若要使用 Yammer 連接器，您必須先建立**連接**，然後提供下列屬性的詳細資料： 

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 Yammer 的認證 |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 
> [!TIP]
> 您可以在其他邏輯應用程式中使用這個連接。
> 
> 

## <a name="yammer-rest-api-reference"></a>Yammer REST API 參考
本文件適用的版本：1.0

### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>取得已登入使用者的 Yammer 網路中的所有公開訊息
對應到 Yammer Web 介面中的「所有」交談。  
```GET: /messages.json```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| older_then |integer |no |query |無 |傳回比指定為數值字串的訊息識別碼還要舊的訊息。 這在您將訊息編頁時會很有用。 舉例來說，如果您正在檢視 20 個訊息，其中最舊的訊息為 2912 號，則您可以讓要求附加「?older_than=2912」，以便取得比您正在檢視的訊息更早的 20 個訊息。 |
| newer_then |integer |no |query |無 |傳回比指定為數值字串的訊息識別碼還要新的訊息。 當您輪詢新訊息時，就必須使用這個參數。 如果您正在查看訊息，且傳回的最新訊息是 3516 號，則您可以傳送參數為「? newer_than = 3516」的要求，以確保您不會收到已出現在您頁面上的相同訊息。 |
| limit |integer |no |query |無 |只傳回指定數目的訊息。 |
| page |integer |no |query |無 |取得指定的頁面。 如果傳回的資料超出限制，您可以使用這個欄位來存取後續的頁面 |

### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 408 |要求逾時 |
| 429 |太多要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 503 |Yammer 服務無法使用 |
| 504 |閘道逾時 |
| 預設值 |作業失敗。 |

### <a name="post-a-message-to-a-group-or-all-company-feed"></a>將訊息張貼到群組中或全公司摘要上
如果有提供群組識別碼，訊息將會張貼到指定群組中，否則將會張貼到全公司摘要上。    
```POST: /messages.json``` 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| input | |是 |body |無 |張貼訊息的要求 |

### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 201 |建立時間 |

## <a name="object-definitions"></a>物件定義
#### <a name="message-yammer-message"></a>Message：Yammer 訊息
| 名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |integer |no |
| content_excerpt |string |no |
| sender_id |integer |no |
| replied_to_id |integer |no |
| created_at |string |no |
| network_id |integer |no |
| message_type |string |no |
| sender_type |string |no |
| url |字串 |no |
| web_url |string |no |
| group_id |integer |no |
| body |沒有定義 |no |
| thread_id |integer |no |
| direct_message |布林值 |no |
| client_type |string |no |
| client_url |string |no |
| 語言 |字串 |no |
| notified_user_ids |array |no |
| privacy |字串 |no |
| liked_by |沒有定義 |no |
| system_message |布林值 |no |

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest：代表要張貼到 Yammer 上的 Yammer 連接器張貼要求
| 名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| body |字串 |yes |
| group_id |integer |no |
| replied_to_id |integer |no |
| direct_to_id |integer |no |
| broadcast |布林值 |no |
| topic1 |字串 |no |
| topic2 |字串 |no |
| topic3 |字串 |no |
| topic4 |字串 |no |
| topic5 |字串 |no |
| topic6 |字串 |no |
| topic7 |字串 |no |
| topic8 |字串 |no |
| topic9 |字串 |no |
| topic10 |字串 |no |
| topic11 |字串 |no |
| topic12 |字串 |no |
| topic13 |字串 |no |
| topic14 |字串 |no |
| topic15 |字串 |no |
| topic16 |字串 |no |
| topic17 |字串 |no |
| topic18 |字串 |no |
| topic19 |字串 |no |
| topic20 |字串 |no |

#### <a name="messagelist-list-of-messages"></a>MessageList：訊息的清單
| 名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| messages |array |no |

#### <a name="messagebody-message-body"></a>MessageBody：訊息內文
| 名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| parsed |字串 |no |
| plain |字串 |no |
| rich |字串 |no |

#### <a name="likedby-liked-by"></a>LikedBy：設為喜歡的人
| 名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 計數 |integer |no |
| names |array |no |

#### <a name="yammmerentity-liked-by"></a>YammmerEntity：設為喜歡的人
| 名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 類型 |字串 |no |
| id |integer |no |
| full_name |string |no |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png



<!--HONumber=Dec16_HO2-->


