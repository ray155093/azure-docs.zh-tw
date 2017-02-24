---
title: "Azure Functions 儲存體佇列繫結 | Microsoft Docs"
description: "瞭解如何在 Azure Functions 中使用「Azure 儲存體」觸發程序和繫結。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/18/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 770cac8809ab9f3d6261140333ec789ee1390daf
ms.openlocfilehash: bf9bd2a1b5acdf5a4a4f862bef693f8c60c63a33


---
# <a name="azure-functions-storage-queue-bindings"></a>Azure Functions 儲存體佇列繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中為 Azure 儲存體佇列繫結進行設定及撰寫程式碼。 Azure Functions 支援 Azure 儲存體佇列的觸發程序和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>儲存體佇列觸發程序
Azure 儲存體佇列觸發程序可讓您監視儲存體佇列的新訊息，並對它們做出回應。 

函式的儲存體佇列觸發程序會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

`connection` 必須包含儲存體連接字串的應用程式設定名稱。 在 Azure 入口網站中，當您建立儲存體帳戶或選取一個現有的儲存體帳戶時，您可以在 [整合] 索引標籤中設定此應用程式設定。 若要手動建立此應用程式設定，請參閱[管理 App Service 設定](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)。

您可以在 host.json 檔案中提供[其他設定](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)，進一步微調儲存體佇列觸發程序。  

### <a name="handling-poison-queue-messages"></a>處理有害的佇列訊息
當佇列觸發程序函數失敗時，Azure Functions 會針對指定的佇列訊息重試該函數最多五次，包括第一次嘗試。 如果五次嘗試全都失敗，Functions 會將訊息新增至名為 *&lt;originalqueuename>-poison* 的儲存體佇列。 您可以撰寫函數，透過記錄或傳送通知表示需要手動處理，來處理有害佇列中的訊息。 

若要手動處理有害訊息，您可以藉由檢查 `dequeueCount` 來取得訊息受到挑選來處理的次數 (請參閱[佇列觸發程序中繼資料)](#meta)。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>觸發程序使用方式
在 C# 函式中，您使用在您函式簽章中的具名參數 (例如 `<T> <name>`) 繫結至輸入的訊息。
其中 `T` 是您要用來還原序列化資料的資料類型，而 `paramName` 是您在 [觸發程序繫結](#trigger) 中指定的名稱。 在 Node.js 函式中，您使用 `context.bindings.<name>` 存取 blob 的輸入資料。

佇列訊息可以還原序列化為下列任何一種類型︰

* [物件](https://msdn.microsoft.com/library/system.object.aspx) - 用於 JSON 序列化訊息。 當您宣告自訂輸入類型時，執行階段會嘗試將 JSON 物件還原序列化。 
* String
* 位元組陣列
* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) (僅限 C#)

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>佇列觸發程序中繼資料
您可以使用這些變數名稱，在函式中取得佇列中繼資料︰

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (將佇列訊息文字做為字串擷取的另一種方式)

請參閱[觸發程序範例](#triggersample)以了解如何使用佇列中繼資料

<a name="triggersample"></a>

## <a name="trigger-sample"></a>觸發程序範例
假設您有下列 function.json，則會定義儲存體佇列觸發程序︰

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

請參閱可擷取及記錄佇列中繼資料的特定語言範例。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C 中的觸發程序範例# #
```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的觸發程序範例

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>儲存體佇列輸出繫結
Azure 儲存體佇列輸出繫結可讓您在函式中將訊息寫入儲存體佇列。 

函式的儲存體佇列輸出會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

`connection` 必須包含儲存體連接字串的應用程式設定名稱。 在 Azure 入口網站中，當您建立儲存體帳戶或選取一個現有的儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。 若要手動建立此應用程式設定，請參閱[管理 App Service 設定](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)。

<a name="outputusage"></a>

## <a name="output-usage"></a>輸出使用方式
在 C# 函數中，您可以使用像是 `out <T> <name>` 函數簽章中的具名 `out` 參數來寫入佇列訊息。 在這種情控下，`T` 是您要用來序列化訊息的資料類型，而 `paramName` 是您在 [輸出繫結][](#output) 中指定的名稱。 在 Node.js 函式中，您會使用 `context.bindings.<name>` 來存取輸出。

您可以在您的程式碼中使用任何資料類型輸出佇列訊息︰

* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx)：`out MyCustomType paramName`  
用於 JSON 序列化。  當您宣告自訂輸出類型時，執行階段會嘗試將物件序列化為 JSON。 函數結束時，如果輸出參數為 Null，則執行階段會建立佇列訊息作為 Null 物件。
* 字串：`out string paramName`  
用於測試訊息。 當函數結束時，如果字串參數非 Null，執行階段才會建立訊息。
* 位元組陣列：`out byte[]` 

C# 函數支援這些額外的輸出類型︰

* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx)︰`out CloudQueueMessage` 
* `ICollector<T>` 或 `IAsyncCollector<T>`，其中的 `T` 是支援的類型之一。

<a name="outputsample"></a>

## <a name="output-sample"></a>輸出範例
假設您有下列 function.json，則會定義[儲存體佇列觸發程序](functions-bindings-storage-queue.md)、儲存體 blob 輸入和儲存體 blob 輸出︰

儲存體佇列輸出繫結的範例 *function.json*，此繫結會使用手動觸發程序並將輸入寫入佇列訊息︰

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "queue",
      "name": "myQueueItem",
      "queueName": "myqueue",
      "connection": "my_storage_connection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

請參閱可為每個輸入佇列訊息寫入輸出佇列訊息的特定語言範例。

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C 中的輸出範例# #

```cs
public static void Run(string input, out string myQueueItem, TraceWriter log)
{
    myQueueItem = "New message: " + input;
}
```

或者，若要傳送多個訊息，

```cs
public static void Run(string input, ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Message 1: " + input);
    myQueueItem.Add("Message 2: " + "Some other message.");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的輸出範例

```javascript
module.exports = function(context) {
    // Define a new message for the myQueueItem output binding.
    context.bindings.myQueueItem = "new message";
    context.done();
};
```

或者，若要傳送多個訊息，

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>後續步驟

如需一個使用儲存體佇列觸發程序和繫結的函數範例，請參閱 [建立連接至 Azure 服務的 Azure Functions](functions-create-an-azure-connected-function.md)。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Jan17_HO3-->


