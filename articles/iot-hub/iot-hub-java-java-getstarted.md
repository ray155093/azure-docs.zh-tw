<properties
    pageTitle="適用於 Java 的 Azure IoT 中樞快速入門 | Microsoft Azure"
    description="採用 Java 的 Azure IoT 中樞快速入門教學課程。 使用 Azure IoT 中樞與 Java 搭配 Microsoft Azure IoT SDK 來實作物聯網解決方案。"
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>


# <a name="get-started-with-azure-iot-hub-for-java"></a>開始使用適用於 Java 的 Azure IoT 中樞

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教學課程結尾處，您會有三個 Java 主控台應用程式：

* **create-device-identity**，這會建立裝置身分識別和相關聯的安全性金鑰，來連線您的模擬裝置。
* **read-d2c-messages**，其中顯示模擬的裝置所傳送的遙測。
* **simulated-device**，這會使用先前建立的裝置身分識別連接到您的 IoT 中樞，並使用 AMQPS 通訊協定每秒傳送遙測訊息。

> [AZURE.NOTE] 文章 [IoT 中樞 SDK][lnk-hub-sdks] 提供可讓您可以用來建置兩個應用程式，以在裝置和您的方案後端上執行的各種 SDK 的相關資訊。

若要完成此教學課程，您需要下列項目：

+ Java SE 8。 <br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Java。

+ Maven 3。  <br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Java。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

最後一個步驟，記下**主要金鑰**值，然後按一下 [傳訊]。 記下 [傳訊] 刀鋒視窗上的**事件中樞相容名稱**和**事件中樞相容端點**。 在建立 **read-d2c-messages** 應用程式時需要用到這三個值。

![Azure 入口網站的 IoT 中樞傳訊刀鋒視窗][6]

您現在已經建立 IoT 中樞，並擁有完成本教學課程所需的 IoT 中樞主機名稱、IoT 中樞連接字串、IoT 中樞主要金鑰、事件中樞相容名稱和事件中樞相容端點。

## <a name="create-a-device-identity"></a>建立裝置識別

在本節中，您會建立 Java 主控台應用程式，它會在 IoT 中樞的身分識別登錄中建立新的裝置身分識別。 裝置無法連線到 IoT 中樞，除非它在裝置識別登錄中具有項目。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]的**裝置識別登錄**一節。 執行這個主控台應用程式時，它會產生唯一的裝置識別碼及金鑰，當裝置向 IoT 中樞傳送裝置對雲端訊息時，可以用來識別裝置本身。

1. 建立稱為 iot-java-get-started 的新的空資料夾。 在 iot-java-get-started 資料夾的命令提示字元下，使用下列命令建立名為 **create-device-identity** 的新 Maven 專案。 注意，這是一個單一且非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 create-device-identity 資料夾。

3. 使用文字編輯器，在 create-device-identity 資料夾中開啟 pom.xml 檔案，並對 [相依性]  節點新增下列相依性。 這可讓您在應用程式中使用 iothub-service-sdk 封裝：

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器開啟 create-device-identity\src\main\java\com\mycompany\app\App.java 檔案。

6. 在此檔案中新增下列 **import** 陳述式：

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 將下列類別層級變數新增至 [應用程式] 類別，並以您稍早記下的值取代 **{yourhubconnectionstring}**：

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. 修改 **main** 方法的簽章以加入如下所示的例外狀況：

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. 新增下列程式碼做為 **main** 方法的主體。 如果還沒有裝置，此程式碼會在 IoT 中樞身分識別登錄中建立名為 javadevice  的裝置。 然後它會顯示稍後需要用到的裝置識別碼和金鑰：

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. 儲存並關閉 App.java 檔案。

11. 若要使用 Maven 建置 **create-device-identity** 應用程式，請在命令提示字元中的 create-device-identity 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```

12. 若要使用 Maven 執行 **create-device-identity** 應用程式，請在命令提示字元中的 create-device-identity 資料夾內執行下列命令：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. 記下**裝置識別碼**和**裝置金鑰**。 稍後在建立連線至做為裝置之 IoT 中樞的應用程式時，會需要這些資料。

> [AZURE.NOTE] IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對中樞的安全存取。 它會儲存裝置識別碼和金鑰來做為安全性認證，以及啟用或停用旗標，讓您用來停用個別裝置的存取。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]。

## <a name="receive-device-to-cloud-messages"></a>接收裝置到雲端的訊息

在本節中，您會建立 Java 主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。 IoT 中樞會公開與[事件中樞][lnk-event-hubs-overview]相容的端點以讓您讀取裝置到雲端訊息。 為了簡單起見，本教學課程會建立的基本讀取器不適合用於高輸送量部署。 [處理裝置到雲端的訊息][lnk-process-d2c-tutorial]教學課程會說明如何大規模處理裝置到雲端的訊息。 [開始使用事件中樞][lnk-eventhubs-tutorial]教學課程則會提供進一步資訊，說明如何處理來自事件中樞的訊息，而且此教學課程也適用於 IoT 中樞事件中樞相容端點。

> [AZURE.NOTE] 用於讀取裝置到雲端訊息的事件中樞相容端點一律會使用 AMQPS 通訊協定。

1. 在「建立裝置識別」  一節所建立的 iot-java-get-started 資料夾中，於命令提示字元下使用下列命令建立名為 **read-d2c-messages** 的新 Maven 專案。 注意，這是一個單一且非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 read-d2c-messages 資料夾。

3. 使用文字編輯器，在 read-d2c-messages 資料夾中開啟 pom.xml 檔案，並對 [相依性]  節點新增下列相依性。 這可讓您在應用程式中使用 eventhubs-client 封裝，以從事件中樞相容端點進行讀取：

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器開啟 read-d2c-messages\src\main\java\com\mycompany\app\App.java 檔案。

6. 在此檔案中新增下列 **import** 陳述式：

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. 將下列類別層級變數新增到 **App** 類別中。 以先前記下的值取代 **{youriothubkey}**、**{youreventhubcompatibleendpoint}** 和 **{youreventhubcompatiblename}**：

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. 在 **App** 類別中新增下列 **receiveMessages** 方法。 這個方法會建立 **EventHubClient** 執行個體以連線至事件中樞相容的端點，然後以非同步方式建立 **PartitionReceiver** 執行個體以讀取事件中樞資料分割。 它會不斷地重複，並列印訊息的詳細資料，直到應用程式終至為止。

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] 在建立開始執行後只會讀取傳送到 IoT 中樞之訊息的收件者時，這個方法會使用篩選器。 這很適合測試環境，因為如此一來您就可以看到目前的訊息集。 在生產環境中，您的程式碼應該要確定它能處理所有訊息；如需詳細資訊，請參閱[如何處理 IoT 中樞裝置到雲端的訊息][lnk-process-d2c-tutorial]教學課程。

9. 修改 **main** 方法的簽章，以加入如下所示的例外狀況：

    ```
    public static void main( String[] args ) throws IOException
    ```

10. 在 **App** 類別中對 **main** 方法新增下列程式碼。 此程式碼會建立兩個 **EventHubClient** 和 **PartitionReceiver** 執行個體，並可讓您在訊息處理完成時關閉應用程式︰

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] 此程式碼假設您已在 F1 (免費) 層建立 IoT 中樞。 免費 IoT 中樞有 "0" 和 "1" 這兩個資料分割。

11. 儲存並關閉 App.java 檔案。

12. 若要使用 Maven 建置 **read-d2c-messages** 應用程式，請在命令提示字元中的 read-d2c-messages 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您會撰寫 Java 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中樞。

1. 在「建立裝置識別」  一節所建立的 iot-java-get-started 資料夾中，於命令提示字元下使用下列命令建立稱為 **simulated-device** 的新 Maven 專案。 注意，這是一個單一且非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 simulated-device 資料夾。

3. 使用文字編輯器，在 simulated-device 資料夾中開啟 pom.xml 檔案，並對 [相依性]  節點新增下列相依性。 這可讓您使用應用程式中的 iothub-java-client 封裝與 IoT 中樞通訊，以及將 Java 物件序列化為 JSON：

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器開啟 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

6. 在此檔案中新增下列 **import** 陳述式：

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. 將下列類別層級變數新增至 **App** 類別，將 **{youriothubname}** 取代為 IoT 中樞名稱，並將 **{yourdevicekey}** 取代為您在「建立裝置識別」一節中產生的裝置金鑰值：

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    此範例應用程式在具現化 **DeviceClient** 物件時使用 **protocol** 變數。 您可以使用 HTTPS 或 AMQPS 通訊協定與 IoT 中樞進行通訊。

8. 在 **App** 類別內新增下列巢狀 **TelemetryDataPoint** 類別，以指定裝置傳送至 IoT 中樞的遙測資料：

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. 在 **App** 類別內新增下列巢狀 **EventCallback** 類別，以顯示 IoT 中樞在處理來自模擬裝置的訊息時，所傳回的認可狀態。 這個方法也會在處理訊息時通知應用程式中的主執行緒：

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. 在 **App** 類別中新增下列巢狀 **MessageSender** 類別。 此類別中的 **run** 方法會產生範例遙測資料以傳送至 IoT 中樞，並先等待認可再傳送下一則訊息：

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    這個方法會在 IoT 中樞認可先前的訊息一秒後，傳送新的裝置到雲端訊息。 此訊息會包含 JSON 序列化物件及 deviceId 與隨機產生的數字，以模擬風向速度感應器。

11. 將 **main** 方法取代為下列程式碼，以建立執行緒來將裝置到雲端訊息傳送至 IoT 中樞：

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. 儲存並關閉 App.java 檔案。

13. 若要使用 Maven 建置 **simulated-device** 應用程式，請在命令提示字元中的 simulated-device 資料夾內執行下列命令：

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在 read-d2c 資料夾的命令提示字元中，執行下列命令以開始監視 IoT 中樞內的第一個資料分割：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![用來監視裝置到雲端訊息的 Java IoT 中樞服務用戶端應用程式][7]

2. 在 simulated-device 資料夾的命令提示字元中，執行下列命令以開始將遙測資料傳送至 IoT 中樞：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![用來傳送裝置到雲端訊息的 Java IoT 中樞裝置用戶端應用程式][8]

3. [Azure 入口網站][lnk-portal]中的 [使用量] 圖格會顯示傳送至中樞的訊息數目︰

    ![顯示傳送到 IoT 中樞之訊息數目的 Azure 入口網站使用量圖格][43]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您在入口網站中設定了新的 IoT 中樞，然後在中樞的身分識別登錄中建立了裝置識別。 您會將此裝置識別用於啟用模擬的裝置應用程式，以將裝置對雲端訊息傳送至中樞。 您也會建立一個應用程式來顯示中樞所接收的訊息。 

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

- [連接您的裝置][lnk-connect-device]
- [開始使用裝置管理][lnk-device-management]
- [開始使用閘道 SDK][lnk-gateway-SDK]

若要了解如何擴充您的 IoT 解決方案及大規模處理裝置對雲端訊息，請參閱[處理裝置對雲端訊息][lnk-process-d2c-tutorial]教學課程。

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Oct16_HO2-->


