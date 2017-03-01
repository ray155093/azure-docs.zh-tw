---
title: "使用 .NET Standard 從 Azure 事件中樞接收事件 | Microsoft Docs"
description: "開始使用 .NET Standard 中的 EventProcessorHost 接收訊息"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 57175ddc53d5856cd3492d4c631a92d4bf9247c4
ms.openlocfilehash: 859f87356448041a320c2e126478aabf1efa0d44
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-receiving-messages-with-the-eventprocessorhost-in-net-standard"></a>開始使用 .NET Standard 中的 EventProcessorHost 接收訊息

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleEphReceiver) 上取得此範例。

## <a name="what-will-be-accomplished"></a>將會完成的工作

本教學課程示範如何建立現有的解決方案 **SampleEphReceiver** (在此資料夾中)。 您可以依解決方案現狀執行，使用您的事件中樞和儲存體帳戶值取代 `EhConnectionString`、`EhEntityPath` 和 `StorageAccount` 字串，或遵循本教學課程建立您自己的方案。

在本教學課程中，我們將撰寫一個 .NET Core 主控台應用程式來使用 **EventProcessorHost** 從事件中樞接收訊息。

## <a name="prerequisites"></a>必要條件

1. [Visual Studio 2015](http://www.visualstudio.com)。

2. [.NET Core Visual Studio 2015 工具](http://www.microsoft.com/net/core)。

3. Azure 訂用帳戶。

4. 事件中樞命名空間。
    
## <a name="receive-messages-from-the-event-hub"></a>從事件中樞接收訊息

### <a name="create-a-console-application"></a>建立主控台應用程式

1. 啟動 Visual Studio，並建立新的 .NET Core 主控台應用程式。

### <a name="add-the-event-hubs-nuget-package"></a>新增事件中樞 NuGet 封裝

* 將下列 NuGet 封裝新增至您的專案：
  * [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
  * [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

### <a name="implement-the-ieventprocessor-interface"></a>實作 IEventProcessor 介面

1. 建立稱為 ’mpleEventProcessor' 的新類別。

2. 在 SimpleEventProcessor.cs 檔案開頭處新增下列 `using` 陳述式。

    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. 實作 `IEventProcessor` 介面。 該類別看起來應該如下所示：

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Collections.Generic;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class SimpleEventProcessor : IEventProcessor
        {
            public Task CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
                return Task.CompletedTask;
            }
    
            public Task OpenAsync(PartitionContext context)
            {
                Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
                return Task.CompletedTask;
            }
    
            public Task ProcessErrorAsync(PartitionContext context, Exception error)
            {
                Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
                return Task.CompletedTask;
            }
    
            public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (var eventData in messages)
                {
                    var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                    Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
                }
    
                return context.CheckpointAsync();
            }
        }
    }
    ```

### <a name="write-a-main-console-method-that-uses-simpleeventprocessor-to-receive-messages-from-an-event-hub"></a>撰寫使用 `SimpleEventProcessor` 來從事件中樞接收訊息的主要主控台方法

1. 在 Program.cs 檔案開頭處加入 `using` 陳述式。
  
    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. 針對事件中樞連接字串、事件中心路徑、儲存體容器名稱、儲存體帳戶名稱和儲存體帳戶金鑰，新增常數至 `Program` 類別。 以對應值取代預留位置。

    ```cs
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 新增稱為 `MainAsync` 的新方法至 `Program` 類別，如下所示：
    ```cs
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. 將下列程式碼行新增至 `Main` 方法：

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 檔案看起來應該會像下面這樣：

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";
    
            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");
    
                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);
    
                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
    
                Console.WriteLine("Receiving. Press enter key to stop worker.");
                Console.ReadLine();
    
                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
  
4. 執行程式，並確定沒有任何錯誤。
  
恭喜！ 您現在已可以從事件中樞接收訊息。

## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

* [事件中樞概觀](event-hubs-what-is-event-hubs.md)
* [建立事件中樞](event-hubs-create.md)
* [事件中樞常見問題集](event-hubs-faq.md)
