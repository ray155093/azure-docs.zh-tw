<properties
   pageTitle="Reliable Actors 重新進入 | Microsoft Azure"
   description="Service Fabric Reliable Actors 重新進入簡介"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="vturecek"/>


# Reliable Actors 重新進入
Reliable Actors 執行階段預設允許邏輯呼叫以內容為基礎的重新進入。這允許位於相同的呼叫內容鏈結的動作項目可重新進入。例如，動作項目 A 傳送訊息給動作項目 B，而動作項目 B 又將訊息傳送給動作項目 C。當處理訊息時，如果動作項目 C 呼叫動作項目 A，則此訊息是可以重新進入的，因此將允許此訊息。屬於不同呼叫內容的其他任何訊息都將在動作項目 A 上遭到封鎖，直到其處理完畢為止。

動作項目想要禁止邏輯呼叫以內容為基礎的重新進入，能以裝飾動作項目類別的 `ReentrantAttribute(ReentrancyMode.Disallowed)` 將其停用。

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

下列程式碼會示範動作項目類別如何將重新進入模式設定為 `ReentrancyMode.Disallowed`。在此情況下，如果動作項目傳送可重新進入的訊息給另一個動作項目類型，就會擲回 `FabricException` 類型的例外狀況。

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class MyActor : Actor, IMyActor
{
    ...
}
```

## 後續步驟
 - [動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)
 - [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [範例程式碼](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0406_2016-->