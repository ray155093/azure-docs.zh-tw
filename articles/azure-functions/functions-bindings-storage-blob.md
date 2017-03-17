---
title: "Azure Functions 儲存體 blob 繫結 | Microsoft Docs"
description: "瞭解如何在 Azure Functions 中使用「Azure 儲存體」觸發程序和繫結。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 1c071390fd6cd9bb5889cb225696b7782fe2bd6b
ms.lasthandoff: 03/14/2017


---
# <a name="azure-functions-storage-blob-bindings"></a>Azure Functions 儲存體 blob 繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中為 Azure 儲存體 blob 繫結進行設定及撰寫程式碼。 Azure Functions 支援適用於 Azure 儲存體 blob 的觸發程序、輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> 不支援[僅限 blob 儲存體帳戶](../storage/storage-create-storage-account.md#blob-storage-accounts)。 Azure Functions 需要一般用途的儲存體帳戶才能使用 blob。 
> 
> 

<a name="trigger"></a>

## <a name="storage-blob-trigger"></a>儲存體 Blob 觸發程序
「Azure 儲存體」Blob 觸發程序可讓您監視新增和更新之 Blob 的儲存體容器，並在偵測到變更時執行您的函式程式碼。 

儲存體 blob 觸發程序的函式會使用下列 `bindings` function.json 陣列中的 JSON 物件︰

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection":"<Name of app setting - see below>"
}
```

請注意：

* 如 `path`，請參閱[命名模式](#pattern)以了解如何格式化 blob 名稱模式。
* `connection` 必須包含儲存體連接字串的應用程式設定名稱。 在 Azure 入口網站中，當您建立儲存體帳戶或選取一個現有的儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。 若要手動建立此應用程式設定，請參閱[手動設定此應用程式設定]()。 

在執行取用方案時，如果函式應用程式已進入閒置狀態，則處理新 Blob 時最多會有 10 分鐘的延遲。 如果函式應用程式正在執行，Blob 的處理速度會較快。 為了避免發生此初始延遲，您可以使用一般的 App Service 方案並啟用 [永遠開啟]，或使用其他機制來觸發 Blob 的處理，例如包含 Blob 名稱的佇列訊息。 

另外，如需詳細資訊，請參閱下列其中一個子標題︰

* [名稱模式](#pattern)
* [Blob 回條](#receipts)
* [處理有害的 blob](#poison)

<a name="pattern"></a>

### <a name="name-patterns"></a>名稱模式
您可以在 `path` 屬性中指定 Blob 名稱模式。 例如：

```json
"path": "input/original-{name}",
```

此路徑會在 *input* 容器中尋找名為 *original-Blob1.txt* 的 Blob，且函式程式碼中的 `name` 變數值會是 `Blob1`。

另一個範例：

```json
"path": "input/{blobname}.{blobextension}",
```

此路徑也會尋找名為 *original-Blob1.txt* 的 Blob，而函式程式碼中的 `blobname` 和 `blobextension` 變數值會是 *original-Blob1* 和 *txt*。

您可以使用檔案副檔名的固定值來限制 blob 的類型。 例如：

```json
"path": "samples/{name}.png",
```

在此情況下，只有 *samples* 容器中的 *.png* blob 會觸發函式。

大括號是名稱模式中的特殊字元。 如果要指定名稱中包含大括號的 Blob 名稱，請按兩下大括號。 例如：

```json
"path": "images/{{20140101}}-{name}",
```

此路徑會在 *images* 容器中尋找名為 *{20140101}-soundfile.mp3* 的 blob，而函式程式碼中的 `name` 變數值會是 *soundfile.mp3*。 

<a name"receipts"></a>

### <a name="blob-receipts"></a>Blob 回條
Azure Functions 執行階段可確保不會針對一樣新或版本相同的 blob 多次呼叫 blob 觸發程序函式。 它的運作方式是藉由維護 *Blob 回條* 來判斷指定的 Blob 版本是否已處理過。

Blob 回條儲存於您函數應用程式 (`AzureWebJobsStorage` 應用程式設定所指定) 的 Azure 儲存體帳戶中名為 *azure-webjobs-hosts* 的容器中。 Blob 回條具有下列資訊：

* 已觸發的函式 ("*&lt;function app name>*.Functions.*&lt;function name>*"，例如："functionsf74b96f7.Functions.CopyBlob")
* 容器名稱
* Blob 類型 ("BlockBlob" 或 "PageBlob")
* Blob 名稱
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

要強制重新處理某個 Blob，可以從 *azure-webjobs-hosts* 容器中手動刪除該 Blob 的 Blob 回條。

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>處理有害的 blob
當 blob 觸發程序函式失敗時，Azure Functions 依預設會針對指定的 blob 重試該函式最多 5 次 (包括第一次嘗試)。 如果 5 次嘗試全都失敗，函式會將訊息新增至名為 *webjobs-blobtrigger-有害* 的儲存體佇列。 適用於有害 Blob 的佇列訊息是一個 JSON 物件，其中包含下列屬性：

* FunctionId (格式為 *&lt;function app name>*.Functions.*&lt;function name>*)
* BlobType ("BlockBlob" 或 "PageBlob")
* ContainerName
* BlobName
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>大型容器的 Blob 輪詢
如果繫結所監看的 blob 容器包含超過 10,000 個 blob，Functions 執行階段會掃描記錄檔以監看新增或變更的 blob。 此程序不是即時。 可能直到建立 Blob 之後數分鐘或更久，才會觸發函數。 此外，[會以「最大努力」建立儲存體記錄](https://msdn.microsoft.com/library/azure/hh343262.aspx)。 並不保證會擷取所有的事件。 在某些情況下可能會遺失記錄檔。 如果您的應用程式無法接受大容器 blob 觸發程序的速度和可靠性限制，建議的方法是當您建立 blob 時建立[佇列訊息](../storage/storage-dotnet-how-to-use-queues.md)，並使用[佇列觸發程序](functions-bindings-storage-queue.md)而非 blob 觸發程序來處理 blob。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>觸發程序使用方式
在 C# 函式中，您使用在您函式簽章中的具名參數 (例如 `<T> <name>`) 繫結至輸入的 blob 資料。
其中 `T` 是您要用來還原序列化資料的資料類型，而 `paramName` 是您在 [觸發 JSON][](#trigger) 中指定的名稱。 在 Node.js 函式中，您使用 `context.bindings.<name>` 存取 blob 的輸入資料。

blob 可以還原序列化為下列任何一種類型︰

* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化的 blob 資料。
  如果您宣告自訂輸入類型 (例如 `FooType`)，Azure Functions 會嘗試將 JSON 資料還原序列化為您指定的類型。
* 字串 - 適用於文字 blob 資料。

在 C# 函式中，您也可以繫結至下列任何類型，函式的執行階段會嘗試使用該類型還原序列化 blob 資料︰

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* 透過 [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

## <a name="trigger-sample"></a>觸發程序範例
假設您有下列 function.json，則會定義儲存體 blob 觸發程序︰

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

請參閱會將每個新增的 Blob 內容記錄到受監視容器的語言特定範例。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>C 中的觸發程序使用方式# #

```cs
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

``` 
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Node.js 中的觸發程序使用方式

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

<a name="input"></a>

## <a name="storage-blob-input-binding"></a>儲存體 Blob 輸入繫結
Azure 儲存體 blob 輸入繫結可讓您在函式中從儲存體容器使用 blob。 

儲存體 blob 輸入的函式會使用下列 `bindings` function.json 陣列中的 JSON 物件︰

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "blob",
  "direction": "in"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

請注意：

* `path` 必須包含容器名稱和 blob 名稱。 例如，如果您在函數中有[佇列觸發程序](functions-bindings-storage-queue.md)，您可以使用 `"path": "samples-workitems/{queueTrigger}"` 以與觸發程序訊息中指定之 blob 名稱相符的名稱來指向 `samples-workitems` 容器中的 blob。   
* `connection` 必須包含儲存體連接字串的應用程式設定名稱。 在 Azure 入口網站中，當您建立儲存體帳戶或選取一個現有的儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。 若要手動建立此應用程式設定，請參閱[手動設定此應用程式設定]()。 

<a name="inputusage"></a>

## <a name="input-usage"></a>輸入使用方式
在 C# 函式中，您使用在您函式簽章中的具名參數 (例如 `<T> <name>`) 繫結至輸入的 blob 資料。
其中 `T` 是您要用來還原序列化資料的資料類型，而 `paramName` 是您在 [輸入繫結][](#input) 中指定的名稱。 在 Node.js 函式中，您使用 `context.bindings.<name>` 存取 blob 的輸入資料。

blob 可以還原序列化為下列任何一種類型︰

* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化的 blob 資料。
  如果您宣告自訂輸入類型 (例如 `InputType`)，Azure Functions 會嘗試將 JSON 資料還原序列化為您指定的類型。
* 字串 - 適用於文字 blob 資料。

在 C# 函式中，您也可以繫結至下列任何類型，函式的執行階段會嘗試使用該類型還原序列化 blob 資料︰

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="inputsample"></a>

## <a name="input-sample"></a>輸入範例
假設您有下列 function.json，則會定義[儲存體佇列觸發程序](functions-bindings-storage-queue.md)、儲存體 blob 輸入和儲存體 blob 輸出︰

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

請參閱將輸入 blob 複製到輸出 blob 的語言特定範例。

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="input-usage-in-c"></a>C 中的輸入使用方式# #

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

``` 
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Node.js 中的輸入使用方式

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

<a name="output"></a>

## <a name="storage-blob-output-binding"></a>儲存體 Blob 輸出繫結
Azure 儲存體 blob 輸出繫結可讓您在函式中將 blob 寫入儲存體容器。 

函式的儲存體 blob 輸出會使用下列 `bindings` function.json 陣列中的 JSON 物件︰

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "blob",
  "direction": "out",
  "path": "<Path of input blob - see below>",
  "connection": "<Name of app setting - see below>"
}
```

請注意：

* `path` 必須包含容器名稱和要寫入的 blob 名稱。 例如，如果您在函數中有[佇列觸發程序](functions-bindings-storage-queue.md)，您可以使用 `"path": "samples-workitems/{queueTrigger}"` 以與觸發程序訊息中指定之 blob 名稱相符的名稱來指向 `samples-workitems` 容器中的 blob。   
* `connection` 必須包含儲存體連接字串的應用程式設定名稱。 在 Azure 入口網站中，當您建立儲存體帳戶或選取一個現有的儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。 若要手動建立此應用程式設定，請參閱[手動設定此應用程式設定]()。 

<a name="outputusage"></a>

## <a name="output-usage"></a>輸出使用方式
在 C# 函式中，您使用函式簽章中名為 `out` 的參數 (例如 `out <T> <name>`) 繫結至輸出 blob，其中 `T` 是您想要用來序列化資料的資料類型，而 `paramName` 是您在[輸出繫結](#output)中指定的名稱。 在 Node.js 函式中，您使用 `context.bindings.<name>` 存取輸出 blob。

您可以使用下列任何類型寫入輸出 blob︰

* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化。
  如果您宣告自訂輸出類型 (例如 `out OutputType paramName`)，Azure Functions 會嘗試將物件序列化為 JSON。 如果函式結束時輸出參數為 null，則函式的執行階段會建立 blob 作為 null 物件。
* 字串 - (`out string paramName`) 適用於文字 blob 資料。 當函式結束時，如果字串參數非 Null，就只會建立 Blob。

在 C# 函式中，您也可以輸出至下列任何類型︰

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="outputsample"></a>

## <a name="output-sample"></a>輸出範例
請參閱[輸入範例](#inputsample).

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


