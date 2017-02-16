---
title: "了解在您的邏輯應用程式中使用 Azure 服務匯流排連接器 | Microsoft Docs"
description: "使用 Azure App Service 建立邏輯應用程式。 連線到 Azure 服務匯流排來傳送及接收訊息。 您可以執行動作，例如傳送至佇列、傳送至主題、從佇列接收和從訂用帳戶接收。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 1e23402cbc63aeb262bfb471745589cc0bbd734f


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>開始使用 Azure 服務匯流排連接器
連線到 Azure 服務匯流排來傳送及接收訊息。 您可以執行動作，例如傳送至佇列、傳送至主題、從佇列接收和從訂用帳戶接收。

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)來開始。

## <a name="connect-to-service-bus"></a>連接到服務匯流排
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>使用服務匯流排觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>使用服務匯流排動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="technical-details"></a>技術詳細資訊
以下是有關這個連接支援的觸發程序、動作和回應的詳細資料。

### <a name="service-bus-triggers"></a>服務匯流排觸發程序
服務匯流排具有下列觸發程序：  

| 觸發程序 | 說明 |
| --- | --- |
| [在佇列中收到訊息時](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) |當在佇列中收到訊息時，此作業就會觸發流程。 |
| [在主題訂用帳戶中收到訊息時](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) |當在主題訂用帳戶中收到訊息時，此作業就會觸發流程。 |

### <a name="service-bus-actions"></a>服務匯流排動作
服務匯流排具有下列動作︰

| 動作 | 說明 |
| --- | --- |
| [傳送訊息](connectors-create-api-servicebus.md#send-message) |這項作業會傳送訊息至佇列或主題。 |

### <a name="action-and-trigger-details"></a>動作和觸發程序的詳細資料
以下是此連接器動作和觸發程序以及其回應的詳細資料。

#### <a name="send-message"></a>傳送訊息
| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| ContentData* |內容 |訊息的內容。 |
| ContentType |內容類型 |訊息內容的內容類型。 |
| 屬性 |屬性 |每個訊息代理程式屬性的索引鍵-值配對。 |
| entityName* |佇列/主題名稱 |佇列或主題的名稱。 |

這些進階參數也可供使用︰

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| MessageId |訊息識別碼 |服務匯流排可用來識別重複訊息的使用者定義值 (如果已啟用)。 |
| To |收件人 |要傳送的地址。 |
| ReplyTo |回覆地址 |要回覆的佇列地址。 |
| ReplyToSessionId |回覆至工作階段識別碼 |要回覆的工作階段識別碼。 |
| Label |標籤 |應用程式特定的標籤。 |
| ScheduledEnqueueTimeUtc |ScheduledEnqueueTimeUtc |訊息將加入佇列的日期和時間 (UTC 格式)。 |
| SessionId |工作階段識別碼 |工作階段的識別碼。 |
| CorrelationId |相互關連識別碼 |相互關聯的識別碼。 |
| TimeToLive |存留時間 |訊息有效的持續時間 (以刻度為單位)。 持續時間從訊息傳送至服務匯流排開始計算。 |

標示 * 代表必要屬性。

#### <a name="when-a-message-is-received-in-a-queue"></a>在佇列中收到訊息時
| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| queueName* |Queue name |佇列的名稱。 |

標示 * 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
ServiceBusMessage︰此物件具有服務匯流排訊息的內容和屬性。

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ContentData |string |訊息的內容。 |
| ContentType |string |訊息內容的內容類型。 |
| Properties |物件 |每個訊息代理程式屬性的索引鍵-值配對。 |
| MessageId |string |服務匯流排可用來識別重複訊息的使用者定義值 (如果已啟用)。 |
| To |string |傳送至地址。 |
| ReplyTo |string |要回覆的佇列地址。 |
| ReplyToSessionId |string |要回覆的工作階段識別碼。 |
| Label |string |應用程式特定的標籤。 |
| ScheduledEnqueueTimeUtc |string |訊息將加入佇列的日期和時間 (UTC 格式)。 |
| SessionId |string |工作階段的識別碼。 |
| CorrelationId |string |相互關聯的識別碼。 |
| TimeToLive |string |訊息有效的持續時間 (以刻度為單位)。 持續時間從訊息傳送至服務匯流排開始計算。 |

#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>在主題訂用帳戶中收到訊息時
| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| topicName* |主題名稱 |主題的名稱。 |
| subscriptionName* |主題訂用帳戶名稱 |主題訂用帳戶的名稱。 |

標示 * 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
ServiceBusMessage︰此物件具有服務匯流排訊息的內容和屬性。

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| ContentData |string |訊息的內容。 |
| ContentType |string |訊息內容的內容類型。 |
| Properties |物件 |每個訊息代理程式屬性的索引鍵-值配對。 |
| MessageId |string |服務匯流排可用來識別重複訊息的使用者定義值 (如果已啟用)。 |
| To |string |傳送至地址。 |
| ReplyTo |string |要回覆的佇列地址。 |
| ReplyToSessionId |string |要回覆的工作階段識別碼。 |
| Label |string |應用程式特定的標籤。 |
| ScheduledEnqueueTimeUtc |string |訊息將加入佇列的日期和時間 (UTC 格式)。 |
| SessionId |string |工作階段的識別碼。 |
| CorrelationId |string |相互關聯的識別碼。 |
| TimeToLive |string |訊息有效的持續時間 (以刻度為單位)。 持續時間從訊息傳送至服務匯流排開始計算。 |

### <a name="http-responses"></a>HTTP 回應
上述動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰

| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤。 |
| 預設值 |作業失敗。 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。




<!--HONumber=Jan17_HO3-->


