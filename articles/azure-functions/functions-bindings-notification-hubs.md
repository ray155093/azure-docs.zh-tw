---
title: "Azure Functions 通知中樞繫結 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用「Azure 通知中樞」繫結。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: fa3d37b963c1bb6b58127b9180cd657d7b1dabcc
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017


---
# <a name="azure-functions-notification-hub-output-binding"></a>Azure Functions 通知中樞輸出繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中為「Azure 通知中樞」繫結進行設定及撰寫程式碼。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

您的函式可使用已設定的「Azure 通知中樞」搭配幾行程式碼來傳送推播通知。 不過，「Azure 通知中樞」必須針對您要使用的「平台通知服務」(PNS) 做設定。 如需設定 Azure 通知中樞以及開發註冊來接收通知之用戶端應用程式的詳細資訊，請參閱 [開始使用通知中樞](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 並按一下頂端的目標用戶端平台。

您傳送的通知可以是原生通知或範本通知。 原生通知是以輸出繫結的 `platform` 屬性中所設定的特定通知平台為目標。 範本通知可用來以多個平台為目標。   

## <a name="notification-hub-output-binding-properties"></a>通知中樞輸出繫結屬性
function.json 檔案提供下列屬性：

* `name` ︰函式程式碼中用於通知中樞訊息的變數名稱。
* `type`：必須設定為 *"notificationHub"*。
* `tagExpression` ︰標籤運算式可讓您指定將通知傳遞到一組裝置，這些裝置已註冊要接收與標籤運算式相符的通知。  如需詳細資訊，請參閱 [路由與標籤運算式](../notification-hubs/notification-hubs-tags-segment-push-message.md)。
* `hubName` ︰Azure 入口網站中通知中樞資源的名稱。
* `connection`︰此連接字串必須是設定為您通知中樞之 *DefaultFullSharedAccessSignature* 值的「應用程式設定」連接字串。
* `direction`：必須設定為 *"out"*。 
* `platform`：此平台屬性指出作為您通知目標的通知平台。 必須為下列其中一個值：
  * 依照預設，如果輸出繫結中省略平台屬性，範本通知可用來以「Azure 通知中樞」上所設定的任何平台為目標。 如需有關一般使用範本搭配「Azure 通知中樞」來傳送跨平台通知的詳細資訊，請參閱[範本](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。
  * `apns`：Apple Push Notification Service。 如果有關設定適用於 APNS 的通知中樞及在用戶端 App 中接收通知的詳細資訊，請參閱[使用 Azure 通知中樞將推播通知傳送至 iOS](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
  * `adm`：[Amazon 裝置傳訊](https://developer.amazon.com/device-messaging)。 如果有關設定適用於 ADM 的通知中樞及在 Kindle App 中接收通知的詳細資訊，請參閱[開始使用適用於 Kindle 應用程式的通知中樞](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
  * `gcm`：[Google 雲端通訊](https://developers.google.com/cloud-messaging/)。 也支援 Firebase Cloud Messaging (新版 GCM)。 如果有關設定適用於 GCM/FCM 的通知中樞及在 Android 用戶端 App 中接收通知的詳細資訊，請參閱[使用 Azure 通知中樞將推播通知傳送至 Android](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
  * `wns`：以 Windows 平台為目標的 [Windows 推播通知服務](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。 WNS 也支援 Windows Phone 8.1 和更新版本。 如果有關設定適用於 WNS 的通知中樞及在「通用 Windows 平台」(UWP) app 中接收通知的詳細資訊，請參閱[開始使用適用於 Windows 通用平台應用程式的通知中樞](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
  * `mpns`：[Microsoft 推播通知服務](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx)。 此平台支援 Windows Phone 8 和舊版 Windows Phone 平台。 如果有關設定適用於 MPNS 的通知中樞及在 Windows Phone App 中接收通知的詳細資訊，請參閱[在 Windows Phone 上使用 Azure 通知中樞傳送推播通知](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)

function.json 範例：

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>通知中樞連接字串設定
若要使用通知中樞輸出繫結，必須設定中樞的連接字串。 若要這麼做，只要在 [整合] 索引標籤上選取您的通知中樞或建立一個新通知中樞即可。 

您也可以將「DefaultFullSharedAccessSignature」  新增至通知中樞，手動新增現有中樞的連接字串。 此連接字串提供您的函式存取權限來傳送通知訊息。 您可以在 Azure 入口網站中，從通知中樞資源之主要刀鋒視窗中的 [金鑰] 按鈕存取 *DefaultFullSharedAccessSignature* 連接字串值。 若要手動新增中樞的連接字串，請使用下列步驟︰ 

1. 在 Azure 入口網站的 [函數應用程式] 刀鋒視窗上，按一下 [函數應用程式設定] > [前往 App Service 設定]。
2. 在 [設定] 刀鋒視窗中，按一下 [應用程式設定]。
3. 向下捲動至 [應用程式設定] 區段，為通知中樞的 *DefaultFullSharedAccessSignature* 值新增具名項目。
4. 參考輸出繫結中的應用程式設定字串名稱。 與上述範例中使用的 **MyHubConnectionString** 類似。

## <a name="apns-native-notifications-with-c-queue-triggers"></a>含 C# 佇列觸發程序的 APNS 原生通知
這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型來傳送原生 APNS 通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>含 C# 佇列觸發程序的 GCM 原生通知
這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型來傳送原生 GCM 通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>含 C# 佇列觸發程序的 WNS 原生通知
這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型來傳送原生 WNS 快顯通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Node.js 計時器觸發程序的範本範例
這個範例會傳送包含 `location` 和 `message` 的[範本註冊](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>F# 計時器觸發程序的範本範例
這個範例會傳送包含 `location` 和 `message` 的[範本註冊](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>使用 out 參數的範本範例
這個範例會傳送在範本中包含 `message` 預留位置的[範本註冊](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>含非同步函式的範本範例
如果您使用非同步程式碼，則不允許使用 out 參數。 在此情況下，請使用 `IAsyncCollector` 來傳回您的範本通知。 下列程式碼是上述程式碼的非同步範例。 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>使用 JSON 的範本範例
這個範例會使用有效的 JSON 字串來傳送在範本中包含 `message` 預留位置的[範本註冊](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>使用通知中樞程式庫類型的範本範例
這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型。 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


