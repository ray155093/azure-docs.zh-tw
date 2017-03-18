---
title: "了解 Azure IoT 中樞傳訊 | Microsoft Docs"
description: "開發人員指南 - IoT 中樞的裝置到雲端及雲端到裝置傳訊。 其中包括訊息格式和支援之通訊協定的相關資訊。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2fb6f4d8330eb62e01af318277bc0e90aee039e0
ms.openlocfilehash: d3c4d1a91615957764552a985e0dfeba7c10a927
ms.lasthandoff: 03/01/2017


---
# <a name="send-and-receive-messages-with-iot-hub"></a>使用 IoT 中樞傳送及接收訊息
## <a name="overview"></a>概觀
IoT 中樞提供下列與裝置通訊的傳訊基礎︰

* [裝置到雲端][lnk-d2c]︰從裝置到後端應用程式。
* [雲端到裝置][lnk-c2d]︰從後端應用程式 (「服務」或「雲端」)。

IoT 中樞傳訊功能的核心屬性是訊息的可靠性和持久性。 這些屬性可在裝置端上恢復間歇性連線，以及在雲端恢復事件處理的負載尖峰。 IoT 中樞會針對裝置到雲端和雲端到裝置訊息，實作「至少一次」  傳遞保證。

IoT 中樞支援多個[裝置面向通訊協定][lnk-protocols] (例如 MQTT、AMQP 和 HTTP)。 為了支援完美的跨通訊協定互通性，IoT 中樞定義了所有裝置面向通訊協定均可支援的[通用訊息格式][lnk-message-format]。

IoT 中樞會公開內建的[事件中樞相容端點][lnk-compatible-endpoint]，可讓後端應用程式讀取中樞所接收的裝置到雲端訊息。 您也可以將訂用帳戶中的其他服務連結到 IoT 中樞，來在中樞中建立自訂端點。

### <a name="when-to-use"></a>使用時機
使用裝置到雲端訊息可傳送裝置應用程式所傳來的時間序列遙測和警示，使用雲端到裝置訊息則可用來傳送單向通知給裝置應用程式。

如果不確定要使用報告屬性、裝置對雲端訊息或檔案上傳，請參閱[裝置到雲端通訊指引][lnk-d2c-guidance]。
如果不確定要使用所需屬性、直接方法或雲端對裝置訊息，請參閱[雲端對裝置通訊指引][lnk-c2d-guidance]。

如需 IoT 中樞與事件中樞服務的比較，請參閱 [IoT 中樞與事件中樞的比較][lnk-compare]。

## <a name="device-to-cloud-messages"></a>裝置到雲端的訊息
您透過裝置面向端點傳送裝置到雲端訊息 (**/devices/{deviceId}/messages/events**)。 路由規則接著會將訊息路由至您 IoT 中樞上的其中一個服務面向端點。 路由規則會使用流經您中樞的裝置到雲端訊息屬性，決定訊息的路由目標。 根據預設，訊息會路由至內建的服務面向端點 (messages/events)，此端點與[事件中樞][lnk-event-hubs]相容。 因此，您可以使用標準[事件中樞整合和 SDK][lnk-compatible-endpoint] 來接收裝置到雲端的訊息。

IoT 中樞使用串流訊息模式實作裝置到雲端傳訊。 IoT 中樞的裝置到雲端訊息與[事件中樞][lnk-event-hubs]*事件*較為相似 (而不像[服務匯流排][lnk-servicebus]*訊息*) 因為會有大量的事件傳遞給該服務，並可由多個讀取器讀取。

此實作具有下列含意：

* 裝置到雲端訊息與事件中樞事件類似，會長期保留在 IoT 中樞的預設 **messages/events** 端點，最多達七天。
* 如同事件中樞的事件，裝置到雲端的訊息最大可能為 256 KB，而且可分成數個批次以最佳化傳送。 批次最多可達 256 KB。

不過，IoT 中樞裝置到雲端訊息與事件中樞之間還有一些重要差異：

* 如[控制 IoT 中樞的存取權][lnk-devguide-security]一節所述，IoT 中樞允許每一裝置的驗證和存取控制。
* IoT 中樞可讓您建立最多 10 個自訂端點。 訊息會根據您 IoT 中樞上所設定的路由傳遞至端點。
* IoT 中樞允許同時連接數百萬個裝置 (請參閱[配額和節流][lnk-quotas])，而事件中樞則受限於每個命名空間 5000 個 AMQP 連線。
* IoT 中樞不允許使用 **PartitionKey**任意進行資料分割。 裝置到雲端訊息會根據其原始的 **deviceId**進行分割。
* 調整 IoT 中樞的方式與調整事件中樞有些微不同。 如需詳細資訊，請參閱[調整 IoT 中樞][lnk-guidance-scale]。

如需如何使用裝置到雲端傳訊的詳細資訊，請參閱 [Azure IoT SDK][lnk-sdks]。

如需有關如何設定訊息路由的詳細資料，請參閱[路由規則](#routing-rules)。

> [!NOTE]
> 使用 HTTP 傳送裝置到雲端訊息時，屬性名稱和值只能包含 ASCII 英數字元和 ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``。
> 
> 

### <a name="non-telemetry-traffic"></a>非遙測流量
通常除了遙測資料點之外，裝置也會傳送需要從應用程式商務邏輯層進行個別執行與處理的訊息及要求。 例如，必須於後端觸發特定動作的重大警示。 您可以輕鬆地撰寫路由規則，以將這些訊息類型傳送至專屬的處理端點。

如需處理這種訊息最佳方式的詳細資訊，請參閱[教學課程：如何處理 IoT 中樞裝置到雲端訊息][lnk-d2c-tutorial]教學課程。

### <a name="routing-rules"></a>路由規則

IoT 中樞可讓您根據訊息屬性，將訊息路由至 IoT 中樞端點。 路由規則提供您傳送訊息目的地的彈性，而不需要為了處理訊息或撰寫額外程式碼而停止其他服務。 您設定的每個路由規則都具有下列屬性：

* **名稱**。 可識別規則的唯一名稱。
* **來源**。 要據以處理的資料串流來源。 例如裝置遙測。
* **條件**。 針對訊息屬性執行的路由規則查詢運算式，用來判斷它是否符合端點。 如需建構路由條件的詳細資訊，請參閱[參考 - 裝置對應項和作業的查詢語言][lnk-devguide-query-language]。
* **端點**。 IoT 中樞傳送符合條件之訊息的目的地端點名稱。 端點應該與 IoT 中樞位於相同區域，否則您可能需要支付跨區域寫入費用。

單一訊息可能符合多個路由規則的條件，在這種情況下 IoT 中樞會將訊息傳遞至與每個符合的規則相關聯的端點。 IoT 中樞也會自動刪除重複的訊息傳遞，如此若有訊息符合多個規則並且都有相同的目的地，則訊息僅會寫入到該目的地一次。

如需在 IoT 中樞建立自訂端點的詳細資訊，請參閱 [IoT 中樞端點][lnk-devguide-endpoints]。

### <a name="built-in-endpoint-messagesevents"></a>內建端點：messages/events

IoT 中樞會公開下列屬性，讓您控制與事件中樞相容的內建訊息端點 **messages/events**。

* **分割計數**。 在建立時設定此屬性，以定義裝置對雲端事件擷取的[資料分割][lnk-event-hub-partitions]數目。
* **保留時間**。 此屬性可指定 IoT 中樞保留訊息的天數。 預設值是一天，但它可以增加到七天。

IoT 中樞也可讓您管理內建裝置對雲端接收端點上的取用者群組。

根據預設，所有未明確符合訊息路由規則的訊息，均會寫入至內建端點。 如果您停用此後援路由，則會捨棄未明確符合任何訊息路由規則的訊息。

您可以透過 [IoT 中樞資源提供者 REST API][lnk-resource-provider-apis] 以程式設計方式來修改保留時間，或使用 [Azure 入口網站][lnk-management-portal]來修改。

### <a name="anti-spoofing-properties"></a>防詐騙屬性
為了避免裝置到雲端的訊息中出現裝置詐騙，IoT 中樞使用下列屬性在所有訊息上加上戳記：

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

前兩個屬性包含來源裝置的 **deviceId** 和 **generationId** (依據[裝置身分識別屬性][lnk-device-properties])。

**ConnectionAuthMethod** 屬性包含具有下列屬性的 JSON 序列化物件︰

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>雲端到裝置的訊息
您可以透過面向服務的端點 (**/messages/devicebound**) 來傳送雲端到裝置訊息。 裝置透過裝置專用的端點 (**/devices/{deviceId}/messages/devicebound**) 來接收它們。

每個雲端到裝置訊息都是以單一裝置為目標，其中會將 **to** 屬性設定為 **/devices/{deviceId}/messages/devicebound**。

> [!IMPORTANT]
> 每個裝置佇列最多保留 50 則雲端到裝置訊息。 嘗試將更多訊息傳送到相同的裝置會導致錯誤。
> 
> [!NOTE]
> 傳送雲端到裝置訊息時，屬性名稱和值只能包含 ASCII 英數字元和 ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``。
> 
> 

### <a name="message-lifecycle"></a>訊息生命週期
為了保證至少一次訊息傳遞，IoT 中樞會在每個裝置佇列保留雲端到裝置訊息。 裝置必須明確地認可「完成」  ，如此一來，IoT 中樞便能從佇列中移除它們。 這保證連線失敗和裝置故障能夠恢復。

下圖顯示雲端到裝置訊息的生命週期狀態圖。

![雲端到裝置的訊息生命週期][img-lifecycle]

當服務傳送訊息時，它會被視為「已加入佇列」 。 當裝置想要「接收」訊息時，IoT 中樞會「鎖定」該訊息 (將狀態設為**不可見**)，以便讓相同裝置上的其他執行緒開始接收其他訊息。 當裝置執行緒完成處理訊息時，它會藉由「完成」  訊息來通知 IoT 中樞。

裝置也可以︰

* *拒絕*訊息，這會導致 IoT 中樞將它設定為 **寄不出**狀態。 注意︰使用 MQTT 連接的裝置無法拒絕雲端到裝置訊息。
* *放棄*訊息，這會導致 IoT 中樞將訊息放回佇列，並將狀態設定為**已排入佇列**。

執行緒可能無法處理訊息，且不會通知 IoT 中樞。 在此情況下，訊息會在過了 [可見性 (或鎖定) 逾時] 之後，自動從**不可見**狀態轉換回**已加入佇列**狀態。 此逾時的預設值是一分鐘。

訊息可以在**已加入佇列**與**不可見**狀態之間轉換的次數，以 IoT 中樞上**最大傳遞計數**屬性中指定的次數為限。 達到該轉換次數之後，IoT 中樞就會將訊息的狀態設定為 **寄不出**。 同樣地，IoT 中樞也會在訊息的到期時間過後 (請參閱[存留時間][lnk-ttl])，將訊息的狀態設定為**寄不出**。

如需雲端到裝置訊息的教學課程，請參閱[教學課程：如何使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d-tutorial]。 如需有關不同 Azure IoT SDK 如何公開雲端到裝置功能的參考主題，請參閱 [Azure IoT SDK][lnk-sdks]。

> [!NOTE]
> 通常只要遺失訊息不會影響應用程式邏輯，就應該完成雲端到裝置的訊息。 例如，訊息內容已經成功保存於本機儲存體，或者作業已經成功執行。 訊息可能也會攜帶暫時性資訊，遺失該訊息並不會影響應用程式的功能。 有時，對於長時間執行的作業，您可以在將作業描述保存於本機儲存體之後，完成雲端到裝置訊息。 接著，您可以在作業進度的各個階段，利用一或多個裝置到雲端訊息來通知解決方案後端。
> 
> 

### <a name="message-expiration-time-to-live"></a>訊息到期 (存留時間)
每個雲端到裝置的訊息都有到期時間。 此時間可以由服務 (在 **ExpiryTimeUtc** 屬性中) 設定，或由 IoT 中樞使用指定為 IoT 中樞屬性的預設「存留時間」來設定。 請參閱[雲端到裝置的設定選項][lnk-c2d-configuration]。

> [!NOTE]
> 利用訊息到期和避免將訊息傳送至已中斷連線裝置的常見方法是設定較短的存留時間值。 這個做法可達到與維護裝置連線狀態相同的效果，同時更具效率。 當您要求訊息收條時，IoT 中樞會通知您，哪些裝置能夠收到訊息，而哪些裝置不在線上或故障。
> 
> 

### <a name="message-feedback"></a>訊息意見反應
當您傳送雲端到裝置的訊息時，服務可以要求傳遞每則訊息的意見反應 (關於該訊息的最終狀態)。

* 如果您將 **Ack** 屬性設定為 **positive**，則只有在雲端到裝置訊息達到**已完成**狀態時，IoT 中樞才會產生意見反應訊息。
* 如果您將 **Ack** 屬性設定為 **negative**，則只有在雲端到裝置訊息達到**寄不出**狀態時，IoT 中樞才會產生意見反應訊息。
* 如果您將 **Ack** 屬性設定為 **full**，則 IoT 中樞在上述任一情況下都會產生意見反應訊息。

> [!NOTE]
> 如果 **Ack** 是 **full**，而且您沒有收到意見反應訊息，這表示該意見反應訊息已過期。 服務無法得知原始訊息發生了什麼事。 實際上，服務應該確保它可以在回饋到期之前對其進行處理。 最長到期時間是兩天，在發生失敗時，能夠有相當充裕的時間可讓服務啟動並正常執行。
> 
> 

如[端點][lnk-endpoints]中所述，IoT 中樞會透過面向服務的端點 (**/messages/servicebound/feedback**) 利用訊息來傳遞意見反應。 接收意見反應的語意與雲端到裝置訊息的接收語意相同，並且具有相同的[訊息生命週期][lnk-lifecycle]。 可能的話，訊息意見反應會放入單一訊息中，其格式如下：

| 屬性 | 說明 |
| --- | --- |
| EnqueuedTime |指出訊息建立時間的時間戳記。 |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

主體是記錄的 JSON 序列化陣列，而每筆記錄都具有下列屬性︰

| 屬性 | 說明 |
| --- | --- |
| EnqueuedTimeUtc |指出訊息的結果出現時的時間戳記。 例如，完成已裝置或訊息已到期。 |
| OriginalMessageId |**MessageId** 。 |
| StatusCode |必須是整數。 用於 IoT 中樞所產生的回饋訊息中。 <br/> 0 = 成功 <br/> 1 = 訊息過期 <br/> 2 = 超過最大傳遞計數 <br/> &3; = 訊息被拒 |
| 說明 |**StatusCode**的字串值。 |
| deviceId |**DeviceId** 。 |
| DeviceGenerationId |**DeviceGenerationId** 。 |

> [!IMPORTANT]
> 服務必須指定雲端到裝置訊息的 **MessageId** ，才能使其意見反應與原始訊息相互關聯。
> 
> 

下列範例顯示意見反應訊息的本文。

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>雲端到裝置組態選項
每個 IoT 中樞都會針對雲端到裝置傳訊公開下列設定選項。

| 屬性 | 說明 | 範圍和預設值 |
| --- | --- | --- |
| defaultTtlAsIso8601 |雲端到裝置訊息的預設 TTL。 |ISO_8601 間隔高達 2D (最小為 1 分鐘)。 預設值︰1 小時。 |
| maxDeliveryCount |每個裝置佇列的雲端到裝置最大傳遞計數。 |1 到 100。 預設值：10 |
| feedback.ttlAsIso8601 |保留服務繫結的意見反應訊息。 |ISO_8601 間隔高達 2D (最小為 1 分鐘)。 預設值︰1 小時。 |
| feedback.maxDeliveryCount |意見反應佇列的最大傳遞計數。 |1 到 100。 預設值 = 100。 |

如需詳細資訊，請參閱[建立 IoT 中樞][lnk-portal]。

## <a name="read-device-to-cloud-messages"></a>讀取裝置到雲端的訊息
IoT 中樞會公開您後端服務的 **messages/events** 內建端點，以讀取您的中樞收到的裝置到雲端訊息。 此端點與事件中樞相容，可讓您使用事件中樞服務支援的任何機制讀取訊息。

您也可以在 IoT 中樞中建立自訂端點。 IoT 中樞目前支援事件中樞、服務匯流排佇列，及服務匯流排主題做為自訂端點。 如需從這些服務讀取的詳細資訊，請參閱：從[事件中樞][lnk-getstarted-eh]讀取、從[服務匯流排佇列][lnk-getstarted-queue]讀取、從[服務匯流排主題][lnk-getstarted-topic]讀取。

### <a name="reading-from-the-built-in-endpoint"></a>從內建端點讀取

使用[適用於 .NET 的 Azure 服務匯流排 SDK][lnk-servicebus-sdk] 或[事件中樞 - 事件處理器主機][lnk-eventprocessorhost]時，您可以使用任何 IoT 中樞連接字串搭配正確的權限。 然後使用 **messages/events** 做為事件中樞名稱

當您使用未能察覺 IoT 中樞的 SDK (或產品整合) 時，必須從 [Azure 入口網站][lnk-management-portal]的 IoT 中樞設定中，擷取事件中樞相容端點和事件中樞相容名稱︰

1. 在 IoT 中樞刀鋒視窗中，按一下 [端點]。
2. 在 [內建端點] 區段中，按一下 [事件]。 刀鋒視窗包含下列值：[事件中樞相容端點]、[事件中樞相容名稱]、[資料分割]、[保留時間] 和 [取用者群組]。
   
    ![裝置到雲端設定][img-eventhubcompatible]

> [!NOTE]
> IoT 中樞 SDK 需要 IoT 中樞端點名稱，也就是 [端點] 刀鋒視窗中顯示的 **messages/events**。
>
>

> [!NOTE]
> 如果您正在使用的 SDK 需要**主機名稱**或**命名空間**值，請從 [事件中樞相容端點] 中移除配置。 例如，如果您的事件中樞相容端點為 **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**，**主機名稱**會是 **iothub-ns-myiothub-1234.servicebus.windows.net**，而**命名空間**會是 **iothub-ns-myiothub-1234**。
> 
>

然後，您可以使用具有 **ServiceConnect** 權限的任何共用存取原則，連接至指定的事件中樞。

如果您需要使用先前資訊來建置事件中樞連接字串，請使用下列模式：

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

以下是您可以搭配 IoT 中樞公開之事件中樞相容端點使用之 SDK 和整合項目的清單：

* [Java 事件中樞用戶端](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm Spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。 您可以檢視 GitHub 上的 [Spout 原始檔](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 。
* [Apache Spark 整合](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>參考主題：
下列參考主題會提供您關於與 IoT 中樞交換訊息的詳細資訊。

## <a name="message-format"></a>訊息格式
IoT 中樞訊息包含︰

* 一組 *系統屬性*。 IoT 中樞解譯或設定的屬性。 這個集合是預先決定的。
* 一組 *應用程式屬性*。 應用程式可以定義的字串屬性字典，而且不需將訊息本文還原序列化即可加以存取。 IoT 中樞不會修改這些屬性。
* 不透明的二進位主體。

如需不同通訊協定中如何將訊息編碼的詳細資訊，請參閱 [Azure IoT SDKs][lnk-sdks]。

下表列出 IoT 中樞訊息中的系統屬性集合。

| 屬性 | 說明 |
| --- | --- |
| MessageId |使用者可設定的訊息識別碼，用於「要求-回覆」模式。 格式：區分大小寫的字串，最長為 128 個字元，可使用 ASCII 7 位元英數字元和 `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| 序號 |IoT 中樞指派給每則雲端到裝置訊息的數字 (對每個裝置佇列而言都是唯一的)。 |
| 收件人 |[雲端到裝置][lnk-c2d]訊息中指定的目的地。 |
| ExpiryTimeUtc |訊息到期的日期和時間。 |
| EnqueuedTime |IoT 中樞收到訊息的日期和時間。 |
| CorrelationId |回應訊息中的字串屬性，通常包含採用「要求-回覆」模式之要求的 MessageId。 |
| UserId |用來指定訊息來源的識別碼。 當 IoT 中樞產生訊息時，它會設定為 `{iot hub name}`。 |
| Ack |意見反應訊息產生器。 這個屬性是在雲端到裝置訊息中使用，可要求 IoT 中樞因為裝置取用訊息而產生意見反應訊息。 可能的值︰**none** (預設值)︰不會產生任何意見反應訊息；**positive**︰如果訊息已完成，則會收到意見反應訊息；**negative**︰如果訊息未由裝置完成就到期 (或已達到最大傳遞計數) 則會收到意見反應訊息；或者 **full**︰positive 和 negative。 若需詳細資訊，請參閱[訊息意見反應][lnk-feedback]。 |
| ConnectionDeviceId |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 **deviceId** 。 |
| ConnectionDeviceGenerationId |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 **generationId** (依據[裝置身分識別屬性][lnk-device-properties])。 |
| ConnectionAuthMethod |由 IoT 中樞在裝置到雲端訊息上設定的驗證方法。 這個屬性包含用來驗證傳送訊息之裝置的驗證方法的相關資訊。 如需詳細資訊，請參閱[裝置到雲端反詐騙][lnk-antispoofing]。 |

## <a name="message-size"></a>訊息大小

IoT 中樞會以不限通訊協定的方式 (只考慮實際的承載) 測量郵件大小。 大小 (以位元組為單位) 的計算方式為下列項目的總和︰

* 內文大小 (以位元組為單位)，加上
* 訊息系統屬性所有值的大小 (以位元組為單位)，加上
* 所有使用者屬性名稱和值的大小 (以位元組為單位)。

請注意，屬性名稱和值僅限使用 ASCII 字元，因此字串長度會等於大小 (以位元組為單位)。

## <a name="communication-protocols"></a>通訊協定
IoT 中樞可讓裝置使用 [MQTT][lnk-mqtt]、透過 WebSocket 的 MQTT、[AMQP][lnk-amqp]、透過 WebSocket 的 AMQP 以及 HTTP 通訊協定進行裝置端通訊。 下表針對您選擇的通訊協定提供概略的建議︰

| 通訊協定 | 應選擇此通訊協定的情況 |
| --- | --- |
| MQTT <br> 透過 WebSocket 的 MQTT |可在不需要透過相同 TLS 連線來連線到多個裝置 (各有自己的每一裝置認證) 的裝置上使用。 |
| AMQP <br> 透過 WebSocket 的 AMQP |在欄位和雲端閘道上使用，以利用跨裝置進行多工的連線。 |
| HTTP |針對無法支援其他通訊協定的裝置使用。 |

當您選擇裝置端通訊的通訊協定時，考慮下列幾點：

* **雲端到裝置的模式**。 HTTP 沒有有效的方式可實作伺服器發送。 因此在使用 HTTP 時，裝置會向 IoT 中樞輪詢雲端到裝置的訊息。 這對於裝置和 IoT 中樞而言都沒有效率。 在目前的 HTTP 指導方針中，每個裝置應每隔 25 分鐘或更久進行一次訊息輪詢。 另一方面，MQTT 和 AMQP 則支援在收到雲端到裝置訊息時進行伺服器推送。 它們能夠將訊息立即從 IoT 中樞推送到裝置。 如果傳遞延遲是一大考量，最好是使用 MQTT 或 AMQP 通訊協定。 如果是很少連接的裝置，也適用於 HTTP。
* **現場閘道器**。 使用 MQTT 和 HTTP 時，您無法使用相同的 TLS 連線來連線到多個裝置 (各有自己的每一裝置認證)。 因此對於[現場閘道案例][lnk-azure-gateway-guidance]，這些並不是最理想的通訊協定，因為對於每個連線至現場閘道的裝置，這些通訊協定需要一個現場閘道和 IoT 中樞之間的 TLS 連線。
* **低資源裝置**。 MQTT 和 HTTP 程式庫的使用量比 AMQP 程式庫小。 因此，如果裝置的資源有限 (例如 RAM 小於 1 MB)，這些通訊協定可能是唯一可用的通訊協定實作。
* **網路周遊**。 標準 AMQP 通訊協定使用連接埠 5671，MQTT 則會在連接埠 8883 上接聽，這可能會導致對非 HTTP 通訊協定關閉的網路發生問題。 透過 WebSocket 的 MQTT、透過 WebSocket 的 AMQP 和 HTTP 皆可用於此案例中。
* **承載大小**。 MQTT 和 AMQP 是二進位通訊協定，這使得其承載比 HTTP 更精簡。

> [!NOTE]
> 使用 HTTP 時，每個裝置應該每隔 25 分鐘或更久輪詢一次雲端到裝置訊息。 不過，在開發期間，可以接受比每隔 25 分鐘更頻繁的輪詢頻率。
> 
> 

## <a name="port-numbers"></a>連接埠號碼
裝置可以在 Azure 中使用各種通訊協定來與 IoT 中樞通訊。 一般而言，選擇的通訊協定是根據方案的特定需求而定。 下表列出必須要為裝置開啟的輸出連接埠，以使用特定的通訊協定：

| 通訊協定 | 連接埠 |
| --- | --- |
| MQTT |8883 |
| 透過 WebSocket 的 MQTT |443 |
| AMQP |5671 |
| 透過 WebSocket 的 AMQP |443 |
| HTTP |443 |
| LWM2M (裝置管理) |5684 |

當您在 Azure 區域中建立 IoT 中樞後，IoT 中樞將在該 IoT 中樞的存留期間保留相同的 IP 位址。 不過，為了維護服務品質，如果 Microsoft 將 IoT 中樞移至不同的縮放單位，則它會獲派新的 IP 位址。

## <a name="notes-on-mqtt-support"></a>MQTT 支援的注意事項
IoT 中樞會實作 MQTT v3.1.1 通訊協定，但其具有下列限制和特定行為：

* **不支援 QoS 2**。 當裝置應用程式發佈 **QoS 2** 的訊息時，IoT 中樞會關閉網路連接。 當裝置應用程式訂閱具有 **QoS 2** 的主題時，IoT 中樞會在 **SUBACK** 封包中授與最大 QoS 層級 1。
* **保留訊息不會保存**。 如果裝置應用程式發佈 RETAIN 旗標設為 1 的訊息，IoT 中樞會在訊息中新增 **x-opt-retain** 應用程式屬性。 在此情況下，IoT 中樞不會持續保留訊息，而是改為將它傳遞至後端應用程式。

如需詳細資訊，請參閱 [IoT 中樞的 MQTT 支援][lnk-devguide-mqtt]。

最後請務必檢閱 [Azure IoT 通訊協定閘道][lnk-azure-protocol-gateway]，它可讓您部署高效能自訂通訊協定閘道，以便直接與 IoT 中樞互動。 Azure IoT 通訊協定閘道器可讓您自訂裝置通訊協定，以順應要重建的 MQTT 部署或其他自訂通訊協定。 不過，這種方法會要求您執行及操作自訂通訊協定閘道。

## <a name="additional-reference-material"></a>其他參考資料
IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點][lnk-endpoints]說明每個 IoT 中樞公開給執行階段和管理作業的各種端點。
* [節流和配額][lnk-quotas]說明適用於 IoT 中樞服務的配額，和使用服務時所預期的節流行為。
* [Azure IoT 裝置和服務 SDK][lnk-sdks] 列出各種語言 SDK，可供您在開發與「IoT 中樞」互動的裝置和服務應用程式時使用。
* [裝置對應項和作業的 IoT 中樞查詢語言][lnk-query]說明的 IoT 中樞查詢語言可用來從 IoT 中樞擷取有關裝置對應項和作業的資訊。
* [IoT 中樞 MQTT 支援][lnk-devguide-mqtt]針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟
現在您已了解如何使用 IoT 中樞傳送和接收訊息，接下來您可能對下列 IoT 中樞開發人員指南主題感興趣︰

* [從裝置上傳檔案][lnk-devguide-upload]
* [在 IoT 中樞管理裝置身分識別][lnk-devguide-identities]
* [控制 IoT 中樞的存取權][lnk-devguide-security]
* [使用裝置對應項同步處理狀態和組態][lnk-devguide-device-twins]
* [在裝置上叫用直接方法][lnk-devguide-directmethods]
* [排程多個裝置上的作業][lnk-devguide-jobs]

如果您想要嘗試本文章所述的概念，您可能會對下列 IoT 中樞教學課程感興趣：

* [開始使用 Azure IoT 中樞][lnk-getstarted-tutorial]
* [如何使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d-tutorial]
* [如何處理 IoT 中樞裝置到雲端訊息][lnk-d2c-tutorial]

[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md

[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-what-is-event-hubs.md#partitions
