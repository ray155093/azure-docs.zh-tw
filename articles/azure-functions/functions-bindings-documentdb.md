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
ms.date: 04/18/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: e38c9187be42946df1e8059ba44f10f76d32d984
ms.lasthandoff: 04/21/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Azure Functions DocumentDB 繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中為 Azure DocumentDB 繫結進行設定及撰寫程式碼。 Azure Functions 支援 DocumentDB 的輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

如需有關 DocumentDB 的詳細資訊，請參閱 [DocumentDB 簡介](../documentdb/documentdb-introduction.md)和[建置 DocumentDB 主控台應用程式](../documentdb/documentdb-get-started.md)。

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>DocumentDB 輸入繫結
DocumentDB 輸入繫結會擷取 DocumentDB 文件，並將它傳遞給函式的具名輸入參數。 您可以根據叫用該函式的觸發程序來判斷文件識別碼。 

DocumentDB 輸入繫結在 *function.json* 中具有下列屬性：

- `name`︰函數程式碼中用於文件的識別碼名稱
- `type`︰必須設定為 "documentdb"
- `databaseName`︰包含文件的資料庫
- `collectionName`︰包含文件的集合
- `id`︰要擷取之文件的識別碼。 此屬性支援繫結參數；請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)一文中的[在繫結運算式中繫結到自訂輸入屬性](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression)。
- `sqlQuery`：用來擷取多份文件的 DocumentDB SQL 查詢。 此查詢支援執行階段繫結。 例如：`SELECT * FROM c where c.departmentId = {departmentId}`
- `connection`：包含您 DocumentDB 連接字串的應用程式設定名稱
- `direction`：必須設定為 `"in"`。

不可同時指定 `id` 和 `sqlQuery`。 如果既未設定 `id` 也未設定 `sqlQuery`，則會擷取整個集合。

## <a name="using-a-documentdb-input-binding"></a>使用 DocumentDB 輸入繫結

* 在 C# 和 F# 函數中，當函數順利結束時，會自動傳送透過具名輸入參數對輸入文件所做的任何變更。 
* 在 JavaScript 函數中，不會在函數結束時自動執行更新。 請改用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 來進行更新。 請參閱 [JavaScript 範例](#injavascript)。

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>單一文件的輸入範例
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
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C 中的輸入範例# #

```cs
// Change input document contents using DocumentDB input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F 中的輸入範例# #

```fsharp
(* Change input document contents using DocumentDB input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

此範例需要一個指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 相依性的 `project.json` 檔案︰

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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>以 JavaScript 撰寫的輸入範例

```javascript
// Change input document contents using DocumentDB input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>具有多份文件的輸入範例

假設您想要使用佇列觸發程序來自訂查詢參數，以擷取 SQL 查詢所指定的多份文件。 

在此範例中，佇列觸發程序會提供參數 `departmentId`。`{ "departmentId" : "Finance" }` 的佇列訊息會傳回財務部門的所有記錄。 請在 *function.json* 中使用下列程式碼︰

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}"
    "connection": "DocumentDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>以 C 撰寫之具有多份文件的輸入範例#

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>以 JavaScript 撰寫之具有多份文件的輸入範例

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }        
    context.done();
};
```

## <a id="docdboutput"></a>DocumentDB 輸出繫結
DocumentDB 輸出繫結可讓您撰寫新的文件至 DocumentDB 資料庫。 它在 *function.json* 中具有下列屬性：

- `name`︰函數程式碼中用於新文件的識別碼
- `type`：必須設定為 `"documentdb"`
- `databaseName` ︰包含其中將建立新文件之集合的資料庫。
- `collectionName` ︰其中將建立新文件的集合。
- `createIfNotExists`︰一個布林值，用來指出當集合不存在時是否要建立集合。 預設值為 *false*。 因為新集合會使用保留的輸送量建立，其具有價格含意。 如需詳細資訊，請瀏覽 [定價頁面](https://azure.microsoft.com/pricing/details/documentdb/)。
- `connection`：包含您 DocumentDB 連接字串的應用程式設定名稱
- `direction`：必須設定為 `"out"`

## <a name="using-a-documentdb-output-binding"></a>使用 DocumentDB 輸出繫結
本節說明如何在您的函式程式碼中使用您的 DocumentDB 輸出繫結。

在函式中寫入輸出參數時，依預設會在資料庫中產生新文件，使用自動產生的 GUID 做為文件識別碼。 您可以藉由在輸出參數中指定 `id` JSON 屬性來指定輸出文件的文件識別碼。 

>[!Note]  
>當您指定現有文件的識別碼時，新的輸出文件會覆寫現有文件。 

如果要輸出多個文件，您也可以繫結至 `ICollector<T>` 或 `IAsyncCollector<T>`，`T` 表示其中一種支援的類型。

<a name="outputsample"></a>

## <a name="documentdb-output-binding-sample"></a>DocumentDB 輸出繫結範例
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
* [JavaScript](#outjavascript)

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

此範例需要一個指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 相依性的 `project.json` 檔案︰

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>以 JavaScript 撰寫的輸出範例

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

