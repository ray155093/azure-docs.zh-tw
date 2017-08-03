---
title: "使用 Azure Application Insights .NET SDK 追蹤自訂作業 | Microsoft Docs"
description: "使用 Azure Application Insights .NET SDK 追蹤自訂作業"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/31/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 883750c939aa3bd605189f513e5ce74642f91709
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---

# <a name="tracking-custom-operations-with-application-insights-net-sdk"></a>使用 Application Insights .NET SDK 追蹤自訂作業

Application Insights SDK 自動追蹤相依服務的傳入 HTTP 要求和呼叫 - HTTP 要求、SQL 查詢等等。採用結合多項微服務的應用程式。 追蹤和相互關聯要求與相依性，可讓您深入了解橫跨所有微服務的整體應用程式回應能力和可靠性。 我們即將逐漸擴充這份清單，以及自動收集其他知名的平台和架構。 

有一類應用程式模式無法以一般方式支援。 適當監視這類模式時，需要進行手動程式碼檢測。 本文涵蓋一些可能需要手動檢測的模式。 包括處理或執行長時間執行之背景工作的自訂佇列。

本文提供有關如何使用 ApplicationInsights SDK 追蹤自訂作業的指引。

此文件與下列各項相關：
- 適用於 .NET 的 Application Insights (也稱為 Base SDK) 版本`2.4+`
- 適用於 Web 應用程式 (執行 ASP.NET) 的 Application Insights 版本`2.4+`
- 適用於 AspNetCore 的 Application Insights 版本`2.1+`

## <a name="overview"></a>概觀
藉此作業，我們了解應用程式執行的某些邏輯工作。 它具有名稱、開始時間和持續時間、執行的內容，例如使用者名稱、屬性和結果。 如果作業 `A` 由作業 `B` 所起始 - 作業 `B` 會設定為 `A` 的父代。  作業只能有一個父代和許多子系作業。 您可以在[這裡](application-insights-correlation.md)深入了解作業和遙測相互關聯。

在 Application Insights.NET SDK 中，作業是由抽象類別 [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) 及其子系 [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) 和 [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs) 描述。

## <a name="incoming-operations-tracking"></a>傳入作業追蹤 
Application Insights Wb SDK 會針對在 IIS 管線中執行的 ASP.NET 應用程式和所有的 ASP.NET Core 應用程式，自動收集 HTTP 要求。 其他平台和架構有社群支援的解決方案。 不過，如果任何標準或社群支援的解決方案不支援應用程式，您可以用手動方式進行檢測。

接收佇列中項目的背景工作是另一個需要自訂追蹤的範例。 對於某些佇列，將訊息新增至此佇列的呼叫會作為相依性追蹤。 但是，不會自動收集描述處理訊息的高階作業。

我們來看看要如何追蹤這類作業。

整體言而，此工作是要建立 `RequestTelemetry` 和設定已知的屬性：一旦作業完成，請追蹤遙測。 下列範例提供示範。

### <a name="http-request-in-owin-self-hosted-app"></a>Owin 自我裝載應用程式中的 HTTP 要求
我們會遵循[相互關聯的 Http 通訊協定](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)，而您應會收到此處描述的標頭。

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // if there is Request-Id recevied from the upstream service, set telemetry context accordingly
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows to correlate 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry item
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // process request
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // update status code and success as appropriate
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // now it's time to stop operation (and track telemetry)
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // returns root Id from the '|' to first '.' if any
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

HTTP 相互關聯通訊協定也會宣告 `Correlation-Context` 標頭，但是會為了精簡而省略此標頭。

## <a name="queue-instrumentation"></a>佇列檢測
對於 HTTP 通訊，我們建立了可傳遞相互關聯詳細資料的通訊協定。 有些佇列通訊協定允許您將其他中繼資料隨著訊息一起傳遞，有些則不允許。

### <a name="service-bus-queue"></a>服務匯流排佇列
[服務匯流排佇列](../service-bus-messaging/index.md)允許您隨著訊息傳遞屬性包，而我們會使用它來傳遞相互關聯識別碼。

服務匯流排佇列會使用以 TCP 為基礎的通訊協定。 Application Insights 不會自動追蹤佇列作業，所以我們會以手動方式進行追蹤。 清除佇列作業是推送型 API，無法完全加以追蹤。

#### <a name="enqueue"></a>加入佇列

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes telemetry item
    // and allows to correlate this operation with its parent and children
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // ServiceBus Queue allows to pass property bag along with the message
    // we will use them to pass our correlation identifiers (and other context)
    // to the consumer
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```C#
public async Task Process(BrokeredMessage message)
{
    // once the message is taken from the queue, create ReqeustTelemetry to track its processing
    // it may also make sense to get name from the message
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure 儲存體佇列
下列範例示範如何追蹤 [Azure 儲存體佇列](../storage/storage-dotnet-how-to-use-queues.md)作業，並且讓產生者、取用者與 Azure 儲存體之間的遙測交互關聯。 

Azure 儲存體佇列具有 HTTP API，而 HTTP 要求的 ApplicationInsights DependencyCollector 會追蹤佇列的所有呼叫。
請確定您在 `applicationInsights.config` 中有 `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer`，或如[這裡](app-insights-api-filtering-sampling.md)所述以程式設計方式新增它。

如果您手動設定 Application Insights，請務必建立並初始化 `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`，類似於：
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// you can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add Azure Storage endpoint, otherwise you may experience request signature validation issues on the Storage service side
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// do not forget to dispose module during application shutdown
```

您也需要將 ApplicationInsights 作業識別碼與 Azure 儲存體 RequestId 相互關聯。 請查看[本文](../storage/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)，了解如何設定和取得儲存體要求用戶端和伺服器要求識別碼。

#### <a name="enqueue"></a>加入佇列
由於 Azure 儲存體佇列支援 HTTP API，ApplicationInsights 會自動追蹤所有作業與佇列。 在許多情況下，此檢測應該就足夠了。
不過，若要讓取用者端追蹤與生產者追蹤相互關聯，您需要傳遞一些相互關聯內容，類似於我們在「相互關聯的 Http 通訊協定」中的作業方式。 

在下列範例中，我們會追蹤選擇性 `Enqueue` 作業。 它允許
 - 相互關聯重試 (如果有的話)：全部都有一個通用父代，也就是 `Enqueue` 作業。 否則會作為傳入要求的子系追蹤。 因此，如果佇列有多個邏輯要求，可能難以找到導致重試的呼叫。
 - 使 Azure 儲存體記錄 (必要時) 與 Application Insights 遙測相互關聯。

`Enqueue` 作業是「父代」作業的子系 (例如，傳入 HTTP 要求)，而 'Http' 相依性呼叫是 `Enqueue` 作業的子系以及傳入要求的孫系。

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload
    // e.g. if you choose to pass payload serialized to json, it may look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Azure Storage logs and ApplciationInsights telemetry
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}  
```

如果您因為其他原因，而想要減少您應用程式回報的遙測資料量，或不想追蹤 `enqueue` 作業，您可以直接使用`Activity` API：

- 建立 (和啟動) 新的 `Activity`，而不要啟動 ApplicationInsights 作業 (您不需要指派除了作業名稱以外的任何屬性)。
- 將 `yourActivity.Id` 序列化成為訊息承載，而不是 `operation.Telemetry.Id`。 您也可以使用 `Activity.Current.Id`


#### <a name="dequeue"></a>清除佇列
類似於 `Enqueue`，Application Insights 會自動追蹤 Azure 儲存體佇列會的實際 HTTP 要求。 不過 `Enqueue` 作業可能發生於父代內容，例如「傳入」要求內容。 Application Insights SDK 會使這類作業 (及其 HTTP 部分) 與父代要求和相同範圍中報告的其他遙測自動相互關聯。

`Dequeue` 作業很微妙：Application Insights SDK 會自動追蹤 HTTP 要求，但是在訊息剖析前，不會知道相互關聯內容。 不可能相互關聯 HTTP 要求來取得包含遙測其餘部分的訊息。

在許多情況下，將佇列的 HTTP 要求與其他追蹤相互關聯也很有用。 下一個範例示範如何達成此目的。

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // if there is a message, we want to correlate Dequeue operation with processing
            // However we will only know what correlation Id to use AFTER we will get it from the message
            // So we will report telemetry once we know the Ids.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // delete message
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

在下列範例中，我們追蹤「傳入」訊息的方式類似於我們追蹤傳入 HTTP 要求的方式。

```C#
public async Task Process(MessagePayload message)
{
    // once the message is dequeued from the queue, create ReqeustTelemetry to track it's processing
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // it may also make sense to get name from the message
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

同樣地，可能會檢測其他佇列作業。 預覽 (Peek) 作業應以類似清除佇列的方式進行檢測。 不一定要檢測佇列管理作業。 Application Insights 會追蹤 HTTP 這類作業，這在大多數情況下已足夠。

檢測訊息刪除時，請務必設定作業 (相互關聯) 識別碼。 或者，您可以使用 `Activity` API (您就不需要在遙測項目上設定作業識別碼，ApplicationInsights 會為您設定)：

- 取得佇列中的項目後，建立新的 `Activity`
- 使用 `Activity.SetParentId(message.ParentId)` 讓取用者和產生者記錄相互關聯
- 啟動 `Activity`
- 使用 `Start/StopOperation` 協助程式追蹤清除佇列、處理和刪除作業。 從相同的非同步控制流程 (執行內容) 執行此作業。 如此一來，這些作業就會正確地相互關聯。
- 然後停止 `Activity`
- 使用 `Start/StopOperation` 或手動呼叫追蹤遙測 

### <a name="batch-processing"></a>批次處理
有些佇列允許以一項要求取消包含多個訊息的佇列，但處理這類訊息大概是獨立的，而且屬於不同的邏輯作業。
在此情況下，不可能使 `Dequeue` 作業與特定訊息處理相互關聯。

每個訊息處理應該在自己的非同步控制流程中進行。 您可以在[連出相依性追蹤](#outgoing-dependencies-tracking)一節中找到更多詳細資料。

## <a name="long-running-background-tasks"></a>長時間執行的背景工作
有些應用程式可能會應使用者要求，啟動長時間執行的作業。 就追蹤/檢測觀點而言，這與要求或相依性檢測並無不同。 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // process task
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // update status code and success as appropriate
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // update status code and success as appropriate
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

在此範例中，我們使用 `telemetryClient.StartOperation` 來建立 `RequestTelemetry` 和填滿相互關聯內容。 假設您有一項父作業，例如由排程作業的傳入要求所建立。 只要 `BackgroundTask` 在與傳入要求相同的非同步控制流程中啟動，它就會與該父作業相互關聯。 `BackgroundTask` 和所有巢狀遙測項目將會自動與造成它的要求相互關聯，即使在要求結束後亦然。

從沒有任何相關聯作業 (活動) 的背景執行緒啟動工作時，`BackgroundTask` 沒有任何父代。 不過，它可以有巢狀作業，而工作回報的所有遙測項目會與在 `BackgroundTask` 中建立的 `RequestTelemetry` 相互關聯。

## <a name="outgoing-dependencies-tracking"></a>連出相依性追蹤
您需要追蹤自己的「相依性」種類或 Application Insights 不支援的某些作業。

服務匯流排佇列或 Azure 儲存體佇列中的 `Enqueue` 方法可作為這類自訂追蹤的範例。

自訂相依性追蹤的一般方法如下：
- 呼叫 `TelemetryClient.StartOperation` (擴充) 方法，以填滿相互關聯所需的 `DependencyTelemetry` 屬性和其他一些屬性 (開始時間戳記、持續時間)。
- 在 `DependencyTelemetry` 上設定其他自訂屬性：名稱與您需要的任何其他內容
- 進行相依性呼叫並等候它
- 完成時使用 `StopOperation` 停止作業
- 處理例外狀況

`StopOperation` 只會停止已啟動的作業：如果目前執行中作業不符合您想停止的作業，StopOperation 不會做任何動作。 如果您以平行方式在相同的執行內容中啟動多項作業，可能會發生這種情形：

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// this will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

因此您必須確定一律呼叫 `StartOperation` 並在其自己的內容中執行您的工作：
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // update status code and success as appropriate
    }
    catch(...) 
    {
        // update status code and success as appropriate
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>後續步驟

- 了解 Application Insights 中[遙測相互關聯](application-insights-correlation.md)的基本概念。
- 如需 Application Insights 類型和資料模型，請參閱[資料模型](application-insights-data-model.md)。
- 向 Application Insights 報告自訂[事件和計量](app-insights-api-custom-events-metrics.md)。
- 請查看標準內容屬性集合[組態](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)。
- 查看 [System.Diagnostics.Activity 使用者指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)以了解如何使遙測相互關聯

