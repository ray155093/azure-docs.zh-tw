---
title: "在您的邏輯應用程式中新增 Twilio 連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Twilio 連接器的概觀"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 6770aa8387fe3d381fd9c566ca0c38c88a4b1e55


---
# <a name="get-started-with-the-twilio-connector"></a>開始使用 Twilio 連接器
連線到 Twilio 來傳送及接收全域 SMS、多媒體及 IP 訊息。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以利用 Twilio 來：

* 根據您從 Twilio 所取得的資料，來建置您的商務流程。 
* 使用會取得訊息、列出訊息等等的動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當您收到新的 Twilio 訊息時，您可以取得此訊息，並在服務匯流排工作流程中使用。 

如要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Twilio 連接器包含下列動作， 但不包含觸發程序。 

| 觸發程序 | 動作 |
| --- | --- |
| None |<ul><li>取得訊息</li><li>列出訊息</li><li>傳送訊息</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。 

## <a name="create-a-connection-to-twilio"></a>建立至 Twilio 的連線
當您將這個連接器新增到邏輯應用程式時，請輸入下列的 Twilio 值：

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 帳戶識別碼 |是 |輸入您的 Twilio 帳戶識別碼 |
| 存取權杖 |是 |輸入您的 Twilio 存取權杖 |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

如果您還沒有權杖，請參閱 [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) 來建立存取權杖。

> [!TIP]
> 您可以在其他的邏輯應用程式中，使用這個相同的 Twilio 連線。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參考
#### <a name="this-documentation-is-for-version-10"></a>本文件適用的版本：1.0
### <a name="get-message"></a>取得訊息
傳回由所提供訊息識別碼指定的單一訊息。  
```GET: /Messages/{MessageId}.json```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| MessageId |字串 |是 |路徑 |None |訊息識別碼 |

### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 404 |找不到訊息 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

### <a name="list-messages"></a>列出訊息
傳回與您帳戶相關聯的訊息清單。  
```GET: /Messages.json```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 收件人 |字串 |no |query |None |收件者的電話號碼 |
| 從 |字串 |no |query |None |寄件者的電話號碼 |
| DateSent |字串 |no |query |None |只顯示在這一天 (GMT 格式) 傳送的郵件 ，指定方式為 YYYY-MM-DD。 例如：DateSent=2009-07-06。 您也可以指定不相等的日期，例如 DateSent<=YYYY-MM-DD 的訊息就是在該日期午夜或午夜之前所傳送的訊息，而 DateSent>=YYYY-MM-DD 則是在該日期的午夜或午夜之後所傳送的訊息。 |
| PageSize |integer |no |query |50 |在每個清單頁面中所傳回的資源數目。 預設值為 50。 |
| Page |integer |no |query |0 |頁碼。 預設值為 0。 |

### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

### <a name="send-message"></a>傳送訊息
將新的訊息傳送到某個行動電話號碼。  
```POST: /Messages.json```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |是 |body |None |要傳送的訊息 |

### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |不正確的要求 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest：傳送訊息作業的要求模型
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 從 |字串 |是 |
| 收件人 |字串 |是 |
| body |字串 |yes |
| media_url |array |no |
| status_callback |string |no |
| messaging_service_sid |string |no |
| application_sid |string |no |
| max_price |string |no |

#### <a name="message-model-for-message"></a>Message：訊息的模型
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| body |字串 |no |
| 從 |字串 |no |
| 收件人 |字串 |no |
| status |字串 |no |
| sid |字串 |no |
| account_sid |string |no |
| api_version |string |no |
| num_segments |string |no |
| num_media |string |no |
| date_created |string |no |
| date_sent |string |no |
| date_updated |string |no |
| direction |字串 |no |
| error_code |string |no |
| error_message |string |no |
| price |字串 |no |
| price_unit |string |no |
| uri |字串 |no |
| subresource_uris |array |no |
| messaging_service_sid |string |no |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList：列出訊息作業的回應模型
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| messages |array |no |
| Page |integer |no |
| page_size |integer |no |
| num_pages |integer |no |
| uri |字串 |no |
| first_page_uri |string |no |
| next_page_uri |string |no |
| total |integer |no |
| previous_page_uri |string |no |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList：列出訊息作業的回應模型
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| incoming_phone_numbers |array |no |
| Page |integer |no |
| page_size |integer |no |
| num_pages |integer |no |
| uri |字串 |no |
| first_page_uri |string |no |
| next_page_uri |string |no |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest：新增來電號碼作業的要求模型
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| PhoneNumber |字串 |是 |
| AreaCode |字串 |no |
| FriendlyName |字串 |no |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber：來電號碼
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| sid |字串 |no |
| account_sid |string |no |
| date_created |string |no |
| date_updated |string |no |
| capabilities |沒有定義 |no |
| status_callback |string |no |
| status_callback_method |string |no |
| api_version |string |no |

#### <a name="capabilities-phone-number-capabilities"></a>Capabilities：電話號碼容量
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| mms |布林值 |no |
| sms |布林值 |no |
| voice |布林值 |no |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers：可用的電話號碼
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| lata |字串 |no |
| 緯度 |字串 |no |
| 經度 |字串 |no |
| postal_code |string |no |
| rate_center |string |no |
| region |字串 |no |
| mms |布林值 |no |
| sms |布林值 |no |
| voice |布林值 |no |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords：使用量記錄類別
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| category |字串 |no |
| usage |字串 |no |
| usage_unit |string |no |
| 說明 |字串 |no |
| price |number |no |
| price_unit |string |no |
| 計數 |字串 |no |
| count_unit |string |no |
| start_date |string |no |
| end_date |string |no |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


