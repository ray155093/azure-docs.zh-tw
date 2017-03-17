---
title: "Azure Functions DocumentDB 繫結 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure DocumentDB 繫結。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: e476a80a3846b8c80c35d6803d5518727f008824
ms.lasthandoff: 03/06/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Azure Functions DocumentDB 繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中為 Azure DocumentDB 繫結進行設定及撰寫程式碼。 Azure Functions 支援 DocumentDB 的輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

如需有關 DocumentDB 的詳細資訊，請參閱 [DocumentDB 簡介](../documentdb/documentdb-introduction.md)和[建置 DocumentDB 主控台應用程式](../documentdb/documentdb-get-started.md)。

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>DocumentDB 輸入繫結
DocumentDB 輸入繫結會擷取 DocumentDB 文件，並將它傳遞給函式的具名輸入參數。 您可以根據叫用該函式的觸發程序來判斷文件識別碼。 

函式的 DocumentDB 輸入會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

請注意：

* `id` 支援類似於 `{queueTrigger}` 的繫結，此繫結會使用佇列訊息的字串值做為文件識別碼。
* `connection` 必須是指向 DocumentDB 帳戶端點的應用程式設定名稱 (具有值 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`)。 如果您透過 Functions 入口網站 UI 建立 DocumentDB 帳戶，帳戶建立程序會為您建立應用程式設定。 若要使用現有的 DocumentDB 帳戶，您需要[手動設定設定此應用程式](functions-how-to-use-azure-function-app-settings.md)。 
* 如果找不到指定的文件，對函式的具名輸入參數會設定為 `null`。 

## <a name="input-usage"></a>輸入使用方式
本節說明如何在您的函式程式碼中使用您的 DocumentDB 輸入繫結。

在 C# 和 F# 函式中，當函式成功結束時，會將對輸入文件 (具名輸入參數) 所做的任何變更自動傳送回集合。 在 Node.js 函式中，對輸入繫結中文件的更新不會傳送回集合。 不過，您可以使用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 對輸入文件進行更新。 請在 [Node.js 範例](#innodejs)中查看如何進行。

<a name="inputsample"></a>

## <a name="input-sample"></a>輸入範例
假設您的 function.json `bindings` 陣列中有下列 DocumentDB 輸入繫結︰

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

請參閱使用此輸入繫結來更新文件的文字值的特定語言範例。

* [C#](#incsharp)
* [F#](#infsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C 中的輸入範例# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F 中的輸入範例# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

您需要新增可指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 相依性的 `project.json` 檔案︰

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

若要新增 `project.json` 檔案，請參閱 [F# 封裝管理](functions-reference-fsharp.md#package)。

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js 中的輸入範例

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a id="docdboutput"></a>DocumentDB 輸出繫結
DocumentDB 輸出繫結可讓您撰寫新的文件至 DocumentDB 資料庫。 

輸出繫結會使用 function.json `bindings` 陣列中的下列 JSON 物件︰ 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

請注意：

* 如果不存在，請將 `createIfNotExists` 設定為 `true` 來建立資料庫和集合。 預設值為 `false`。 新集合會使用保留的輸送量建立，其具有價格含意。 如需詳細資訊，請參閱 [DocumentDB 定價](https://azure.microsoft.com/pricing/details/documentdb/)。
* `connection` 必須是指向 DocumentDB 帳戶端點的應用程式設定的名稱 (具有值 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`)。 如果您透過 Functions 入口網站 UI 建立 DocumentDB 帳戶，帳戶建立程序會為您建立新的應用程式設定。 若要使用現有的 DocumentDB 帳戶，您需要[手動設定設定此應用程式]()。 

## <a name="output-usage"></a>輸出使用方式
本節說明如何在您的函式程式碼中使用您的 DocumentDB 輸出繫結。

在函式中寫入輸出參數時，依預設會在資料庫中產生新文件，使用自動產生的 GUID 做為文件識別碼。 您可以藉由在輸出參數中指定 `id` JSON 屬性來指定輸出文件的文件識別碼。 

>[!Note]  
>當您指定現有文件的識別碼時，新的輸出文件會覆寫現有文件。 

您可以使用下列任何類型來寫入輸出︰

* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化。
  如果您宣告自訂輸出類型 (例如 `out FooType paramName`)，Azure Functions 會嘗試將物件序列化為 JSON。 如果函式結束時輸出參數為 Null，則函式的執行階段會建立 Blob 作為 Null 物件。
* 字串 - (`out string paramName`) 適用於文字 Blob 資料。 當函式結束時，如果字串參數非 Null，就只會建立 Blob。

在 C# 函式中，您也可以輸出至下列任何類型︰

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

如果要輸出多個文件，您也可以繫結至 `ICollector<T>` 或 `IAsyncCollector<T>`，`T` 表示其中一種支援的類型。


<a name="outputsample"></a>

## <a name="output-sample"></a>輸出範例
假設您的 function.json `bindings` 陣列中有下列 DocumentDB 輸出繫結︰

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "out"
}
```

而且您有可接收 JSON 佇列的佇列輸入繫結，其格式如下︰

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

而且您想要針對每一個記錄以下列格式建立 DocumentDB 文件︰

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

請參閱使用此輸出繫結將文件新增至您的資料庫的特定語言範例。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C 中的輸出範例# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
{
  log.Info($"C# Queue trigger function processed: {myQueueItem}");

  dynamic employee = JObject.Parse(myQueueItem);

  employeeDocument = new {
    id = employee.name + "-" + employee.employeeId,
    name = employee.name,
    employeeId = employee.employeeId,
    address = employee.address
  };
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F 中的輸出範例# #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

您需要新增可指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 相依性的 `project.json` 檔案︰

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

若要新增 `project.json` 檔案，請參閱 [F# 封裝管理](functions-reference-fsharp.md#package)。

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的輸出範例

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```

