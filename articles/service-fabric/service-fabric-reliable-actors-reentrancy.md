---
title: Reliable Actors 重新進入 | Microsoft Docs
description: Service Fabric Reliable Actors 重新進入簡介
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2016
ms.author: vturecek

---
# Reliable Actors 重新進入
Reliable Actors 執行階段預設允許邏輯呼叫以內容為基礎的重新進入。這允許位於相同的呼叫內容鏈結的動作項目可重新進入。例如，動作項目 A 傳送訊息給動作項目 B，而動作項目 B 又將訊息傳送給動作項目 C。當處理訊息時，如果動作項目 C 呼叫動作項目 A，則此訊息是可以重新進入的，因此將允許此訊息。屬於不同呼叫內容的其他任何訊息都將在動作項目 A 上遭到封鎖，直到其處理完畢為止。

有兩個選項適用於 `ActorReentrancyMode` 列舉中定義的動作項目重新進入︰

* `LogicalCallContext` (預設行為)
* `Disallowed` - 停用重新進入

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

可在註冊期間在 `ActorService` 的設定中設定重新進入。此設定適用於動作項目服務中建立的所有動作項目執行個體。

下列範例會示範動作項目服務如何將重新進入模式設定為 `ActorReentrancyMode.Disallowed`。在此情況下，如果動作項目傳送可重新進入的訊息給另一個動作項目類型，就會擲回 `FabricException` 類型的例外狀況。

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context, 
                    actorType, () => new Actor1(), 
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```

## 後續步驟
* [動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)
* [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [範例程式碼](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->