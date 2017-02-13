---
title: "Reliable Actors 計時器和提醒 | Microsoft Docs"
description: "Service Fabric Reliable Actors 計時器和提醒簡介。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 56b2e13c3bf053175e357a627d45a91d9a9a4ba9


---
# <a name="actor-timers-and-reminders"></a>動作項目計時器和提醒
動作項目可藉由註冊計時器或提醒來排程本身的週期性工作。 本文示範如何使用計時器和提醒，以及說明它們之間的差異。

## <a name="actor-timers"></a>動作項目計時器
動作項目計時器提供 .NET 計時器的簡單包裝函式，以確保回呼方法採用動作項目執行階段所提供的回合式並行保證。

動作項目可以在其基底類別使用 `RegisterTimer` 和 `UnregisterTimer` 方法註冊和取消其計時器。 下列範例示範如何使用計時器 API。 API 和 .NET 計時器非常類似。 在此範例中，當計時器到期時，動作項目執行階段將會呼叫 `MoveObject` 方法。 此方法保證會採用回合式並行存取。 這表示沒有其他動作項目方法或計時器/提醒回呼將在進行中，直到此回呼完成執行為止。

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```

當回呼完成執行後，就會啟動下一期間的計時器。 這表示回呼執行時計時器將會停止，並在回呼完成時重新啟動。

動作項目執行階段會儲存在回呼完成時，對動作項目的狀態管理員所做的變更。 如果儲存狀態時發生錯誤，將會停用該動作項目物件並啟動新的執行個體。 

當動作項目在記憶體回收期間停用時，將會停止所有的計時器。 而在此之後不會叫用任何計時器回呼。 此外，動作項目執行階段並不保留任何停用前執行中的計時器資訊。 由動作項目來決定任何未來重新啟動時所需計時器的註冊。 如需詳細資訊，請參閱 [動作項目記憶體回收](service-fabric-reliable-actors-lifecycle.md)一節。

## <a name="actor-reminders"></a>動作項目提醒
提醒是一個會在指定時間於動作項目上觸發持續性回呼的機制。 其功能很類似計時器。 但與計時器不同的是，在所有情況下都會觸發提醒，直到動作項目明確地取消註冊它們或動作項目明確刪除為止。 具體而言，動作項目的停用和容錯移轉會觸發提醒，因為動作項目執行階段所保存的動作項目提醒相關資訊。

若要註冊提醒，動作項目會呼叫基底類別提供的 `RegisterReminderAsync` 方法，如下列範例所示：

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

在此範例中， `"Pay cell phone bill"` 為該提醒名稱。 這是動作項目用來唯一識別提醒的一個字串。 `BitConverter.GetBytes(amountInDollars)` 為與提醒相關聯的內容。 其會作為提醒回呼的引數傳回給動作項目，也就是 `IRemindable.ReceiveReminderAsync`。

使用提醒的動作項目必須實作 `IRemindable` 介面，如下列範例所示。

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

當觸發提醒時，Reliable Actors 執行階段將會叫用動作項目上的 `ReceiveReminderAsync` 方法。 一個動作項目可以註冊多個提醒，而每當觸發這些提醒中的任一個時，便會叫用 `ReceiveReminderAsync` 方法。 動作項目可以使用傳遞至 `ReceiveReminderAsync` 方法的提醒名稱，以找出已觸發的提醒。

當 `ReceiveReminderAsync` 呼叫完成時，動作項目執行階段會儲存動作項目狀態。 如果儲存狀態時發生錯誤，將會停用該動作項目物件並啟動新的執行個體。 

若要將提醒取消註冊，動作項目會呼叫 `UnregisterReminderAsync` 方法，如下列範例所示。

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```

如上所示，`UnregisterReminderAsync` 方法會接受 `IActorReminder` 介面。 動作項目基底類別支援 `GetReminder` 方法，在傳遞進提醒名稱時可以用來擷取 `IActorReminder` 介面。 這很方便，因為動作項目不需保存從 `RegisterReminder` 方法呼叫傳回的 `IActorReminder` 介面。

## <a name="next-steps"></a>後續步驟
* [動作項目事件](service-fabric-reliable-actors-events.md)
* [動作項目重新進入](service-fabric-reliable-actors-reentrancy.md)
* [動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)
* [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [範例程式碼](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Nov16_HO3-->


