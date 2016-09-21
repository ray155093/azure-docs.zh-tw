<properties
	pageTitle="處理 IoT 中樞的裝置到雲端訊息 (Java) | Microsoft Azure"
	description="依照此 Java 教學課程來學習有用的模式，以處理「IoT 中樞」的裝置到雲端訊息。"
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# 教學課程：如何使用 Java 來處理 IoT 中樞的裝置到雲端訊息

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## 簡介

Azure IoT 中樞是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。其他教學課程 ([IoT 中樞入門]和[使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d]) 說明如何使用 IoT 中樞的裝置到雲端和雲端到裝置的基本傳訊功能。

本教學課程是以 [IoT 中樞入門]中顯示的程式碼為基礎，以呈現兩種可用來處理裝置到雲端訊息的可調整模式：

- [Azure Blob 儲存體]中的可靠儲存體，用來儲存裝置到雲端的訊息。有一個常見的案例是「冷路徑」分析，在這項分析中，您可將遙測資料儲存在 Blob 中，以用來作為分析程序的輸入。這些程序是衍生自 [Azure Data Factory] 或 [HDInsight (Hadoop)] 堆疊等工具。

- 「互動式」裝置到雲端訊息的可靠處理。當裝置到雲端訊息因為應用程式後端的一組動作而立即觸發時，這類訊息會是互動式的。例如，裝置可能會傳送一則警示訊息，以觸發將票證插入 CRM 系統的作業。相較之下，「資料點」訊息只會饋送至分析引擎。例如，來自裝置且要儲存以供日後分析的溫度遙測就是資料點訊息。

由於 IoT 中樞會公開[事件中樞][lnk-event-hubs]相容端點以接收裝置到雲端訊息，因此本教學課程會使用 [EventProcessorHost] 執行個體。這個執行個體會：

* 在 Azure Blob 儲存體中可靠地儲存「資料點」訊息。
* 將「互動式」裝置到雲端訊息轉寄到 Azure [服務匯流排佇列]，以立即處理。

服務匯流排有助於確保能可靠地處理互動式訊息，因為它提供了各個訊息的檢查點，以及以時間範圍為基礎的重複資料刪除。

> [AZURE.NOTE] **EventProcessorHost** 執行個體是用來處理互動式訊息的唯一方式。其他選項包括 [Azure Service Fabric][lnk-service-fabric] 和 [Azure 串流分析][lnk-stream-analytics]。

在本教學課程結尾，您會執行三個 Java 主控台應用程式：

* **simulated-device** (在[開始使用 IoT 中樞]教學課程中建立之應用程式的已修改版本) 會每秒傳送資料點裝置到雲端訊息，以及每 10 秒傳送互動式裝置到雲端訊息。此應用程式會使用 AMQPS 通訊協定與 IoT 中樞進行通訊。
* **process-d2c-messages** 會使用 [EventProcessorHost] 類別從「事件中樞」相容端點擷取訊息。接著，會將資料點訊息可靠地儲存在 Azure Blob 儲存體中，並將互動式訊息轉送到「服務匯流排」佇列。
* **process-interactive-messages** 會將互動式訊息從「服務匯流排」佇列中清除。

> [AZURE.NOTE] IoT 中樞對於許多裝置平台和語言 (包括 C、Java 和 JavaScript) 提供 SDK 支援。如需了解如何以實體裝置取代本教學課程中的模擬裝置，以及如何將裝置連接到「IoT 中樞」，請參閱 [Azure IoT 開發人員中心]。

本教學課程可直接套用至事件中樞相容訊息的其他使用方式，例如 [HDInsight (Hadoop)] 專案。如需詳細資訊，請參閱 [Azure IoT 中樞開發人員指南 - 裝置到雲端]。

若要完成此教學課程，您需要下列項目：

+ [開始使用 IoT 中樞]教學課程的完整運作版本。

+ Java SE 8。<br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Java。

+ Maven 3。<br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Maven。

+ 使用中的 Azure 帳戶。<br/>如果您沒有 Azure 訂用帳戶，則只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/free/)。

您應具備 [Azure 儲存體]和 [Azure 服務匯流排]的基本知識。


## 從模擬裝置傳送互動式訊息

在本節中，您會修改您在[開始使用 IoT 中樞]教學課程中建立的模擬裝置應用程式，使其將互動式裝置到雲端訊息傳送給 IoT 中樞。

1. 使用文字編輯器來開啟 simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。這個檔案包含您在 [開始使用 IoT 中樞]教學課程中建立的 **simulated-device** 應用程式。

2. 將下列巢狀類別新增到 **App** 類別中：

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    這個類別與 **simulated-device** 專案中的 **MessageSender** 類似。唯一的差異在於您現在設定的是 **MessageId** 系統屬性，以及一個稱為 **messageType** 的自訂屬性。程式碼會指派一個通用唯一識別碼 (UUID) 給 **MessageId** 屬性。「服務匯流排」可以使用這個識別碼來刪除所接收的重複訊息。此範例使用 **messageType** 屬性來區別互動式訊息與資料點訊息。應用程式會在訊息屬性中傳遞此資訊，而不是在訊息主體中傳遞，因此事件處理器不需要將訊息還原序列化來執行訊息路由。

    > [AZURE.NOTE] 在裝置程式碼中建立用來刪除重複互動式訊息的 **MessageId** 很重要。斷斷續續的網路通訊或其他失敗可能會導致從該裝置多次重新傳輸相同的訊息。您也可以使用語意式訊息識別碼 (例如相關訊息資料欄位的雜湊) 來取代 UUID。

3. 修改 **main** 方法以傳送互動式訊息及資料點訊息，如下列程式碼片段所示︰

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. 儲存並關閉 simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

    > [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，您應該如 MSDN 文章 [Transient Fault Handling (暫時性錯誤處理)] 所建議來實作重試原則 (例如指數型輪詢)。

5. 若要使用 Maven 建置 **simulated-device** 應用程式，請在命令提示字元中的 simulated-device 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```

## 處理裝置到雲端的訊息

在本節中，您會建立 Java 主控台應用程式，以處理來自「IoT 中樞」的裝置到雲端訊息。IoT 中樞會公開與[事件中樞]相容的端點，以讓應用程式讀取裝置對雲端訊息。本教學課程使用 [EventProcessorHost] 類別來處理主控台應用程式中的這些訊息。如需有關如何處理來自「事件中樞」之訊息的詳細資訊，請參閱[開始使用事件中樞]教學課程。

實作可靠的資料點訊息儲存或互動式訊息轉送時，主要的挑戰在於事件處理會倚賴訊息取用者提供其進度的檢查點。此外，為了達到高輸送量，當您從「事件中樞」讀取時，應該以大批方式提供檢查點。如果發生失敗而您還原至先前的檢查點，此方法可能會導致發生重複處理大量訊息的情況。在本教學課程中，您將了解如何將 Azure 儲存體寫入及「服務匯流排」重複資料刪除時間範圍與 **EventProcessorHost** 檢查點同步。

為了能可靠地將訊息寫入至 Azure 儲存體，範例會使用[區塊 Blob][Azure Block Blobs] 的個別區塊認可功能。事件處理器會將訊息累積在記憶體中，直到達到提供檢查點的時間為止。例如，在累積的訊息緩衝區達到 4 MB 的區塊大小上限之後，或在超過「服務匯流排」重複資料刪除時間範圍之後。然後，程式碼會在檢查點之前，先將新區塊認可至 Blob。

事件處理器會使用「事件中樞」訊息位移做為區塊識別碼。這個機制可讓事件處理器在將新區塊認可至儲存體之前，先執行重複資料刪除檢查，以處理在認可區塊與檢查點之間可能發生的損毀。

> [AZURE.NOTE] 本教學課程使用單一儲存體帳戶來寫入從 IoT 中樞擷取的所有訊息。若要判斷您的解決方案中是否需要使用多個「Azure 儲存體」帳戶，請參閱 [Azure 儲存體延展性指導方針]。

應用程式會使用「服務匯流排」重複資料刪除功能，以在處理互動式訊息時避免有重複項目。模擬的裝置會為每個互動式訊息加上一個獨特的 **MessageId** 戳記。此識別碼可讓「服務匯流排」確保在指定的重複資料刪除時間範圍內，不會有兩個具有相同 **MessageId** 的訊息被傳遞給接收者。此重複資料刪除功能連同「服務匯流排」佇列所提供的每一訊息完成語意，使得實作可靠的互動式訊息處理變得相當容易。

為了確保不會在重複資料刪除時間範圍外重新提交任何訊息，程式碼會將 **EventProcessorHost** 檢查點機制與「服務匯流排」佇列重複資料刪除時間範圍同步。此同步處理的執行方式是在每次重複資料刪除時間範圍過去時 (在本教學課程中為一小時)，至少強制執行一次檢查點。

> [AZURE.NOTE] 本教學課程使用單一分割服務匯流排佇列來處理所有擷取自 IoT 中樞的互動式訊息。如需有關如何使用「服務匯流排」佇列來滿足您解決方案之延展性需求的詳細資訊，請參閱 [Azure 服務匯流排]文件。

### 佈建 Azure 儲存體帳戶和服務匯流排佇列

若要使用 [EventProcessorHost] 類別，您必須擁有「Azure 儲存體」帳戶，才能讓 **EventProcessorHost** 記錄檢查點資訊。您可以使用現有的儲存體帳戶，或是依照[關於 Azure 儲存體]中的指示建立新的帳戶。請記下儲存體帳戶連接字串。

> [AZURE.NOTE] 當您複製並貼上儲存體帳戶連接字串時，請確定當中未包含任何空格。

您也需要服務匯流排佇列以可靠地處理互動式訊息。您可以透過程式設計方式，建立一個重複資料刪除時間範圍為一小時的佇列，如[如何使用服務匯流排佇列][Service Bus queue]所述。或者，您也可以依照下列步驟使用 [Azure 傳統入口網站][lnk-classic-portal]：

1. 按一下左下角的 [新增]。接著，按一下 [應用程式服務] > [服務匯流排] > [佇列] > [自訂建立]。輸入名稱 **d2ctutorial**，選取區域，然後使用現有命名空間或建立一個新命名空間。請記下命名空間名稱，稍後在本教學課程中，您將會需要用到它。在下一個頁面上，選取 [啟用重複偵測]，然後將 [重複的偵測記錄期間] 設定為一小時。接著，按一下右下角的勾選記號來儲存您的佇列組態。

    ![在 Azure 入口網站中建立佇列][30]

2. 在服務匯流排佇列清單中，按一下 [d2ctutorial]，然後按一下 [設定]。建立兩個共用的存取原則，一個名為**傳送**，具備**傳送**權限，而另一個名為**接聽**，具備**接聽**權限。請將兩個原則的「主索引鍵」值都記下，稍後在本教學課程中，您將會需要用到它們。完成時，請按一下底部的 [儲存]。

    ![在 Azure 入口網站中設定佇列][31]

### 建立事件處理器

在本節中，您會建立一個 Java 應用程式來處理來自「事件中樞」相容端點的訊息。

第一個工作是新增名為 **process-d2c-messages** 的 Maven 專案，此專案會從「IoT 中樞」的「事件中樞」相同端點接收裝置到雲端訊息，然後將這些訊息路由傳送給其他後端服務。

1. 在命令提示字元中使用下列命令，在您於[開始使用 IoT 中樞]教學課程所建立的 [iot-java-get-started] 資料夾中，建立名為 **process-d2c-messages** 的 Maven 專案。注意，這是一個單一且非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 [process-d2c-messages] 資料夾。

3. 使用文字編輯器，開啟 [process-d2c-messages] 資料夾中的 pom.xml 檔案，並在 **dependencies** 節點新增下列相依性。這些相依性可讓您在應用程式中使用 azure-eventhubs、azure-eventhubs-eph 及 azure-servicebus 套件，以與 IoT 中樞及「服務匯流排」佇列進行互動：

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

下一個工作是將 **ErrorNotificationHandler** 類別新增到專案中。

1. 使用文字編輯器來建立 process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\ErrorNotificationHandler.java 檔案。將下列程式碼新增到該檔案中，以顯示來自 **EventProcesssorHost** 執行個體的錯誤訊息︰

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. 儲存並關閉 ErrorNotificationHandler.java 檔案。

現在，您可以新增一個實作 **IEventProcessor** 介面的類別。**EventProcessorHost** 類別會呼叫此類別以處理從「IoT 中樞」收到的「裝置到雲端」訊息。此類別中的程式碼會實作邏輯以在 Blob 容器中可靠地儲存訊息，然後將互動式訊息轉送至「服務匯流排」佇列。

**onEvents** 方法會設定 **latestEventData** 變數，以追蹤此事件處理器所讀取最新訊息的位移和序號。請記住，每個處理器都會負責單一分割區。**onEvents** 方法接著會從「IoT 中樞」接收一批訊息，並依照下列方式處理這些訊息：它會將互動式訊息傳送到「服務匯流排」佇列，並將資料點訊息附加到 **toAppend** 記憶體緩衝區。如果記憶體緩衝區達到 4 MB 區塊限制，或已超過重複資料刪除時間範圍 (在本教學課程中為上一個檢查點之後一小時)，則該方法都會觸發檢查點。

**AppendAndCheckPoint** 方法會先為要附加到 Blob 的區塊產生 **blockId**。Azure 儲存體會要求所有區塊識別碼都具有相同的長度，因此這個方法會在前面加上零來填補位移。接著，如果 Blob 中已經有具有此識別碼的區塊，此方法就會以目前的緩衝區內容覆寫它。

> [AZURE.NOTE] 若要簡化程式碼，本教學課程會使用每個分割的單一 blob 檔案來儲存訊息。實際的解決方案會藉由在一段特定的時間之後，或在檔案達到特定大小時，建立其他檔案，來實作檔案輪替。請記住，Azure 區塊 Blob 最多可包含 195 GB 的資料。

下一個工作是實作 **IEventProcessor** 介面：

1. 使用文字編輯器來建立 process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\EventProcessor.java 檔案。

2. 將下列匯入項和類別定義新增到 EventProcessor.java 檔案中。**EventProcessor** 類別會實作 **IEventProcessor** 介面，以定義「事件中樞」用戶端的行為︰

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. 將下列方法新增到 **EventProcessor** 類別中，以實作 **IEventProcessor** 介面︰

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. 將下列類別層級變數新增到 **EventProcessor** 類別中：

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. 新增一個帶有下列 **EventProcessor** 類別之簽章的 **AppendAndCheckPoint** 方法︰

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. 將下列程式碼新增到 **AppendAndCheckPoint** 方法中，以擷取分割區中目前的訊息位移和序號︰

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. 在 **AppendAndCheckPoint** 方法中，使用目前的位移值來為下一個要儲存到 Blob 的區塊建立 **BlockEntry** 執行個體︰

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. 在 **AppendAndCheckPoint** 方法中，將最新的一組訊息上傳到區塊 Blob，並擷取目前的區塊清單︰

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. 在 **AppendAndCheckPoint** 方法中，在新的 Blob 中建立初始區塊或將區塊附加到現有的 Blob：

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. 最後在 **AppendAndCheckPoint** 方法中，在分割區上建立檢查點，並準備儲存下一個區塊的訊息︰

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. 在 **onEvents** 方法中，新增下列程式碼以從「IoT 中樞」端點擷取訊息，並將互動式訊息傳送給「服務匯流排」佇列。然後在區塊已滿或逾時過期時，呼叫 **AppendAndCheckPoint**︰

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. 最後，在 **onEvents** 方法中，新增 `else if' 子句，以在逾時過期而沒有任何來自「IoT 中樞」的訊息時，呼叫 **AppendAndCheckPoint**：

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. 儲存對 EventProcessor.java 檔案所做的變更。

**process-d2c-messages** 專案中的最後一個工作是將程式碼新增到 **main** 方法中，以將 **EventProcessorHost** 執行個體具現化。

1. 使用文字編輯器來開啟 process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

2. 在此檔案中新增下列 **import** 陳述式：

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. 將下列類別層級變數新增到 **App** 類別中。使用您先前在[佈建 Azure 儲存體帳戶和服務匯流排佇列](#provision-an-azure-storage-account-and-a-service-bus-queue)一節中記下的 Azure 儲存體帳戶連接字串來取代 **{yourstorageaccountconnectionstring}**︰

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. 將下列類別層級變數新增到 **App** 類別中，然後使用您的「服務匯流排」命名空間來取代 **{yourservicebusnamespace}**，以及使用您佇列的 **send** 索引鍵來取代 **{yourservicebussendkey}**。您先前在[佈建 Azure 儲存體帳戶和服務匯流排佇列](#provision-an-azure-storage-account-and-a-service-bus-queue)一節中，已記下命名空間和 **listen** 索引鍵值︰

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. 將下列類別層級變數新增至 **App** 類別。使用「事件中樞」相容端點名稱來取代 **{youreventhubcompatibleendpoint}**。該端點名稱看起來會像 **ihs....namespace**，因此您應該移除 **sb://** 首碼和 **.servicebus.windows.net/** 尾碼。使用「事件中樞」相容名稱來取代 **{youreventhubcompatiblename}**。使用 iothubowner** 索引鍵來取代 *{youriothubkey}。您在 **開始使用適用於 Java 的 Azure IoT 中樞* 教學課程的[建立 IoT 中樞][lnk-create-an-iot-hub]一節中，已記下這些值：

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. 依下列方式修改 **main** 方法的簽章：

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. 將下列程式碼新增到 **main** 方法中，以取得對儲存訊息之 Blob 容器的參考︰

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. 將下列程式碼新增到 **main** 方法中，以取得對「服務匯流排」服務的參考︰

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. 在 **main** 方法中，設定並建立 **EventProcessorHost** 執行個體。**setInvokeProcessorAfterReceiveTimeout** 選項可確保即使在沒有訊息要進行處理的情況下，**EventProcessorHost** 執行個體也會叫用 **IEventProcessor** 介面中的 **onEvents** 方法。**onEvents** 方法則會接著在逾時到期時，一律叫用 **AppendAndCheckPoint** 方法。

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. 在 **main** 方法中，向 **EventProcessorHost**執行個體註冊 **IEventProcessor** 實作：

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. 最後，將邏輯新增到 **main** 方法中來關閉 **EventProcessorHost** 執行個體︰

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. 儲存並關閉 process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

13. 若要使用 Maven 來建置 **process-d2c-messages** 應用程式，請在命令提示字元中，於 [process-d2c-messages] 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```

## 接收互動式訊息

在本節中，您將撰寫會從「服務匯流排」佇列接收互動式訊息的 Java 主控台應用程式。

第一個工作是新增名為 **process-interactive-messages** 的 Maven 專案，以接收「服務匯流排」佇列上從 **EventProcessor** 執行個體傳送的訊息。

1. 在命令提示字元中使用下列命令，在您於[開始使用 IoT 中樞]教學課程所建立的 [iot-java-get-started] 資料夾中，建立名為 **process-interactive-messages** 的 Maven 專案。注意，這是一個單一且非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 [process-interactive-messages] 資料夾。

3. 使用文字編輯器，開啟 [process-interactive-messages] 資料夾中的 pom.xml 檔案，並在 **dependencies** 節點新增下列相依性。此相依性可讓您在應用程式中使用 azure-servicebus 套件，以與「服務匯流排」佇列進行互動：

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

下一個工作是新增程式碼，以從「服務匯流排」佇列擷取訊息。

1. 使用文字編輯器來開啟 process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

2. 將下列 `import` 陳述式新增到檔案中：

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. 將下列類別層級變數新增到 **App** 類別中，然後使用您的「服務匯流排」命名空間來取代 **{yourservicebusnamespace}**，以及使用您佇列的 **listen** 索引鍵來取代 **{yourservicebuslistenkey}**。您先前在[佈建 Azure 儲存體帳戶和服務匯流排佇列](#provision-an-azure-storage-account-and-a-service-bus-queue)一節中，已記下命名空間和 **listen** 索引鍵值︰

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. 將下列巢狀類別新增到 **App** 類別中，以從佇列接收訊息：

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. 依下列方式修改 **main** 方法的簽章：

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. 在 **main** 方法中，新增下列程式碼以開始接聽新訊息：

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. 儲存並關閉 process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

8. 若要使用 Maven 來建置 **process-interactive-messages** 應用程式，請在命令提示字元中，於 [process-interactive-messages] 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```

## 執行應用程式

現在您已經準備好執行這三個應用程式。

1.	若要執行 **process-interactive-messages** 應用程式，請在命令提示字元或殼層中，瀏覽至 [process-interactive-messages] 資料夾，然後執行下列命令：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![執行 process-interactive-messages][processinteractive]

2.	若要執行 **process-d2c-messages** 應用程式，請在命令提示字元或殼層中，瀏覽至 [process-d2c-messages] 資料夾，然後執行下列命令：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![執行 process-d2c-messages][processd2c]

3.	若要執行 **simulated-device** 應用程式，請在命令提示字元或殼層中，瀏覽至 [simulated-device] 資料夾，然後執行下列命令：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![執行 simulated-device][simulateddevice]

> [AZURE.NOTE] 若要查看您 Blob 檔案中的更新，您可能需要將 **StoreEventProcessor** 類別中的 **MAX\_BLOCK\_SIZE** 常數降低為較小的值，例如 **1024**。這項變更相當有用，因為要讓模擬裝置所傳送的資料達到區塊大小限制需要一些時間。如果使用較小的區塊大小，您就不需要等待這麼久才能看到 Blob 的建立和更新。不過，使用比較大的區塊大小可讓應用程式更有彈性。

## 後續步驟

在本教學課程中，您已學到如何使用 [EventProcessorHost] 類別來可靠地處理資料點訊息和互動式裝置到雲端訊息。

[如何使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d]示範如何從後端將訊息傳送到您的裝置。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 套件][lnk-suite]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Azure Blob 儲存體]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[服務匯流排佇列]: ../service-bus/service-bus-dotnet-get-started-with-queues.md

[Azure IoT 中樞開發人員指南 - 裝置到雲端]: iot-hub-devguide.md#d2c

[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中樞開發人員指南]: iot-hub-devguide.md
[IoT 中樞入門]: iot-hub-java-java-getstarted.md
[開始使用 IoT 中樞]: iot-hub-java-java-getstarted.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[關於 Azure 儲存體]: ../storage/storage-create-storage-account.md#create-a-storage-account
[開始使用事件中樞]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure 儲存體延展性指導方針]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[事件中樞]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Event Hubs Programming Guide]: https://github.com/Azure/azure-event-hubs/blob/master/java/readme.md
[Transient Fault Handling (暫時性錯誤處理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

<!----HONumber=AcomDC_0907_2016-->