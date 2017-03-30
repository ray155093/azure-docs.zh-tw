---
title: "在 Azure Functions 中使用觸發程序和繫結 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用觸發程序和繫結，將您的程式碼執行連接到線上事件和雲端服務。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a56d71d437814ed08b2e0a05d9acc8448f6b9ae5
ms.lasthandoff: 03/17/2017


---

# <a name="learn-how-to-work-with-triggers-and-bindings-in-azure-functions"></a>了解如何在 Azure Functions 中使用觸發程序和繫結 
本主題說明如何在 Azure Functions 中使用觸發程序和繫結，將您的程式碼連接到各種不同的觸發程序和 Azure 服務，以及其他雲端服務。 它具備一些進階繫結功能及所有繫結類型都支援的語法。  

如需有關使用特定類型的觸發程序或繫結的詳細資訊，請參閱下列其中一個參考主題︰

| | | | |  
| --- | --- | --- | --- |  
| [HTTP/webhook](functions-bindings-http-webhook.md) | [計時器](functions-bindings-timer.md) | [行動應用程式](functions-bindings-mobile-apps.md) | [服務匯流排](functions-bindings-service-bus.md)  |  
| [DocumentDB](functions-bindings-documentdb.md) |  [儲存體 Blob](functions-bindings-storage-blob.md) | [儲存體佇列](functions-bindings-storage-queue.md) |  [儲存體資料表](functions-bindings-storage-table.md) |  
| [事件中樞](functions-bindings-event-hubs.md) | [通知中樞](functions-bindings-notification-hubs.md) | [SendGrid](functions-bindings-sendgrid.md) | [Twilio](functions-bindings-twilio.md) |   
| | | | |  

這些文章假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)及 [C#](functions-reference-csharp.md)、[F#](functions-reference-fsharp.md) 或 [Node.js](functions-reference-node.md) 開發人員參考文章。

## <a name="overview"></a>概觀
觸發程序是用來觸發自訂程式碼的事件回應。 它們可讓您回應整個 Azure 平台或內部部署環境的事件。 繫結代表用來將程式碼連接到所需觸發程序或相關輸入或輸出資料的必要中繼資料。 每個函式的 *function.json* 檔案都包含所有相關的繫結。 函式可以有的輸入和輸出繫結的數量沒有任何限制。 不過，每個函式中僅支援單一觸發程序繫結。  

若要進一步了解可與您 Azure Functions App 整合的各種不同繫結，請參考下表。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

若要進一步了解觸發程序和繫結的一般概念，假設您想要執行一些程式碼以處理放入「Azure 儲存體」佇列中的新項目。 Azure Functions 便可提供「Azure 佇列」觸發程序來支援這麼做。 您將需要下列資訊來監視佇列：

* 佇列所在的儲存體帳戶。
* 佇列名稱。
* 您程式碼會用來參考放入佇列中之新項目的變數名稱。  

佇列觸發程序繫結會包含 Azure 函式的這項資訊。 以下是一個包含佇列觸發程序繫結的範例 *function.json*。 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

您的程式碼可以根據新佇列項目的處理方式來傳送不同類型的輸出。 例如，您可以將一個新記錄寫入「Azure 儲存體」資料表中。  若要這麼做，您可以對 Azure 儲存體資料表建立輸出繫結。 以下是一個範例 *function.json*，其中包含可與佇列觸發程序搭配使用的儲存體資料表輸出繫結。 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

下列 C# 函式會回應被放入佇列中的新項目，並將新的使用者輸入寫入「Azure 儲存體」資料表中。

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

如需有關所支援 Azure 儲存體類型的更多程式碼範例和更具體資訊，請參閱 [Azure 儲存體的 Azure Functions 觸發程序和繫結](functions-bindings-storage.md)。

若要使用 Azure 入口網站中更進階的繫結功能，請按一下您函式 [整合] 索引標籤上的 [進階編輯器] 選項。 進階編輯器可讓您在入口網站中直接編輯 *function.json*。

## <a name="random-guids"></a>隨機 GUID
Azure Functions 提供可使用您的繫結來產生隨機 GUID 的語法。 下列繫結語法會以獨特的名稱，將輸出寫入儲存體容器中的新 BLOB： 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>傳回單一輸出
如果您的函式程式碼會傳回單一輸出，您可以使用名為 `$return` 的輸出繫結，以在您的程式碼中保留更自然的函式簽章。 這只能與支援傳回值的語言 (C#、Node.js、F#) 搭配使用。 此繫結會類似於下列與佇列觸發程序搭配使用的 Blob 輸出繫結。

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

下列 C# 程式碼會以不在函式簽章中使用 `out` 參數的更自然方式傳回輸出。

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

非同步範例︰

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


以下使用 Node.js 示範這個相同的方法：

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

以下是 F# 範例：

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

這也可以透過以 `$return` 指定單一輸出，來與多個輸出參數搭配使用。

## <a name="resolving-app-settings"></a>解析應用程式設定
使用應用程式設定隨著執行階段環境儲存敏感資訊是最佳作法。 透過讓敏感資訊遠離您的應用程式組態檔，便可在使用公開儲存機制來儲存應用程式檔案時將暴光機會限制在最低。  

當應用程式設定名稱含括在百分比符號 `%your app setting%` 時，Azure Functions 執行階段可以將應用程式設定解析為值。 下列 [Twilio 繫結](functions-bindings-twilio.md)會針對繫結的 `from` 欄位使用名為 `TWILIO_ACCT_PHONE` 的應用程式設定。 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>參數繫結
您可以不為輸出繫結屬性進行靜態組態設定，而是設定讓設定動態繫結到您觸發程序輸入繫結中的資料。 請思考一個使用「Azure 儲存體」佇列來處理新訂單的情況。 每個新的佇列項目都是至少包含下列屬性的 JSON 字串：

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address",
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

您可能會想要使用您的 Twilio 帳戶來傳送簡訊給客戶，來提供已收到訂單的最新資訊。  您可以將 Twilio 輸出繫結的 `body` 和 `to` 欄位設定成動態繫結到包含在輸出中的 `name` 和 `mobileNumber`，如下。

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

現在，您的函式程式碼只需要將輸出參數初始化即可，如下。 在執行期間，輸出屬性會繫結至所需的輸入資料。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js：

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>執行階段的進階繫結 (命令式繫結)

使用 *function.json* 的標準輸入和輸出繫結模式稱為[*宣告式*](https://en.wikipedia.org/wiki/Declarative_programming)繫結，其中，繫結是由 JSON 宣告所定義。 不過，您可以使用[命令式](https://en.wikipedia.org/wiki/Imperative_programming)繫結。 利用此模式，您可以快速在您的程式碼中繫結至任何數量的支援輸入及輸出繫結。
當繫結路徑或其他輸入需要在執行階段函式中不是設計階段中進行的情況下，您可能需要命令式繫結。 

定義命令式繫結，如下所示︰

- **請勿**在 *function.json* 中為您所需的命令式繫結併入項目。
- 傳入輸入參數 [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) 或 [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)。 
- 使用下列 C# 模式來執行資料繫結。

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

其中 `BindingTypeAttribute` 是可定義繫結的.NET 屬性，而 `T` 是該繫結類型支援的輸入或輸出類型。 `T` 也不能是 `out` 參數類型 (例如 `out JObject`)。 例如，Mobile Apps 資料表輸出繫結支援[六個輸出類型](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)，但您只可以對 `T` 使用 [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 或 [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)。
    
下列範例程式碼會使用在執行階段定義的 blob 路徑來建立[儲存體 blob 輸出繫結](functions-bindings-storage-blob.md#storage-blob-output-binding)，然後將字串寫入 blob。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) 會定義[儲存體 blob](functions-bindings-storage-blob.md) 輸入或輸出繫結，而 [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) 是支援的輸出繫結類型。
此程式碼會依原樣取得儲存體帳戶連接字串的預設應用程式設定 (也就是 `AzureWebJobsStorage`)。 您可以指定要使用的自訂應用程式設定，方法是新增 [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) 並將屬性陣列傳遞至 `BindAsync<T>()`。 例如，

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

下表顯示要對每個繫結類型使用的對應 .NET 屬性，以及要參考的封裝。

> [!div class="mx-codeBreakAll"]
| 繫結 | 屬性 | 新增參考 |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| 事件中樞 | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| 行動應用程式 | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| 通知中樞 | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| 服務匯流排 | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| 儲存體佇列 | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| 儲存體 Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| 儲存體資料表 | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [測試函數](functions-test-a-function.md)
* [調整函數](functions-scale.md)


