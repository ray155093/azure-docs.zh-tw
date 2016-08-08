<properties
	pageTitle="使用 IoT 中樞傳送雲端到裝置訊息 | Microsoft Azure"
	description="請遵循本教學課程，以了解如何搭配 Java 使用 Azure IoT 中樞傳送雲端到裝置的訊息。"
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
     ms.date="06/23/2016"
     ms.author="dobett"/>

# 教學課程：如何使用 IoT 中樞和 Java 傳送雲端到裝置訊息

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## 簡介

Azure IoT 中樞是一項完全受管理的服務，有助於讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。[IoT 中心入門]教學課程會示範如何建立 IoT 中心、在其中佈建裝置識別，以及編寫模擬的裝置，以傳送裝置到雲端的訊息。

本教學課程是以[開始使用 IoT 中樞]為基礎。這會說明如何：

- 從您的應用程式雲端後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。
- 接收裝置上的雲端到裝置訊息。
- 從您的應用程式雲端後端，要求確認收到從 IoT 中樞傳送到裝置的訊息 (*意見反應*)。

您可以在 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]中，找到有關雲端到裝置訊息的詳細資訊。

在本教學課程結尾，您將會執行兩個 Java 主控台應用程式：

* **simulated-device**：在[開始使用 IoT 中樞]中建立的應用程式修改版本，可連接到您的「IoT 中樞」並接收雲端到裝置訊息。
* **send-c2d-messages**：會將雲端到裝置訊息透過「IoT 中樞」傳送到模擬的裝置，然後接收其傳遞通知。

> [AZURE.NOTE] 「IoT 中樞」透過 Azure IoT 裝置 SDK 為許多裝置平台和語言 (包括 C、Java 及 Javascript) 提供 SDK 支援。如需有關如何將您的裝置與本教學課程中的程式碼連接 (通常是連接到「Azure IoT 中樞」) 的逐步指示，請參閱 [Azure IoT 開發人員中樞]。

若要完成此教學課程，您需要下列項目：

+ Java SE 8。<br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Java。

+ Maven 3。<br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Maven。

+ 使用中的 Azure 帳戶。(如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。)

## 在模擬裝置上接收訊息

在本節中，您會修改在[開始使用 IoT 中樞]中建立的模擬裝置應用程式，以接收來自 IoT 中樞的雲端對裝置訊息。

1. 使用文字編輯器開啟 simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

2. 在 **App** 類別中新增下列 **MessageCallback** 巢狀類別。裝置從 IoT 中樞接收到訊息時會叫用 **execute** 方法。在此範例中，裝置一律會通知中樞其已完成訊息︰

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. 修改 **main** 方法來建立 **MessageCallback** 執行個體，並先呼叫 **setMessageCallback** 方法再開啟用戶端，如下所示︰

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] 如果您使用 HTTP/1 而非 AMQP 做為傳輸，**DeviceClient** 執行個體不會經常檢查 IoT 中樞是否傳來訊息 (每隔 25 分鐘以上)。如需有關 AMQP 與 HTTP/1 支援間的差異，以及 IoT 中樞節流的詳細資訊，請參閱 [IoT 中樞開發人員指南][IoT Hub Developer Guide - C2D]。

## 傳送雲端到裝置訊息

在本節中，您會建立 Java 主控台應用程式，將雲端到裝置訊息傳送至模擬裝置應用程式。您需要在[開始使用 IoT 中樞]教學課程中新增裝置的裝置識別碼，以及您 IoT 中樞的連接字串 (可在 [Azure 入口網站]中找到)。

1. 在命令提示字元中使用下列命令建立名為 **send-c2d-messages** 的新 Maven 專案。請注意，這是一個非常長的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽到新的 send-c2d-messages 資料夾。

3. 使用文字編輯器，在 send-c2d-messages 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。這可讓您使用應用程式中的 **iothub-java-service-client** 套件與 IoT 中樞服務通訊：

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器開啟 send-c2d-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 檔案。

6. 在此檔案中新增下列 **import** 陳述式：

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 將下列類別層級變數新增至 **App** 類別，並以您稍早記下的值取代 **{yourhubconnectionstring}** 和 **{yourdeviceid}**：

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```
    
8. 使用下列連線到您 IoT 中樞的程式碼取代 **main** 方法，傳送訊息給您的裝置，然後等候裝置已接收並處理訊息的通知︰

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在生產環境程式碼中，您應該如 MSDN 文章[暫時性錯誤處理]所建議，實作重試原則 (例如指數型輪詢)。

## 執行應用程式

現在您已經準備好執行應用程式。

1. 在 simulated-device 資料夾的命令提示字元中，執行下列命令以開始將遙測資料傳送至 IoT 中樞，並接聽從 IoT 中樞送出的雲端到裝置訊息：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![執行模擬裝置應用程式][img-simulated-device]

2. 在 send-c2d-messages 資料夾中的命令提示字元，執行下列命令來傳送雲端到裝置訊息並等候意見反應通知︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![執行命令以傳送 c2d 訊息][img-send-command]

## 後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 套件]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]: media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[IoT 中心入門]: iot-hub-java-java-getstarted.md
[開始使用 IoT 中樞]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[IoT 中樞開發人員指南]: iot-hub-devguide.md
[Azure IoT 開發人員中樞]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 入口網站]: https://portal.azure.com
[Azure IoT 套件]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0727_2016-->