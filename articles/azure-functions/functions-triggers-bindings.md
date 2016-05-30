<properties
	pageTitle="Azure Functions 觸發程序和繫結 | Microsoft Azure"
	description="瞭解如何在 Azure Functions 中使用觸發程序和繫結。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Azure Functions 觸發程序和繫結開發人員參考

這篇文章說明如何為 Azure Functions 中的觸發程序及繫結設定並編碼。透過 Azure 入口網站的**整合** UI 來輕鬆管理大部分繫結，但入口網站並未說明每個繫結的所有功能和選項。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)和 [C#](functions-reference-csharp.md) 或[節點](functions-reference-node.md)開發人員參考文件。

## HTTP 和 WebHook 觸發程序和繫結

您可以使用 HTTP 或 WebHook 觸發程序來呼叫函式以回應 HTTP 要求。要求必須包含 API 金鑰，但該金鑰目前只有在 Azure 入口網站 UI 中提供。

函式 URL 是函式應用程式 URL 和函式名稱的組合︰

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

Function.json 檔案會提供 HTTP 要求和回應的專屬內容。

HTTP 要求的屬性︰

- `name`︰要求物件的函式程式碼中使用的變數名稱 (或 Node.js 函式情況下的要求本文)。
- `type`︰必須設為 httpTrigger。
- `direction`：必須設為 in。 
- `webHookType`︰對於 WebHook 觸發程序，有效值為 github、slack 和 genericJson。對於非 WebHook 的 HTTP 觸發程序，會將這個屬性設為空字串。如需有關 Webhook 的詳細資訊，請參閱下列 [WebHook 觸發程序](#webhook-triggers)一節。
- `authLevel`︰不會套用到 WebHook 觸發程序。設定為 [function] 要求 API 金鑰、[anonymous] 以拖放 API 金鑰需求，或 [admin] 要求主要 API 金鑰。如需詳細資訊，請參閱 [API 金鑰](#apikeys)。

HTTP 回應的屬性︰

- `name`︰函式程式碼中用於回應物件的變數名稱。
- `type`：必須設為 http。
- `direction`：必須設為 out。 
 
範例 function.json：

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

### WebHook 觸發程序

WebHook 觸發程序是一種 HTTP 觸發程序，具有下列專為 Webhook 而設計的功能︰

* 對於特定 WebHook 提供者 (目前支援 GitHub 和 Slack)，Functions 執行階段會驗證提供者的簽章。
* 對於 Node.js 函式，Functions 執行階段提供要求主體而非要求物件。C# 函式無需特殊的處理方式，因為您只要指定參數類型即可控制所提供的內容。如果指定 `HttpRequestMessage`，會取得要求物件。如果指定 POCO 類型，Functions 執行階段會嘗試剖析要求主體中的 JSON 物件以填入物件屬性。
* 若要觸發 WebHook 函式，HTTP 要求必須包含 API 金鑰。對於非 WebHook HTTP 觸發程序，這項需求是選擇性的。

如需如何設定 GitHub WebHook 的相關資訊，請參閱 [GitHub 開發人員 - 建立 Webhook](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409)。

### API 金鑰

根據預設，API 金鑰必須包含在 HTTP 要求之中，才能觸發 HTTP 或 WebHook 函式。金鑰可以包含在名為 `code` 的查詢字串變數中，或包含在 `x-functions-key` HTTP 標頭。對於非 WebHook 函式，您可以在 function.json 檔案中將 `authLevel` 屬性設為 [anonymous]，以將 API 金鑰指定為非必要。

您可以在函式應用程式的檔案系統中，於 D:\\home\\data\\Functions\\secrets 中找到 API 金鑰值。主要金鑰和函式金鑰均設定於 host.json 檔案之中，如此範例所示。

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

host.json 的函式鍵可用來觸發任何函式，但不會觸發已停用的函式。主要金鑰可用來觸發任何函式，並且會觸發已停用的函式。您可以將 `authLevel` 屬性設為 [admin]，設定函式來要求主要金鑰。

如果 secrets 資料夾包含與函式同名的 JSON 檔案，也可使用該檔案中的 `key` 屬性來觸發函式，且此金鑰只會使用其參考的函式。例如，名為 `HttpTrigger` 之函式的 API 金鑰應在 secrets 資料夾的 HttpTrigger.json 中加以指定。下列是一個範例：

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] 設定 WebHook 觸發程序時，請不要與 WebHook 提供者共用主要金鑰。使用只適用於處理 WebHook 的函式。主要金鑰可用來觸發任何函式，即使已停用的函式亦然。

### HTTP 觸發程序函式的 C# 程式碼範例 

程式碼範例會尋找 `name` 參數，其位於查詢字串或 HTTP 要求的主體。

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### HTTP 觸發程序函式的 Node.js 程式碼範例 

此程式碼範例會尋找 `name` 參數，其位於查詢字串或 HTTP 要求的主體。

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### GitHub WebHook 函式的 C# 程式碼範例 

此程式碼範例會記錄 GitHub 問題註解。

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### GitHub WebHook 函式的 Node.js 程式碼範例 

此程式碼範例會記錄 GitHub 問題註解。

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## 計時器觸發程序

Function.json 檔案提供排程運算式和參數，指出是否要立即觸發函式。

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

計時器觸發程序會自動處理多個執行個體向外延展︰只會跨所有執行個體執行特定計時器函式的單一執行個體。

### 排程運算式的格式

排程運算式可以是包含 6 個欄位的 [CRON 運算式](http://en.wikipedia.org/wiki/Cron#CRON_expression)：{秒} {分鐘} {小時} {日} {月} {星期幾}。許多在線上找到的 cron 運算式都會省略 {秒} 欄位，因此如果您複製了這類運算式，就必須調整出額外的欄位。

排程運算式格式也可以是 hh: mm:ss，以指定每次觸發函式之間的延遲。

以下是一些排程運算式範例。

若要每隔 5 分鐘觸發一次︰

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

若要立即觸發程序，然後每隔兩小時觸發一次︰

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

若要每隔 15 秒觸發一次︰

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

### 計時器觸發程序 C# 程式碼範例

每次觸發函式時，此 C# 程式碼範例會寫入單一記錄檔。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Azure 儲存體 (佇列、blob、資料表) 觸發程序和繫結

本節包含下列小節：

* [Azure 儲存體佇列觸發程序](#storagequeuetrigger)
* [Azure 儲存體佇列輸出繫結](#storagequeueoutput)
* [Azure 儲存體 blob 觸發程序](#storageblobtrigger)
* [Azure 儲存體 blob 輸入和輸出繫結](#storageblobbindings)
* [Azure 儲存體資料表輸入和輸出繫結](#storagetablesbindings)

### <a id="storagequeuetrigger"></a>Azure 儲存體佇列觸發程序

儲存體佇列觸發程序的「Function.json」檔案會指定下列屬性。

- `name`︰函式程式碼中用於佇列或佇列訊息的變數名稱。 
- `queueName`：要輪詢的佇列名稱。若要了解佇列命名規則，請參閱[命名佇列和元資料](https://msdn.microsoft.com/library/dd179349.aspx)。
- `connection`︰包含儲存體連接字串的應用程式設定名稱。如果您將 `connection` 留空，觸發程序將會使用函式應用程式的預設儲存體連接字串，此預設值是由 AzureWebJobsStorage 應用程式設定所指定。
- `type`︰必須設為「queueTrigger」。
- `direction`：必須設為「in」。 

#### 儲存體佇列觸發程序的「Function.json」範例

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

#### 佇列觸發程序支援的類型

佇列訊息可以還原序列化為下列任何一種類型︰

* 物件 (來自 JSON)
* String
* 位元組陣列 
* `CloudQueueMessage` (C#) 

#### 佇列觸發程序中繼資料

您可以使用這些變數名稱，在函式中取得佇列中繼資料︰

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (將佇列訊息文字做為字串擷取的另一種方式)

這個 C# 程式碼範例會擷取和記錄佇列中繼資料︰

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

#### 處理有害的佇列訊息

內容會導致函數失敗的訊息稱為「有害訊息」。當函數失敗時不會刪除佇列訊息，最後會再度挑選到該訊息，造成重複循環。SDK 可在有限的反覆次數之後自動中斷循環，或者您可以手動中斷循環。

SDK 將會呼叫函數最多 5 次以處理佇列訊息。如果第五次嘗試失敗，訊息便會移到有害佇列中。

有害佇列名為 *{originalqueuename}*-poison。您可以撰寫函數，透過記錄或傳送通知表示需要手動處理，來處理有害佇列中的訊息。

如果您想要手動處理有害訊息，您可以藉由檢查 `dequeueCount` 來取得訊息已被挑選來處理的次數。

### <a id="storagequeueoutput"></a>Azure 儲存體佇列輸出繫結

儲存體佇列輸出繫結的「Function.json」檔案會指定下列屬性。

- `name`︰函式程式碼中用於佇列或佇列訊息的變數名稱。 
- `queueName`：佇列的名稱。若要了解佇列命名規則，請參閱[命名佇列和元資料](https://msdn.microsoft.com/library/dd179349.aspx)。
- `connection`︰包含儲存體連接字串的應用程式設定名稱。如果您將 `connection` 留空，觸發程序將會使用函式應用程式的預設儲存體連接字串，此預設值是由 AzureWebJobsStorage 應用程式設定所指定。
- `type`：必須設為「queue」。
- `direction`：必須設為「out」。 

#### 儲存體佇列輸出繫結的「Function.json」範例

這個範例會使用佇列觸發程序及撰寫佇列訊息。

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
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### 佇列輸出繫結支援的類型

`queue` 繫結可以將下列類型序列化到佇列訊息︰

* 物件 (在 C# 中為 `out T`，當函式結束時，如果參數為 Null，就會使用 Null 物件建立訊息)
* 字串 (在 C# 中為 `out string`，函式結束時，如果參數值非 Null，就會建立佇列訊息)
* 位元組陣列 (在 C# 中為 `out byte[]`，像字串般運作) 
* `out CloudQueueMessage` (C#，像字串般運作) 

在 C# 中，您也可以繫結至 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是其中一個支援的類型。

#### 佇列輸出繫結程式碼範例

這個 C# 程式碼範例將為每個輸入佇列訊息，撰寫單一輸出佇列訊息。

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

這個 C# 程式碼範例使用 `ICollector<T>` (在非同步函式中使用 `IAsyncCollector<T>`) 撰寫多個訊息：

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

### <a id="storageblobtrigger"></a>Azure 儲存體 blob 觸發程序

儲存體 Blob 觸發程序的「Function.json」檔案會指定下列屬性。

- `name`︰函式程式碼中用於 Blob 的變數名稱。 
- `path`：指定要監視的容器並選擇性指定 Blob 名稱模式的路徑。
- `connection`︰包含儲存體連接字串的應用程式設定名稱。如果您將 `connection` 留空，觸發程序將會使用函式應用程式的預設儲存體連接字串，此預設值是由 AzureWebJobsStorage 應用程式設定所指定。
- `type`︰必須設為「blobTrigger」。
- `direction`：必須設為「in」。

#### 儲存體 Blob 觸發程序的「Function.json」範例

此範例會觸發新增至範例工作項目容器的任何 blob。

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

#### Blob 觸發程序支援的類型

在 Node 或 C# 函式中，Blob 可以還原序列化為下列任何類型︰

* 物件 (來自 JSON)
* String

在 C# 函式中，您也可以繫結至下列任何類型︰

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* 透過 [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 還原序列化的其他型別 

#### Blob 觸發程序 C# 程式碼範例

這個 C# 程式碼範例會將每個新增的 Blob 內容記錄到受監視的容器。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### Blob 觸發程序名稱模式

您可以在 `path` 屬性中指定 Blob 名稱模式。例如：

```json
"path": "input/original-{name}",
```

此路徑會在「input」容器中尋找名為「original-Blob1.txt」的 Blob，且函式程式碼中的 `name` 變數值會是 `Blob1`。

另一個範例：

```json
"path": "input/{blobname}.{blobextension}",
```

此路徑也會尋找名為「original-Blob1.txt」的 Blob，而函式程式碼中的 `blobname` 和 `blobextension` 變數值會是「original-Blob1」和「txt」。

您可以使用固定值來為副檔名指定模式，從而限制觸發函式的 blob 類型。如果您將 `path` 設為「samples/{name}.png」，只有「samples」容器中的「.png」Blob 會觸發函式。

如果您需要針對名稱中包含大括號的 Blob 名稱指定名稱模式，請按兩下大括號。例如，如果您想要在 *images* 容器中尋找具備如下名稱的 Blob：

		{20140101}-soundfile.mp3

將其使用於 `path` 屬性︰

		images/{{20140101}}-{name}

在此範例中，`name` 變數值會是「soundfile.mp3」。

#### Blob 回條

Azure Functions 執行階段可確保不會針對一樣新或版本相同的 blob 多次呼叫 blob 觸發程序函式。它的運作方式是藉由維護 *Blob 回條*來判斷指定的 Blob 版本是否已處理過。

Blob 回條儲存於 AzureWebJobsStorage 連接字串所指定之 Azure 儲存體帳戶中名為 *azure-webjobs-hosts* 的容器中。Blob 回條具有下列資訊：

* 已為 Blob 呼叫的函式 ("{function app name}.Functions.{function name}"，例如："functionsf74b96f7.Functions.CopyBlob")
* 容器名稱
* Blob 類型 ("BlockBlob" 或 "PageBlob")
* Blob 名稱
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

如果您想要強制重新處理某個 Blob，可以從 *azure-webjobs-hosts* 容器中手動刪除該 Blob 的 Blob 回條。

#### 處理有害的 blob

當 blob 觸發程序函式失敗時，SDK 會再次加以呼叫，以防失敗是因暫時性錯誤而造成。如果失敗是因為 Blob 的內容所造成，則此函數會在其每次嘗試處理該 Blob 時失敗。根據預設，SDK 最多會針對指定的 Blob 呼叫函數 5 次。如果第五次嘗試失敗，則 SDK 會在名為 *webjobs-blobtrigger-poison* 的佇列中新增一則訊息。

適用於有害 Blob 的佇列訊息是一個 JSON 物件，其中包含下列屬性：

* FunctionId (格式為 {function app name}.Functions.{function name})
* BlobType ("BlockBlob" 或 "PageBlob")
* ContainerName
* BlobName
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

#### 大型容器的 Blob 輪詢

如果觸發程序正在監視的 blob 容器包含超過 10,000 個 blob，Functions 執行階段會掃描記錄檔以監看新增或變更的 blob。此程序的速度很慢；可能直到建立 Blob 之後數分鐘或更久，才會觸發函數。此外，[儲存體記錄檔建立在「竭盡所能」](https://msdn.microsoft.com/library/azure/hh343262.aspx)的基礎上；並不保證會擷取所有事件。在某些情況下可能會遺失記錄檔。如果您的應用程式無法接受大容器 blob 觸發程序的速度和可靠性限制，建議的方法是當您建立 blob 時建立佇列訊息，並使用佇列觸發程序而非 blob 觸發程序來處理 blob。
 
### <a id="storageblobbindings"></a>Azure 儲存體 Blob 輸入和輸出繫結

儲存體 Blob 輸入或輸出繫結的「Function.json」檔案會指定下列屬性。

- `name`︰函式程式碼中用於 Blob 的變數名稱。 
- `path`：指定要從中讀取 Blob 或將 Blob 寫入到的容器並選擇性指定 Blob 名稱模式的路徑。
- `connection`︰包含儲存體連接字串的應用程式設定名稱。如果您將 `connection` 留空，繫結將會使用函式應用程式的預設儲存體連接字串，此預設值是由 AzureWebJobsStorage 應用程式設定所指定。
- `type`：必須設為「blob」。
- `direction`︰設定為「in」或「out」。 

#### 儲存體 Blob 輸入或輸出繫結的「Function.json」範例

這個範例會使用佇列觸發程序來複製 blob：

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

#### Blob 輸入和輸出支援的類型

在 Node.js 或 C# 函式中，`blob` 繫結可將下列類型序列化或還原序列化︰

* 物件 (在 C# 中為 `out T`，適用於輸出 Blob︰當函式結束時，如果參數值為 null，會建立 Blob 作為 null 物件)
* 字串 (在 C# 中為 `out string`，適用於輸出 Blob；當函式傳回時，如果字串參數非 Null，就只會建立 Blob)

在 C# 函式中，您也可以繫結至下列類型︰

* `TextReader` (僅限輸入)
* `TextWriter` (僅限輸出)
* `Stream`
* `CloudBlobStream` (僅限輸出)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### Blob 輸出 C# 程式碼範例

這個 C# 程式碼範例會複製名稱在佇列訊息中接收的 blob。

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a id="storagetablesbindings"></a>Azure 儲存體資料表輸入和輸出繫結

儲存體資料表的「Function.json」會指定下列屬性。

- `name`︰函式程式碼中用於資料表繫結的變數名稱。 
- `tableName`：資料表的名稱。
- `partitionKey` 和 `rowKey`：搭配使用以讀取 C# 或 Node 函式中的單一實體，或在 Node 函式中寫入單一實體。
- `take`：針對 Node 函式中資料表輸入所讀取的資料列數目上限。
- `filter`：適用於 Node 函式中資料表輸入的 OData filter 篩選運算式。
- `connection`︰包含儲存體連接字串的應用程式設定名稱。如果您將 `connection` 留空，繫結將會使用函式應用程式的預設儲存體連接字串，此預設值是由 AzureWebJobsStorage 應用程式設定所指定。
- `type`：必須設為「table」。
- `direction`︰設定為「in」或「out」。 

#### 儲存體資料表輸入和輸出支援的類型

在 Node.js 或 C# 函式中，`table` 繫結可將物件序列化或還原序列化。物件會有 RowKey 和 PartitionKey 屬性。

在 C# 函式中，您也可以繫結至下列類型︰

* `T`，其中 `T` 會實作 `ITableEntity`
* `IQueryable<T>` (僅限輸入)
* `ICollector<T>` (僅限輸出)
* `IAsyncCollector<T>` (僅限輸出)

#### 儲存體資料表繫結案例

資料表繫結支援下列案例：

* 讀取 C# 或 Node 函式中的單一資料列。

	設定 `partitionKey` 和 `rowKey`。`filter` 和 `take` 屬性不適用於此案例。

* 讀取 C# 函式中的多個資料列。

	Functions 執行階段提供繫結至資料表的 `IQueryable<T>` 物件。類型 `T` 必須衍生自 `TableEntity` 或實作 `ITableEntity`。 `partitionKey`、`rowKey`、`filter` 和 `take` 屬性不適用於此案例，您可以使用 `IQueryable` 物件來執行任何所需的篩選。

* 讀取 Node 函式中的多個資料列。

	設定 `filter` 和 `take` 屬性。不設定 `partitionKey` 或 `rowKey`。

* 在 C# 函式中寫入一或多個資料列。

	Functions 執行階段會提供繫結至資料表的 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 指定您想要加入之實體的結構描述。一般而言，類型 `T` 衍生自 `TableEntity` 或實作 `ITableEntity`，但並非必要。`partitionKey`、`rowKey`、`filter` 和 `take` 屬性不適用於此案例。

#### 儲存體資料表範例︰讀取 C# 或 Node 中的單一資料表實體

此「function.json」範例會使用佇列觸發程序來讀取單一資料表資料列，包含硬式編碼的資料分割金鑰值，及佇列訊息中提供的資料列金鑰。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

下列 C# 程式碼範例使用上述「function.json」檔案以讀取單一資料表實體。佇列訊息具有資料列金鑰值，並將資料表實體讀入「run.csx」檔案中定義的類型。類型包括 `PartitionKey` 和 `RowKey` 屬性，且不是衍生自 `TableEntity`。

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

下列 Node 程式碼範例也使用上述「function.json」檔案以讀取單一資料表實體。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### 儲存體資料表範例︰讀取 C 中的多個資料表實體# 

下列「function.json」和 C# 程式碼範例會讀取佇列訊息中指定的資料分割金鑰的實體。

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# 程式碼會將參考加入至 Azure 儲存體 SDK，讓實體類型可衍生自 `TableEntity`。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### 儲存體資料表範例︰在 C 中建立資料表實體# 

下列「function.json」和「run.csx」範例示範如何在 C# 中撰寫資料表實體。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### 儲存體資料表範例︰在 Node 中建立資料表實體

下列「function.json」和「run.csx」範例示範如何在 Node 中撰寫資料表實體。

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
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Azure 服務匯流排的觸發程序和繫結

本節包含下列小節：

* [Azure 服務匯流排︰PeekLock 行為](#sbpeeklock)
* [Azure 服務匯流排︰有害訊息處理](#sbpoison)
* [Azure 服務匯流排︰單一執行緒](#sbsinglethread)
* [Azure 服務匯流排的佇列或主題觸發程序](#sbtrigger)
* [Azure 儲存體匯流排的佇列或主題輸出繫結](#sboutput)

### <a id="sbpeeklock"></a>Azure 服務匯流排︰PeekLock 行為

Functions 執行階段會在 `PeekLock` 模式中收到訊息，並在函數成功完成時，於訊息中呼叫 `Complete`，或是在函數失敗時呼叫 `Abandon`。如果函數執行時間較 `PeekLock` 逾時還長，即會自動更新鎖定。

### <a id="sbpoison"></a>Azure 服務匯流排︰有害訊息處理

服務匯流排會自行處理無法在 Azure Functions 組態或程式碼中控制或設定的有害訊息。

### <a id="sbsinglethread"></a>Azure 服務匯流排︰單一執行緒

Functions 執行階段預設會並行處理多個佇列訊息。若要指示執行階段一次只處理一個佇列或主題訊息，請在「host.json」檔案中將 `serviceBus.maxConcurrrentCalls` 設定為 1。如需「host.json」檔案的相關資訊，請參閱開發人員參考文章中的[資料夾結構](functions-reference.md#folder-structure)和 WebJobs.Script 儲存機制 wiki 中的 [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)。

### <a id="sbtrigger"></a>Azure 服務匯流排的佇列或主題觸發程序

服務匯流排觸發程序的「Function.json」檔案會指定下列屬性。

- `name`︰函式程式碼中用於佇列或主題或是佇列或主題訊息的變數名稱。 
- `queueName`︰(僅適用於佇列觸發程序) 要輪詢的佇列名稱。
- `topicName`︰(僅適用於主題觸發程序) 要輪詢的主題名稱。
- `subscriptionName`︰(僅適用於主題觸發程序) 訂用帳戶名稱。
- `connection`︰包含服務匯流排連接字串的應用程式設定名稱。連接字串必須是用於服務匯流排命名空間，而不限於特定佇列或主題。如果連接字串不具有管理權限，請設定 `accessRights` 屬性。如果您將 `connection` 留空，觸發程序或繫結將會使用函式應用程式的預設服務匯流排連接字串，此預設值是由 AzureWebJobsServiceBus 應用程式設定所指定。
- `accessRights`︰指定連接字串可用的存取權限。預設值為 `manage`。如果您使用的連接字串不提供管理權限，請設定為 `listen`。否則，Functions 執行階段可能會嘗試，但卻無法執行需要管理權限的作業。
- `type`︰必須設為「serviceBusTrigger」。
- `direction`：必須設為「in」。 

服務匯流排佇列訊息可以還原序列化為下列任何一種類型︰

* 物件 (來自 JSON)
* 字串
* 位元組陣列 
* `BrokeredMessage` (C#) 

#### 適用於使用服務匯流排佇列觸發程序的「function.json」範例

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### 處理服務匯流排佇列訊息的 C# 程式碼範例

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### 處理服務匯流排佇列訊息的 Node.js 程式碼範例

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

### <a id="sboutput"></a>Azure 服務匯流排的佇列或主題輸出

服務匯流排輸出繫結的「Function.json」檔案會指定下列屬性。

- `name`︰函式程式碼中用於佇列或佇列訊息的變數名稱。 
- `queueName`︰(僅適用於佇列觸發程序) 要輪詢的佇列名稱。
- `topicName`︰(僅適用於主題觸發程序) 要輪詢的主題名稱。
- `subscriptionName`︰(僅適用於主題觸發程序) 訂用帳戶名稱。
- `connection`︰與服務匯流排觸發程序相同。
- `accessRights`︰指定連接字串可用的存取權限。預設值為 `manage`。如果您使用的連接字串不提供管理權限，請設定為 `send`。否則，Functions 執行階段可能會嘗試，但卻無法執行需要管理權限的作業，例如建立佇列。
- `type`︰必須設為「serviceBus」。
- `direction`：必須設為「out」。 

Azure Functions 可以從下列任何類型建立服務匯流排佇列訊息。

* 物件 (一律會建立 JSON 訊息，當函式結束時，如果此值為 Null，則會使用 Null 物件建立訊息)
* 字串 (當函式結束時，如果此值非 Null，則會建立訊息)
* 位元組陣列 (像字串般運作) 
* `BrokeredMessage` (C#，像字串般運作)

若要在 C# 函式中建立多個訊息，您可以使用 `ICollector<T>` 或 `IAsyncCollector<T>`。當您呼叫 `Add` 方法時，會建立一則訊息。

#### 適用於使用計時器觸發程序來撰寫服務匯流排佇列訊息的「function.json」範例

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### 建立服務匯流排佇列訊息的 C# 程式碼範例

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### 建立服務匯流排佇列訊息的 Node.js 程式碼範例

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Azure DocumentDB 繫結

本節包含下列小節：

* [Azure DocumentDB 輸入繫結](#docdbinput)
* [Azure DocumentDB 輸出繫結](#docdboutput)

### <a id="docdbinput"></a>Azure DocumentDB 輸入繫結

輸入繫結可從 DocumentDB 集合載入文件，並將它直接傳遞至您的繫結。您可以根據叫用該函式的觸發程序來判斷文件識別碼。在 C# 函式中，當函式成功結束時，會將記錄所做的任何變更自動傳回集合。

function.json 檔案會提供與 DocumentDB 輸入繫結使用的下列屬性︰

- `name`︰函式程式碼中用於文件的變數名稱。
- `type`︰必須設為 "documentdb"。
- `databaseName`︰包含文件的資料庫。
- `collectionName`︰包含文件的集合。
- `id`︰要擷取之文件的識別碼。此屬性支援類似於 "{queueTrigger}" 的繫結，會將佇列訊息的字串值做為文件識別碼。
- `connection`︰這個字串必須是針對您的 DocumentDB 帳戶設為端點的應用程式設定。如果您從 [整合] 索引標籤選擇您的帳戶，將為您建立新的應用程式設定，並採用下列形式 (yourAccount\_DOCUMENTDB) 的名稱。如果您需要手動建立應用程式設定，實際的連接字串必須採用下列格式，AccountEndpoint =<Endpoint for your account>;AccountKey =<Your primary access key>;。
- `direction︰必須設為「"in"」。

function.json 範例：
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### C# 佇列觸發程序的 Azure DocumentDB 輸入程式碼範例
 
使用上述的 function.json 範例，DocumentDB 輸入繫結會擷取文件 (包含符合佇列訊息字串的識別碼)，並將它傳遞至 'document' 參數。如果找不到該文件，則 'document' 參數是 null。當函式結束時，文件隨後會以新的文字值更新。
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Node.js 佇列觸發程序的 Azure DocumentDB 輸入程式碼範例
 
使用上述 function.json 範例，DocumentDB 輸入繫結會擷取文件 (包含符合佇列的訊息字串的識別碼)，並將它傳遞給 `documentIn` 繫結屬性。在 Node.js 函式中，更新的文件不會傳回至集合。不過，您可以將輸入繫結直接傳遞至名為 `documentOut` 的 DocumentDB 輸出繫結以支援更新。這個程式碼範例會更新輸入文件的 text 屬性，並將它設定為輸出文件。
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

### <a id="docdboutput"></a>Azure DocumentDB 輸出繫結

您的函式可使用 **Azure DocumentDB 文件**輸出繫結，將 JSON 文件寫入 Azure DocumentDB 資料庫中。如需 Azure DocumentDB 的詳細資訊，請檢閱 [DocumentDB 簡介](../documentdb/documentdb-introduction.md)和[快速入門教學課程](../documentdb/documentdb-get-started.md)。

function.json 檔案會提供與 DocumentDB 輸出繫結使用的下列屬性︰

- `name`︰函式程式碼中用於新文件的變數名稱。
- `type`︰必須設為「"documentdb"」。
- `databaseName`︰包含將建立新文件所在集合的資料庫。
- `collectionName`︰將建立新文件的集合。
- `createIfNotExists`︰這是布林值，指出是否建立不存在的集合。預設值為 *false*。因為新集合會使用保留的輸送量建立，其具有價格含意。如需詳細資訊，請造訪[價格頁面](https://azure.microsoft.com/pricing/details/documentdb/)。
- `connection`︰這個字串必須是針對您的 DocumentDB 帳戶設為端點的**應用程式設定**。如果您從 [整合] 索引標籤選擇您的帳戶，將為您建立新的應用程式設定，並採用下列形式 (`yourAccount_DOCUMENTDB`) 的名稱。如果您需要手動建立應用程式設定，實際的連接字串必須採用下列格式，`AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`。 
- `direction`：必須設為「"out"」。 
 
function.json 範例：

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Node.js 佇列觸發程序的 Azure DocumentDB 輸出程式碼範例

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

輸出文件︰

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### C# 佇列觸發程序的 Azure DocumentDB 輸出程式碼範例


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Azure DocumentDB 輸出程式碼範例設定檔名稱

如果您想要設定函式中的文件名稱，只要設定 `id` 值即可。例如，如果正在將員工的 JSON 內容排入佇列中，如下所示︰

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

您可以在佇列觸發程序函式中使用下列 C# 程式碼︰
	
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

範例輸出︰

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Azure Mobile Apps 繫結

Azure App Service Mobile Apps 可讓您將資料表端點資料公開至行動用戶端。此相同的表格式資料可用於 Azure Functions 的輸入和輸出繫結中。因為它支援動態結構描述，因此 Node.js 後端行動應用程式非常適合用來公開表格式資料以用於您的函式。動態結構描述預設會啟用，而且應該在生產行動應用程式中停用。如需 Node.js 後端中的資料表端點的詳細資訊，請參閱[概觀：資料表作業](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)。在 Mobile Apps 中，Node.js 後端支援在入口網站內瀏覽和編輯資料表。如需詳細資訊，請參閱 Node.js SDK 主題中的[入口網站內編輯](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing)。當您搭配使用 .NET 後端行動應用程式與 Azure Functions 時，您必須將資料模型手動更新為函式所要求的形式。如需 .NET 後端行動應用程式中的表格端點的詳細資訊，請參閱 .NET 後端 SDK 主題中的[做法：定義資料表控制器](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller)。

本節包含下列小節：

* [Azure Mobile Apps 資料表 API 金鑰](#mobiletablesapikey)
* [Azure Mobile Apps 資料表輸入繫結](#mobiletablesinput)
* [Azure Mobile Apps 資料表輸出繫結](#mobiletablesoutput)

### <a id="mobiletablesapikey"></a>使用 API 金鑰來保護 Mobile Apps 資料表端點的存取。

在 Azure Functions 中，行動資料表繫結可讓您指定 API 金鑰，也就是可用來防止您的函式以外的應用程式存取共用的密碼。Mobile Apps 並沒有內建支援的 API 金鑰驗證。不過，您可以遵循 [Azure App Service Mobile Apps 後端實作 API 金鑰](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)的範例，在 Node.js 後端行動應用程式中實作 API 金鑰。您可以同樣的方式將 API 金鑰實作在 [.NET 後端行動應用程式](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)中。

>[AZURE.IMPORTANT] 此 API 金鑰不可與您的行動應用程式用戶端一起散佈，它應該只安全地散佈給服務端用戶端，如 Azure Functions。

### <a id="mobiletablesinput"></a>Azure Mobile Apps 輸入繫結

輸入繫結可從行動資料表端點載入記錄，並將它直接傳遞至繫結。您可以根據叫用該函式的觸發程序來判斷記錄識別碼。在 C# 函式中，當函式成功結束時，會將記錄所做的任何變更自動傳回資料表。

function.json 檔案支援下列屬性，可與 Mobile Apps 輸入繫結搭配使用︰

- `name`︰函式程式碼中用於新記錄的變數名稱。
- `type`︰繫結類型必須設為「mobileTable」。
- `tableName`︰其中將建立新記錄的資料表。
- `id`：要擷取之記錄的識別碼。此屬性支援類似於 `{queueTrigger}` 的繫結，會將佇列訊息的字串值做為記錄識別碼。
- `apiKey`︰字串，為指定行動應用程式的選擇性 API 金鑰之應用程式設定。當您的行動應用程式使用 API 金鑰來限制用戶端存取時，這是必要的選項。
- `connection`︰字串，為指定行動應用程式 URI 的應用程式設定。
- `direction`︰繫結方向，必須設定為「in」。

function.json 範例：

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### C# 佇列觸發程序的 Azure Mobile Apps 程式碼範例

根據上述的 function.json 範例，輸入繫結會從 Mobile Apps 資料表端點擷取識別碼符合佇列訊息字串的記錄，並將它傳遞至「record」參數。找不到記錄時，參數為 null。當函式結束時，記錄隨後會以新的「Text」值更新。

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Node.js 佇列觸發程序的 Azure Mobile Apps 程式碼範例

根據上述的 function.json 範例，輸入繫結會從 Mobile Apps 資料表端點擷取識別碼符合佇列訊息字串的記錄，並將它傳遞至「record」參數。在 Node.js 函式中，更新的記錄不會傳回至資料表。這個程式碼範例會將擷取的記錄寫入記錄檔。

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


### <a id="mobiletablesoutput"></a>Azure Mobile Apps 輸出繫結

您的函式可以使用輸出繫結，將記錄寫入 Mobile Apps 資料表端點。

function.json 檔案支援下列屬性，可與行動資料表輸出繫結搭配使用︰

- `name`︰函式程式碼中用於新記錄的變數名稱。
- `type` ︰必須設為「mobileTable」的繫結類型。
- `tableName`︰其中將建立新記錄的資料表。
- `apiKey`︰字串，為指定行動應用程式的選擇性 API 金鑰之應用程式設定。當您的行動應用程式使用 API 金鑰來限制用戶端存取時，這是必要的選項。
- `connection`︰字串，為指定行動應用程式 URI 的應用程式設定。
- `direction`︰繫結方向，必須設定為「out」。

function.json 範例：

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### C# 佇列觸發程序的 Azure Mobile Apps 程式碼範例

這個 C# 程式碼範例會將新記錄插入 Mobile Apps 資料表端點，並將「Text」屬性插入上述繫結中指定的資料表。

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Node.js 佇列觸發程序的 Azure Mobile Apps 程式碼範例

這個 Node.js 程式碼範例會將新記錄插入 Mobile Apps 資料表端點，並將「text」屬性插入上述繫結中指定的資料表。

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Azure 通知中樞輸出繫結

您的函式可使用設定的 Azure 通知中樞與極少的程式碼行以傳送推播通知。不過，也必須為您要使用的平台通知服務 (PNS) 設定通知中樞。如需有關設定 Azure 通知中樞和開發註冊通知的用戶端應用程式的詳細資訊，請參閱[開始使用通知中樞](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md)並按一下頂端的目標用戶端平台。

function.json 檔案會提供與通知中樞輸出繫結一起使用的下列屬性︰

- `name`︰函式程式碼中用於通知中樞訊息的變數名稱。
- `type`︰必須設為「"notificationHub"」。
- `tagExpression`︰標記運算式可讓您指定將通知傳遞到一組裝置，它們是註冊希望收到符合標記運算式的裝置。如需詳細資訊，請參閱[路由與標記運算式](../notification-hubs/notification-hubs-routing-tag-expressions.md)。
- `hubName`︰Azure 入口網站中的通知中樞資源的名稱。
- `connection`︰此連接字串必須是針對通知中樞設為「DefaultFullSharedAccessSignature」值的**應用程式設定**連接字串。
- `direction`：必須設為「"out"」。 
 
function.json 範例：

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

### Azure 通知中樞連接字串設定

若要使用通知中樞輸出繫結，必須設定中樞的連接字串。您可以簡單選取您的通知中樞或新建一個，在 [整合] 索引標籤上這麼做。

您也可以將「DefaultFullSharedAccessSignature」新增至通知中樞，手動加入現有中樞的連接字串。此連接字串提供您的函式存取權限來傳送通知訊息。您可從 Azure 入口網站的通知中樞資源的主要刀鋒視窗，從 [金鑰] 按鈕存取「DefaultFullSharedAccessSignature」連接字串值。若要手動新增中樞的連接字串，請使用下列步驟︰

1. 在 Azure 入口網站的 [函式應用程式] 刀鋒視窗中，按一下 [函式應用程式設定] > [移至 App Service 設定]。

2. 按一下 [設定] 刀鋒視窗中的 [應用程式設定]。

3. 向下捲動至 [連接字串] 區段，再為通知中樞新增「DefaultFullSharedAccessSignature」值的命名項目。將類型變更為 [自訂]。
4. 參考輸出繫結中的連接字串名稱。類似於上述範例中使用的 **MyHubConnectionString**。

### Node.js 計時器觸發程序的 Azure 通知中樞程式碼範例 

這個範例會傳送包含 `location` 和 `message` 的[範本註冊](../notification-hubs/notification-hubs-templates.md)的通知。

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

### C# 佇列觸發程序的 Azure 通知中樞程式碼範例

這個範例會傳送包含 `message` 的[範本註冊](../notification-hubs/notification-hubs-templates.md)的通知。


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

這個範例會使用有效的 JSON 字串，傳送包含 `message` 的[範本註冊](../notification-hubs/notification-hubs-templates.md)的通知。

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

### 使用通知類型的 Azure 通知中樞佇列觸發程序 C# 程式碼範例

這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中所定義的 `Notification` 類型。若要使用此類型和程式庫，您必須上傳函式應用程式的「project.json」檔案。project.json 檔案是看起來類似下列的 JSON 文字檔︰

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

如需如何上傳 project.json 檔案的詳細資訊，請參閱[上傳 project.json 檔案](http://stackoverflow.com/questions/36411536/how-can-i-use-nuget-packages-in-my-azure-functions)。

程式碼範例：

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

## 後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions C# 開發人員參考](functions-reference-csharp.md)
* [Azure Functions NodeJS 開發人員參考](functions-reference-node.md)

<!---HONumber=AcomDC_0518_2016-->