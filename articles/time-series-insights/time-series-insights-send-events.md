---
title: "將事件傳送至 Azure Time Series Insights 環境 | Microsoft Docs"
description: "本教學課程說明如何將事件推送至 Time Series Insights 環境"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: d7c01e18355b66670c9ab7d964f5cdb7ba72bb8f
ms.contentlocale: zh-tw
ms.lasthandoff: 06/16/2017

---
# <a name="send-events-to-a-time-series-insights-environment-via-event-hub"></a>透過事件中樞將事件傳送至 Time Series Insights 環境

本教學課程說明如何建立和設定事件中樞，並執行應用程式範例以推送事件。 如果您現有的事件中樞已具有 JSON 格式的事件，您可以跳過本教學課程，並在[時間序列總管](https://insights.timeseries.azure.com)檢視您的環境。

## <a name="configure-an-event-hub"></a>設定事件中樞
1. 若要建立事件中樞，請遵循事件中樞[文件](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)的指示。

2. 確定您已建立專供 Time Series Insights 事件來源使用的取用者群組。

  > [!IMPORTANT]
  > 確定任何其他服務 (例如串流分析作業或其他 Time Series Insights 環境) 均未使用此取用者群組。 如果有其他服務使用此取用者群組，讀取作業會對此環境和其他服務造成負面影響。 如果您使用 “$Default” 做為取用者群組，有可能會導致其他讀取者重複使用。

  ![選取事件中樞取用者群組](media/send-events/consumer-group.png)

3. 在事件中樞內建立「MySendPolicy」，以用來在下列範例中傳送事件。

  ![選取 [共用存取原則]，然後按一下 [新增] 按鈕](media/send-events/shared-access-policy.png)  

  ![新增共用存取原則](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>建立 Time Series Insights 事件來源
1. 如果您尚未建立事件來源，請遵循[這裡](time-series-insights-add-event-source.md)指定的指示來建立事件來源。

2. 指定「deviceTimestamp」做為時間戳記屬性名稱，這個屬性會做為下列範例中的實際時間戳記。 以 JSON 形式將時間戳記屬性傳送至事件中樞時，該屬性的名稱需區分大小寫，且值的格式應為 __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__。 如果事件中不存在該屬性，則系統會使用事件在排入事件中樞佇列時的時間。

  ![建立事件來源](media/send-events/event-source-1.png)

## <a name="run-sample-code-to-push-events"></a>執行程式碼範例來推送事件
1. 移至事件中樞原則「MySendPolicy」，然後複製連接字串與原則金鑰。

  ![複製 MySendPolicy 連接字串](media/send-events/sample-code-connection-string.png)

2. 執行下列程式碼，讓三個裝置各傳送 600 個事件。 使用連接字串更新 `eventHubConnectionString`。

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>支援的 JSON 樣貌
### <a name="sample-1"></a>範例 1

#### <a name="input"></a>輸入

簡單的 JSON 物件。

```json
{
    "deviceId":"device1",
    "deviceTimestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>輸出 - 1 個事件

|deviceId|deviceTimestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>範例 2

#### <a name="input"></a>輸入
具有兩個 JSON 物件的 JSON 陣列。 每個 JSON 物件都會轉換成事件。
```json
[
    {
        "deviceId":"device1",
        "deviceTimestamp":"2016-01-08T01:08:00Z"
    },
    {
        "deviceId":"device2",
        "deviceTimestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---2-events"></a>輸出 - 2 個事件

|deviceId|deviceTimestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|
### <a name="sample-3"></a>範例 3

#### <a name="input"></a>輸入

具有巢狀 JSON 陣列的 JSON 物件，此陣列中包含兩個 JSON 物件。
```json
{
    "location":"WestUs",
    "events":[
        {
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z"
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---2-events"></a>輸出 - 2 個事件
請注意，系統會將「location」屬性複製到每個事件。

|location|events.deviceId|events.deviceTimestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>範例 4

#### <a name="input"></a>輸入

具有巢狀 JSON 陣列的 JSON 物件，此陣列中包含兩個 JSON 物件。 此輸入示範可由複雜 JSON 物件表示的全域屬性。

```json
{
    "location":"WestUs",
    "manufacturerInfo":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z",
            "deviceData":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z",
            "deviceData":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---2-events"></a>輸出 - 2 個事件

|location|manufacturerInfo.name|manufacturerInfo.location|events.deviceId|events.deviceTimestamp|events.deviceData.type|events.deviceData.units|events.deviceData.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>後續步驟

* 在 [Time Series Insights 入口網站](https://insights.timeseries.azure.com)中檢視您的環境

