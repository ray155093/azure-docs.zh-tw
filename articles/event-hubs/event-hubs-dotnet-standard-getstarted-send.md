---
title: "使用 .NET Standard 傳送事件至 Azure 事件中樞 | Microsoft Docs"
description: "開始在 .NET Standard 中傳送事件至事件中樞"
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
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: c549676b2126448d52ed43af509671d0b593a064

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>開始在 .NET Standard 中傳送訊息至事件中樞

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender) 上取得此範例。

## <a name="what-will-be-accomplished"></a>將會完成的工作

本教學課程示範如何建立現有的解決方案 **SampleSender** (在此資料夾中)。 您可以依解決方案現狀執行，使用您的事件中樞值取代 `EhConnectionString`、`EhEntityPath` 和 `StorageAccount` 字串，或遵循本教學課程建立您自己的方案。

在本教學課程中，我們會撰寫 .NET Core 主控台應用程式將訊息傳送至事件中樞。

## <a name="prerequisites"></a>必要條件

1. [Visual Studio 2015](http://www.visualstudio.com)。

2. [.NET Core Visual Studio 2015 工具](http://www.microsoft.com/net/core)。

3. Azure 訂用帳戶。

4. 事件中樞命名空間。

## <a name="send-messages-to-an-event-hub"></a>將訊息傳送至事件中樞

為了將訊息傳送到事件中樞，我們會使用 Visual Studio 撰寫 C# 主控台應用程式。

### <a name="create-a-console-application"></a>建立主控台應用程式

* 啟動 Visual Studio，並建立新的 .NET Core 主控台應用程式。

### <a name="add-the-event-hubs-nuget-package"></a>新增事件中樞 NuGet 封裝

1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]。

2. 按一下 [瀏覽] 索引標籤，然後搜尋「Microsoft Azure 事件中樞」並選取 [Microsoft Azure 事件中樞] 項目。 按一下 [安裝]  完成安裝作業，然後關閉此對話方塊。

### <a name="write-some-code-to-send-messages-to-the-event-hub"></a>撰寫一些程式碼來傳送訊息至事件中樞

1. 在 Program.cs 檔案開頭處新增以下 `using` 陳述式。

    ```cs
    using Microsoft.Azure.EventHubs;
    ```

2. 針對事件中樞連接字串和實體路徑 (個別事件中樞名稱)，新增常數至 `Program` 類別。 將方括號中的預留位置以建立事件中樞時所取得的正確值取代。

    ```cs
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. 新增稱為 `MainAsync` 的新方法至 `Program` 類別，如下所示：

    ```cs
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();
    }
    ```
    
4. 新增稱為 `SendMessagesToEventHub` 的新方法至 `Program` 類別，如下所示：

    ```cs
    // Creates an Event Hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. 將下列程式碼行新增至 `Program` 類別中的 `Main` 方法。

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 看起來應該會像下面這樣。

    ```cs
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
    
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
    
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
    
                await SendMessagesToEventHub(100);
    
                await eventHubClient.CloseAsync();
    
                Console.WriteLine("Press any key to exit.");
                Console.ReadLine();
            }
    
            // Creates an Event Hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }
    
                    await Task.Delay(10);
                }
    
                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```
  
6. 執行程式，並確定沒有執回任何錯誤。
  
恭喜！ 您現在已可以傳送訊息至事件中樞。

## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

* [從事件中樞接收事件](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [事件中樞概觀](event-hubs-what-is-event-hubs.md)
* [建立事件中樞](event-hubs-create.md)
* [事件中樞常見問題集](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


