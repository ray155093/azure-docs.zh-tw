---
title: 開發人員指南 - 直接方法 | Microsoft Docs
description: Azure IoT 中樞開發人員指南 - 使用直接方法來叫用您裝置上的程式碼
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: nberdy

---
# <a name="invoke-a-direct-method-on-a-device-(preview)"></a>叫用裝置上的直接方法 (預覽)
## <a name="overview"></a>概觀
IoT 中樞能讓您從雲端的裝置上叫用方法。 方法代表與裝置的要求-回覆互動，類似於 HTTP 呼叫，因為會立即成功或失敗 (在使用者指定的逾時之後)，讓使用者知道呼叫的狀態。 這對於依據裝置是否可以回應的不同立即動作的案例相當有用，例如如果裝置離線時傳送 SMS 喚醒至裝置 (SMS 比方法呼叫還貴)。

您可以將方法視為對裝置的直接遠端程序呼叫。 只有已在裝置實作的方法可以從雲端呼叫。 如果雲端嘗試在裝置上叫用方法，而該方法尚未定義，則方法呼叫會失敗。

每個裝置方法的目標是單一裝置。 [作業][lnk-devguide-jobs] 提供方法來叫用多個裝置上的方法，並針對已中斷連接的裝置佇列方法引動過程。

IoT 中樞上具有**服務連線**權限的任何人都可以叫用裝置上的方法。

### <a name="when-to-use"></a>使用時機
裝置方法很類似[雲端到裝置訊息][lnk-devguide-messages]啟用雲端後端以將資訊傳遞至裝置，但是基本的方式不同。 就概念而言，方法是同步但非持續，而雲端到裝置訊息是非同步，具有最多 48 小時的持續性。

遵循要求-回應模式的方法不是持續性。 缺乏持續性在您命令裝置時提供兩個立即的優點︰

* **方法執行的立即回應**表示您不需要管理要求與回覆之間的相互關聯。
* **較高的輸送量**表示操作可以執行得更快，因為 IoT 中樞不會提供任何持續性。 IoT 中樞允許每個單位有比雲端到裝置訊息更多的方法呼叫。

雲端到裝置的訊息不是對裝置的必要命令，而只是顯示雲端服務傳遞某些位元的資訊到裝置，讓它在休閒時揀選，裝置的回應可有可無。 雲端到裝置的訊息有較長逾時時間 (最多 48 小時)，而方法則更快到期。

對於裝置上的立即命令引動過程使用裝置方法，對於裝置上的排程命令引動過程使用作業。

## <a name="method-lifecycle"></a>方法生命週期
方法在裝置上實作，而且可能需要方法承載中的零或多個輸入以正確具現化。 您可以透過面向服務的 URI 叫用直接方法 (`{iot hub}/twins/{device id}/methods/`)。 裝置會透過裝置特定 MQTT 主題收到直接方法 (`$iothub/methods/POST/{method name}/`)。 我們未來可能會在額外的裝置端網路通訊協定上支援方法。

> [!NOTE]
> 當您在裝置上叫用直接方法時，屬性名稱和值只能包含 US-ASCII 可列印英數字元，下列集合中的任何字元除外︰``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``。
> 
> 

方法是同步的，可能在逾時期間後成功或失敗 (預設︰30 秒，可設定為 3600 秒)。 方法在您想要裝置當作在線上並且接收命令 (例如透過手機開燈) 的互動案例中相當有用。 在這些案例中，您想要查看立即成功或失敗，讓雲端服務可以儘速處理結果。 裝置可能會傳回部分訊息本文作為方法的結果，但是不需要方法這麼做。 不保證方法呼叫的順序或任何並行語意。

裝置方法呼叫從雲端側是僅限 HTTP，從裝置側是僅限 MQTT。

## <a name="reference"></a>參考
### <a name="service-facing"></a>服務對應
#### <a name="method-invocation"></a>方法引動過程
裝置上的直接方法引動過程是 HTTP 呼叫，包含︰

* 裝置特定的 *URI* (`{iot hub}/twins/{device id}/methods/`)
* POST *方法*
* *標頭*，其中包含授權、要求識別碼、內容類型及內容編碼
* 透明 JSON *本文*格式如下︰

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  逾時 (秒)。 如果未設定逾時，它會預設為 30 秒。

#### <a name="response"></a>Response
後端收到回應，包含︰

* *HTTP 狀態碼*，用於來自 IoT 中樞的錯誤，包括裝置目前未連接的 404 錯誤
* *標頭*，其中包含 Etag、要求識別碼、內容類型及內容編碼
* JSON *本文*格式如下︰

```
{
    "status" : "OK",
    "payload" : {...}
}
```

   `status` 和 `body` 都是由裝置提供，用來回應裝置本身的狀態碼和/或描述。

### <a name="device-facing"></a>裝置對應
#### <a name="method-invocation"></a>方法引動過程
裝置會接收 MQTT 主題的直接方法要求︰`$iothub/methods/POST/{method name}/?$rid={request id}`

裝置所接收的本文格式如下︰

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

方法要求為 QoS 0。

#### <a name="response"></a>Response
裝置會傳送回應至 `$iothub/methods/res/{status}/?$rid={request id}`，其中︰

* `status` 屬性是方法執行的裝置提供狀態。
* `$rid` 屬性是從 IoT 中樞接收的方法引動過程的要求識別碼。

本文是由裝置設定，而且可以是任何狀態。

### <a name="additional-reference-material"></a>其他參考資料
開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點][lnk-endpoints] 說明每個 IoT 中樞針對執行階段和管理作業所公開的各種端點。
* [節流和配額][lnk-quotas] 說明適用於 IoT 中樞服務的配額，和使用服務時所預期的節流行為。
* [IoT 中樞裝置和服務 SDK][lnk-sdks] 列出各種語言 SDK，您可以在開發裝置與服務應用程式 (與 IoT 中樞互動) 時使用。
* [twins、方法和作業的查詢語言][lnk-query] 說明查詢語言，可用來從 IoT 中樞擷取關於裝置 twins、方法和作業的資訊。
* [IoT 中樞 MQTT 支援][lnk-devguide-mqtt] 針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟
現在您已了解如何使用直接方法，您可能對下列開發人員指南主題感興趣︰

* [排程多個裝置上的作業][lnk-devguide-jobs]

如果您想要嘗試本文章所述的概念，您可能會對下列 IoT 中樞教學課程感興趣：

* [使用雲端到裝置方法][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


