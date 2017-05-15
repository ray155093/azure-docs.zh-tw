
---
title: "Service Fabric 的服務遠端處理 | Microsoft Docs"
description: "Service Fabric 遠端處理可讓用戶端和服務使用遠端程序呼叫與服務進行通訊。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: ebca34d5bf092494ea59a4a679f7f1175577320f
ms.contentlocale: zh-tw
ms.lasthandoff: 04/29/2017


---
# <a name="service-remoting-with-reliable-services"></a>使用 Reliable Services 的遠端服務
對於未繫結至特定通訊協定或堆疊 (例如 WebAPI、Windows Communication Foundation (WCF) 或其他項目) 的服務，Reliable Services 架構會提供遠端機制，以便快速且輕鬆設定服務遠端程序呼叫。

## <a name="set-up-remoting-on-a-service"></a>設定在服務上的遠端處理
只要兩個簡單步驟，就能設定服務的遠端處理：

1. 建立服務實作的介面。 這個介面會定義方法，可在您的服務上用於遠端程序呼叫。 方法也必須是傳回工作的非同步方法。 此介面必須實作 `Microsoft.ServiceFabric.Services.Remoting.IService` ，表示服務具有遠端處理介面。
2. 在您的服務中使用遠端接聽程式。 這是提供遠端功能的 `ICommunicationListener` 實作。 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 命名空間包含一個適用於無狀態與具狀態服務的擴充方法 `CreateServiceRemotingListener`，可用於建立使用預設遠端傳輸通訊協定的遠端接聽程式。

例如，下列無狀態服務服務會公開單一方法，透過遠端程序呼叫取得 "Hello World"。

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> 服務介面中的引數和傳回類型可以是任何簡單、複雜或自訂的類型，但必須可由 .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)序列化。
>
>

## <a name="call-remote-service-methods"></a>呼叫遠端服務方法
透過 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 類別使用連至服務的本機 Proxy，可以在服務上使用遠端堆疊呼叫方法。 `ServiceProxy` 方法會使用該服務所實作的相同介面，建立本機 Proxy。 您可以使用該 Proxy 直接在介面上遠端呼叫方法。

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

遠端架構會將在服務擲回的例外狀況傳播給用戶端。 因此在用戶端使用 `ServiceProxy` 的例外狀況處理邏輯，可以直接處理服務擲回的例外狀況。

## <a name="service-proxy-lifetime"></a>服務 Proxy 存留期
建立 ServiceProxy 是輕量型作業，因此沒有限制使用者可建立的數量。 只要有需要，使用者可以重複使用服務 Proxy 。 使用者可以重複使用相同的 Proxy，以防止發生例外狀況。 每個 ServiceProxy 皆包含用戶端透過網路傳送訊息時所用的通訊。 叫用 API 時，我們會透過內部檢查來查看用戶端是否使用有效的通訊。 根據結果，我們會重新建立通訊用戶端。 因此使用者不需要重新建立 serviceproxy，以免發生例外狀況。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory 存留期
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) 是建立不同遠端介面 Proxy 的處理站。 如果您使用 API ServiceProxy.Create 建立 Proxy，則架構會建立單一 ServiceProxyFactory。
當您需要覆寫 [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) 屬性時，最實用的方式是手動建立一個。
處理站是一項昂貴的作業。 ServiceProxyFactory 會保留通訊用戶端的快取。
最佳做法是快取 ServiceProxyFactory 的時間愈長愈好。

## <a name="remoting-exception-handling"></a>遠端例外狀況處理
服務 API 擲出的所有遠端例外狀況會以 AggregateException 的形式傳送回用戶端。 RemoteExceptions 應可進行 DataContract 序列化，否則 Proxy API 會收到包含序列化錯誤的 [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception)。

ServiceProxy 會處理服務分割區 (ServiceProxy 即是為其建立) 的所有容錯移轉列外狀況。 發生容錯移轉例外狀況 (非暫時性例外狀況) 時，ServiceProxy 會重新解析端點，然後以正確的端點再次嘗試呼叫。 容錯移轉例外狀況的重試次數並無限制。
若是發生 TransientExceptions，ServiceProxy 僅會重試呼叫。

預設的重試參數會由 [OperationRetrySettings] 提供。 (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) 使用者可透過將 OperationRetrySettings 物件傳遞至 ServiceProxyFactory 建構函式來設定這些值。

## <a name="next-steps"></a>後續步驟
* [在 Reliable Services 中搭配 OWIN 使用 Web API](service-fabric-reliable-services-communication-webapi.md)
* [使用 Reliable Services 的 WCF 通訊](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services 的安全通訊](service-fabric-reliable-services-secure-communication.md)

