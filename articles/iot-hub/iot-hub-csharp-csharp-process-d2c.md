---
title: "處理 IoT 中樞的裝置到雲端訊息 (.Net) | Microsoft Docs"
description: "請依照此教學課程來學習處理 IoT 中心裝置到雲端訊息的有用模式。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 3b65a12369fbd6f0a2993d875225d49fecb4f39b


---
# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>教學課程：如何使用 .Net 來處理 IoT 中樞的裝置到雲端訊息
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>簡介
Azure IoT 中樞是一項完全受管理的服務，可在數百萬個裝置和一個應用程式後端之間進行可靠且安全的雙向通訊。 其他教學課程 ([開始使用IoT 中樞入門]和[使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d]) 說明如何使用 IoT 中樞的裝置到雲端和雲端到裝置的基本傳訊功能。

本教學課程是以 [開始使用IoT 中樞入門] 中顯示的程式碼為基礎，以呈現兩種可用來處理裝置到雲端訊息的可調整模式：

* [Azure Blob 儲存體]中可靠的裝置到雲端訊息的儲存體。 有一個常見的案例是「冷路徑」  分析，在這項分析中，您可將遙測資料儲存在 Blob 中，以用來作為分析程序的輸入。 這些程序是衍生自 [Azure Data Factory] 或 [HDInsight (Hadoop)] 堆疊等工具。
* 「互動式」  裝置到雲端訊息的可靠處理。 當裝置到雲端訊息因為應用程式後端的一組動作而立即觸發時，這類訊息會是互動式的。 例如，裝置可能會傳送一則警示訊息，以觸發將票證插入 CRM 系統的作業。 相較之下，「資料點」  訊息只會饋送至分析引擎。 例如，來自裝置且儲存來以供日後分析使用的溫度遙測就是資料點訊息。

由於 IoT 中樞會公開[事件中樞][lnk-event-hubs]相容端點以接收裝置到雲端訊息，因此本教學課程會使用 [EventProcessorHost] 執行個體。 這個執行個體會：

* 在 Azure Blob 儲存體中可靠地儲存「資料點」  訊息。
* 將「互動式」  裝置到雲端訊息轉寄到 Azure [服務匯流排佇列] ，以立即處理。

服務匯流排有助於確保能可靠地處理互動式訊息，因為它提供了各個訊息的檢查點，以及以時間範圍為基礎的重複資料刪除。

> [!NOTE]
> **EventProcessorHost** 執行個體是用來處理互動式訊息的唯一方式。 其他選項包括 [Azure Service Fabric][lnk-service-fabric] 和 [Azure 串流分析][lnk-stream-analytics]。
> 
> 

在本教學課程結尾，您會執行三個 Windows 主控台應用程式：

* **SimulatedDevice**，這是在 [開始使用IoT 中樞入門] 教學課程中建立之應用程式的已修改版本、每秒可傳送資料點裝置到雲端訊息，而且每 10 秒可傳送互動式裝置到雲端訊息。 此應用程式會使用 AMQP 通訊協定與「IoT 中樞」進行通訊。
* **ProcessDeviceToCloudMessages** 會使用 [EventProcessorHost] 類別來擷取事件中樞相容端點的訊息。 接著，它會將資料點訊息可靠地儲存在 Azure Blob 儲存體中，並將互動式訊息轉送到「服務匯流排」佇列。
* **ProcessD2CInteractiveMessages** 可將互動式訊息從服務匯流排佇列中清除。

> [!NOTE]
> IoT 中樞對於許多裝置平台和語言 (包括 C、Java 和 JavaScript) 提供 SDK 支援。 若要了解如何以實體裝置取代本教學課程中的模擬裝置，以及如何將裝置連接到「IoT 中樞」，請參閱 [Azure IoT 開發人員中心]。
> 
> 

本教學課程可直接套用至「事件中樞」相容訊息的其他使用方式，例如 [HDInsight (Hadoop)] 專案。 如需詳細資訊，請參閱 [Azure IoT 中樞開發人員指南 - 裝置到雲端]。

若要完成此教學課程，您需要下列項目：

* Microsoft Visual Studio 2015。
* 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/free/) 。

您應具備 [Azure 儲存體]和 [Azure 服務匯流排]的基本知識。

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>從模擬的裝置應用程式傳送互動式訊息
在本節中，您會修改您在[開始使用IoT 中樞入門]教學課程中建立的模擬裝置應用程式，使其將互動式裝置到雲端訊息傳送給 IoT 中樞。

1. 在 Visual Studio 的 **SimulatedDevice** 專案中，將下列方法新增 [程式] 類別。
   
    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();
   
        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);
   
        Task.Delay(10000).Wait();
      }
    }
    ```
   
    此方法與 **SimulatedDevice** 專案中的 **SendDeviceToCloudMessagesAsync** 方法類似。 唯一的差異是您現在是設定 **MessageId** 系統屬性，以及稱為 **messageType** 的使用者屬性。
    程式碼會指派一個全域唯一識別碼 (GUID) 給 **MessageId** 屬性。 「服務匯流排」可以使用這個識別碼來刪除所接收的重複訊息。 範例是使用 **messageType** 屬性來區別來自資料點訊息的互動式訊息。 應用程式會在訊息屬性中傳遞此資訊，而不是在訊息主體中傳遞，因此事件處理器不需要將訊息還原序列化來執行訊息路由。
   
   > [!NOTE]
   > 在裝置程式碼中建立用來刪除重複互動式訊息的 **MessageId** 很重要。 斷斷續續的網路通訊或其他失敗可能會導致從該裝置多次重新傳輸相同的訊息。 您也可以使用語意式訊息識別碼 (例如相關訊息資料欄位的雜湊) 來取代 GUID。
   > 
   > 
2. 將下列方法新增到 **Main** 方法中緊接在 `Console.ReadLine()` 行前面：
   
    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````
   
   > [!NOTE]
   > 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章 [Transient Fault Handling (暫時性錯誤處理)]所建議來實作重試原則 (例如指數型輪詢)。
   > 
   > 

## <a name="process-device-to-cloud-messages"></a>處理裝置到雲端的訊息
在本節中，您會建立 Windows 主控台應用程式，以處理來自「IoT 中樞」的裝置到雲端訊息。 「IoT 中樞」會公開「事件中樞」相容端點，以便讓應用程式讀取裝置到雲端訊息。 本教學課程使用 [EventProcessorHost] 類別來處理主控台應用程式中的這些訊息。 如需有關如何處理來自「事件中樞」之訊息的詳細資訊，請參閱 [開始使用事件中樞] 教學課程。

實作可靠的資料點訊息儲存或互動式訊息轉送時，挑戰在於事件處理會倚賴訊息取用者提供其進度的檢查點。 此外，為了達到高輸送量，當您從「事件中樞」讀取時，應該以大批方式提供檢查點。 如果發生失敗而您還原至先前的檢查點，此方法可能會導致發生重複處理大量訊息的情況。 在本教學課程中，您將了解如何將「Azure 儲存體」寫入及「服務匯流排」重複資料刪除時間範圍與 **EventProcessorHost** 檢查點同步。

為了能可靠地將訊息寫入至 Azure 儲存體，範例會使用[區塊 Blob][Azure Block Blobs] 的個別區塊認可功能。 事件處理器會將訊息累積在記憶體中，直到達到提供檢查點的時間為止。 例如，在累積的訊息緩衝區達到 4 MB 的區塊大小上限之後，或在超過「服務匯流排」重複資料刪除時間範圍之後。 然後，程式碼會在檢查點之前，先將新區塊認可至 Blob。

事件處理器會使用「事件中樞」訊息位移做為區塊識別碼。 這個機制可讓事件處理器在將新區塊認可至儲存體之前，先執行重複資料刪除檢查，以處理在認可區塊與檢查點之間可能發生的損毀。

> [!NOTE]
> 本教學課程使用單一「Azure 儲存體」帳戶來寫入從「IoT 中樞」擷取的所有訊息。 若要判斷您的解決方案中是否需要使用多個「Azure 儲存體」帳戶，請參閱 [Azure 儲存體延展性指導方針]。
> 
> 

應用程式會使用「服務匯流排」重複資料刪除功能，以在處理互動式訊息時避免有重複項目。 模擬的裝置應用程式會為每個互動式訊息加上一個獨特的 **MessageId** 戳記。 這些識別碼可讓「服務匯流排」確保在指定的重複資料刪除時間範圍內，不會有兩個具有相同 **MessageId** 的訊息被傳遞給接收者。 此重複資料刪除功能連同「服務匯流排」佇列所提供的每一訊息完成語意，使得實作可靠的互動式訊息處理變得相當容易。

為了確保不會在重複資料刪除時間範圍外重新提交任何訊息，程式碼會將 **EventProcessorHost** 檢查點機制與「服務匯流排」佇列重複資料刪除時間範圍同步。 此同步處理的執行方式是在每次重複資料刪除時間範圍過去時 (在本教學課程中為一小時)，至少強制執行一次檢查點。

> [!NOTE]
> 本教學課程使用單一分割服務匯流排佇列來處理所有擷取自 IoT 中樞的互動式訊息。 如需有關如何使用「服務匯流排」佇列來滿足您解決方案之延展性需求的詳細資訊，請參閱 [Azure 服務匯流排] 文件。
> 
> 

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>佈建 Azure 儲存體帳戶和服務匯流排佇列
若要使用 [EventProcessorHost] 類別，您必須擁有「Azure 儲存體」帳戶，才能讓 **EventProcessorHost** 記錄檢查點資訊。 您可以使用現有的「Azure 儲存體」帳戶，或是依照[關於 Azure 儲存體] 中的指示建立新的帳戶。 請記下「Azure 儲存體」帳戶連接字串。

> [!NOTE]
> 當您複製並貼上「Azure 儲存體」帳戶連接字串時，請確定當中未包含任何空格。
> 
> 

您也需要服務匯流排佇列以可靠地處理互動式訊息。 您可以透過程式設計方式，建立一個重複資料刪除時間範圍為一小時的佇列，如 [如何使用服務匯流排佇列][服務匯流排佇列]中的可靠儲存體，用來儲存裝置到雲端的訊息。 或者，您也可以依照下列步驟，使用 [Azure 傳統入口網站][lnk-classic-portal]：

1. 按一下左下角的 [新增]。 然後按一下 [應用程式服務] > [服務匯流排] > [佇列] > [自訂建立]。 輸入名稱 **d2ctutorial**，選取區域，然後使用現有命名空間或建立一個新命名空間。 在下一個頁面上，選取 [啟用重複偵測]，然後將 [重複的偵測記錄期間] 設定為一小時。 接著，按一下右下角的勾選記號來儲存您的佇列組態。
   
    ![在 Azure 入口網站中建立佇列][30]
2. 在服務匯流排佇列清單中，按一下 [d2ctutorial]，然後按一下 [設定]。 建立兩個共用的存取原則，一個名為**傳送**，具備**傳送**權限，而另一個名為**接聽**，具備**接聽**權限。 完成時，請按一下底部的 [儲存]。
   
    ![在 Azure 入口網站中設定佇列][31]
3. 按一下頂端的 [儀表板]，然後按一下底部的 [連接資訊]。 記下這兩個連接字串。
   
    ![Azure 入口網站中的「佇列」儀表板][32]

### <a name="create-the-event-processor"></a>建立事件處理器
1. 在目前的 Visual Studio 方案中，若要使用 [主控台應用程式] 專案範本來建立 Visual C# Windows 專案，請按一下 [檔案]  >  [新增]  >  [新增專案]。 確定 .NET Framework 為 4.5.1 或更新版本。 將專案命名為 **ProcessDeviceToCloudMessages**，然後按一下 [確定]。
   
    ![Visual Studio 中的新專案][10]
2. 在 [方案總管] 中，於 [ProcessDeviceToCloudMessages] 專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 套件]。 此時會顯示 [NuGet 封裝管理員]  對話方塊。
3. 搜尋 **WindowsAzure.ServiceBus**，按一下 [安裝]，然後接受使用規定。 此操作會對 [Azure 服務匯流排 NuGet 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus)及其所有相依性進行下載、安裝和新增參考。
4. 搜尋 **Microsoft.Azure.ServiceBus.EventProcessorHost**，按一下 [安裝]，然後接受使用規定。 此操作會對 [Azure 服務匯流排事件中樞 - EventProcessorHost NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)及其所有相依性進行下載、安裝和新增參考。
5. 以滑鼠右鍵按一下 **ProcessDeviceToCloudMessages** 專案、按一下 [新增]，然後按一下 [類別]。 將新類別命名為 **StoreEventProcessor**，然後按一下 [確定] 以建立該類別。
6. 在 StoreEventProcessor.cs 檔案開頭處新增下列陳述式：
   
    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
7. 將該類別的主體取代為下列程式碼：
   
    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;
   
      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;
   
      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);
   
      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);
   
      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }
   
      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }
   
      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
   
        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();
   
        return Task.FromResult<object>(null);
      }
   
      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();
   
          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];
   
            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);
   
            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }
   
          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);
   
          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }
   
      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);
   
        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);
   
        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));
   
          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);
   
          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }
   
        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);
   
        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));
   
        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }
   
      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```
   
    **EventProcessorHost** 類別會呼叫此類別以處理從「IoT 中樞」收到的「裝置到雲端」訊息。 此類別中的程式碼會實作邏輯以在 Blob 容器中可靠地儲存訊息，然後將互動式訊息轉送至「服務匯流排」佇列。
   
    **OpenAsync** 方法會將 **currentBlockInitOffset** 變數初始化，此變數可追蹤此事件處理器所讀取第一個訊息的目前位移。 請記住，每個處理器都會負責單一分割區。
   
    **ProcessEventsAsync** 方法可從「IoT 中樞」接收一批訊息，並依照以下方式處理這些訊息：它會將互動式訊息傳送到「服務匯流排」佇列，並將資料點訊息附加到名為 **toAppend** 的記憶體緩衝區。 如果記憶體緩衝區達到 4 MB 限制，或已超過重複資料刪除時間範圍 (在本教學課程中為檢查點之後一小時)，則應用程式都會觸發檢查點。
   
    **AppendAndCheckpoint** 方法會先為要附加的區塊產生 blockId。 Azure 儲存體會要求所有區塊識別碼都具有相同的長度，因此這個方法會以前置的零來填補位移 - `currentBlockInitOffset.ToString("0000000000000000000000000")`。 接著，如果 Blob 中已經有具有此識別碼的區塊，此方法就會以緩衝區目前的內容覆寫它。
   
   > [!NOTE]
   > 為了簡化程式碼，本教學課程會每個分割使用一個 Blob 來儲存訊息。 實際的解決方案會藉由在一段特定的時間之後，或在檔案達到特定大小時，建立其他檔案，來實作檔案輪替。 請記住，Azure 區塊 Blob 最多可包含 195 GB 的資料。
   > 
   > 
8. 在 **Program** 類別中，於最上方新增下列 **using** 陳述式：
   
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
9. 依下列方式修改 **Program** 類別中的 **Main** 方法。 使用[開始使用IoT 中樞入門]教學課程中的 **iothubowner** 連接字串來取代 **{iot hub connection string}**。 使用您在本節開頭提到的連接字串來取代儲存體連接字串。 使用您在本節開頭所提到名為 **d2ctutorial** 之佇列的「傳送」權限來取代「服務匯流排」連接字串：
   
    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";
   
      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();
   
      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```
   
   > [!NOTE]
   > 為了簡單起見，本教學課程使用單一的 [EventProcessorHost] 類別執行個體。 如需詳細資訊，請參閱 [事件中樞程式設計指南]。
   > 
   > 

## <a name="receive-interactive-messages"></a>接收互動式訊息
在本節中，您將撰寫會從「服務匯流排」佇列接收互動式訊息的 Windows 主控台應用程式。 如需有關如何使用「服務匯流排」來建構解決方案的詳細資訊，請參閱[使用服務匯流排建置多層式應用程式][Build multi-tier applications with Service Bus]。

1. 在目前的 Visual Studio 方案中，使用 [主控台應用程式] 專案範本來建立 Visual C# Windows 專案。 將專案命名為 **ProcessD2CInteractiveMessages**。
2. 在 [方案總管] 中，於 [ProcessD2CInteractiveMessages] 專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 套件]。 此操作會顯示 [NuGet 套件管理員]  視窗。
3. 搜尋 **WindowsAzure.ServiceBus**，按一下 [安裝]，然後接受使用規定。 此操作會對 [Azure 服務匯流排](https://www.nuget.org/packages/WindowsAzure.ServiceBus)及其所有相依性進行下載、安裝和新增參考。
4. 在 **Program.cs** 檔案的最上方，新增下列 **using** 陳述式：
   
    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```
5. 最後，將下列幾行新增到 **Main** 方法中。 以名為 **d2ctutorial** 之佇列的**接聽**權限取代連接字串：
   
    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");
   
    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);
   
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);
   
    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();
   
        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);
   
        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);
   
    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>執行應用程式
現在您已經準備好執行應用程式。

1. 在 Visual Studio 的方案總管中，以滑鼠右鍵按一下您的方案，然後選取 [設定啟始專案]。 選取 [多個啟始專案]，然後針對 **ProcessDeviceToCloudMessages**、**SimulatedDevice** 和 **ProcessD2CInteractiveMessages** 專案選取 [啟動] 動作。
2. 按 **F5** 啟動三個主控台應用程式。 **ProcessD2CInteractiveMessages** 應用程式應處理從 **SimulatedDevice** 應用程式傳送的每則互動式訊息。
   
   ![三個主控台應用程式][50]

> [!NOTE]
> 若要查看您 Blob 中的更新，您可能需要將 **StoreEventProcessor** 類別中的 **MAX_BLOCK_SIZE** 常數降低為較小的值，例如 **1024**。 這項變更相當有用，因為要讓模擬裝置應用程式所傳送的資料達到區塊大小限制需要一些時間。 如果使用較小的區塊大小，您就不需要等待這麼久才能看到 Blob 的建立和更新。 不過，使用比較大的區塊大小可讓應用程式更有彈性。
> 
> 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您學到如何使用 [EventProcessorHost] 類別，可靠地處理資料點與互動式裝置到雲端訊息。

[如何使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d]示範如何從後端將訊息傳送到您的裝置。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 套件][lnk-suite]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Azure Blob 儲存體]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[服務匯流排佇列]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Azure IoT 中樞開發人員指南 - 裝置到雲端]: iot-hub-devguide-messaging.md

[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中樞開發人員指南]: iot-hub-devguide.md
[開始使用IoT 中樞入門]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling (暫時性錯誤處理)]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[關於 Azure 儲存體]: ../storage/storage-create-storage-account.md#create-a-storage-account
[開始使用事件中樞]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Azure 儲存體延展性指導方針]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[事件中樞程式設計指南]: ../event-hubs/event-hubs-programming-guide.md
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Build multi-tier applications with Service Bus]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/



<!--HONumber=Nov16_HO5-->


