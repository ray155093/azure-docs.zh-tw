<properties
	pageTitle="使用 IoT 中樞傳送雲端到裝置訊息 | Microsoft Azure"
	description="請遵循本教學課程，以了解如何搭配 Java 使用 Azure IoT 中樞傳送雲端到裝置的訊息。"
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# 教學課程：如何使用 IoT 中樞和 Node.js 傳送雲端到裝置訊息

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## 簡介

Azure IoT 中樞是一項完全受管理的服務，有助於讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。[IoT 中心入門]教學課程會示範如何建立 IoT 中心、在其中佈建裝置識別，以及編寫模擬的裝置，以傳送裝置到雲端的訊息。

本教學課程是以[開始使用 IoT 中樞]為基礎。這會說明如何：

- 從您的應用程式雲端後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。
- 接收裝置上的雲端到裝置訊息。
- 從您的應用程式雲端後端，要求確認收到從 IoT 中樞傳送到裝置的訊息 (*意見反應*)。

您可以在 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]中，找到有關雲端到裝置訊息的詳細資訊。

在本教學課程結尾，您會執行兩個 Node.js 主控台應用程式：

* **SimulatedDevice**，[IoT 中心入門]中建立之應用程式的修改版本，可連接到您的 IoT 中心，並接收雲端到裝置的訊息。
* **SendCloudToDeviceMessage**：會透過 IoT 中樞，將雲端到裝置訊息傳送到模擬裝置，然後接收其傳遞通知。

> [AZURE.NOTE] 「IoT 中樞」透過 Azure IoT 裝置 SDK 為許多裝置平台和語言 (包括 C、Java 及 Javascript) 提供 SDK 支援。如需有關如何將您的裝置與本教學課程中的程式碼連接 (通常是連接到「Azure IoT 中樞」) 的逐步指示，請參閱 [Azure IoT 開發人員中樞]。

若要完成此教學課程，您需要下列項目：

+ Node.js 0.12.x 版或更新版本。<br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。

+ 使用中的 Azure 帳戶。(如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。)

## 在模擬裝置上接收訊息

在本節中，您會修改在[開始使用 IoT 中樞]中建立的模擬裝置應用程式，以接收來自 IoT 中樞的雲端到裝置訊息。

1. 使用文字編輯器開啟 SimulatedDevice.js 檔案。

2. 修改 **connectCallback** 函式來處理從 IoT 中樞傳送的訊息。在此範例中，裝置永遠會叫用**完整**函式，目的是通知 IoT 中樞訊息已處理完畢。新版的 **connectCallback** 函式會看起來像這樣︰

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] 如果您使用 HTTP/1 而非 AMQP 做為傳輸，**DeviceClient** 執行個體不會經常檢查 IoT 中樞是否傳來訊息 (每隔 25 分鐘以上)。如需有關 AMQP 與 HTTP/1 支援間的差異，以及 IoT 中樞節流的詳細資訊，請參閱 [IoT 中樞開發人員指南][IoT Hub Developer Guide - C2D]。

## 傳送雲端到裝置訊息

在本節中，您會建立 Node.js 主控台應用程式，將雲端到裝置訊息傳送至模擬裝置應用程式。您需要您在[開始使用 IoT 中樞]教學課程中所新增裝置的裝置識別碼。您也需要 IoT 中樞的連接字串 (可在 [Azure 入口網站]中找到)。

1. 建立新的名為 **sendcloudtodevicemessage** 的空資料夾。在 **sendcloudtodevicemessage** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。接受所有預設值：

    ```
    npm init
    ```

2. 在 **sendcloudtodevicemessage** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iothub** 封裝：

    ```
    npm install azure-iothub --save
    ```

3. 使用文字編輯器在 **sendcloudtodevicemessage** 資料夾中建立新的 **SendCloudToDeviceMessage.js** 檔案。

4. 在 **SendCloudToDeviceMessage.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. 在 **SendCloudToDeviceMessage.js** 檔案中新增下列程式碼。將連接字串預留位置的值替換為您在[開始使用 IoT 中樞]教學課程中為 IoT 中樞所建立的連接字串。將目標裝置預留位置的值替換為您在[開始使用 IoT 中樞]教學課程中新增裝置的裝置識別碼：

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 加入下列函式來將作業結果列印至主控台︰

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 加入下列函式來將傳遞意見反應訊息列印至主控台︰

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. 加入下列程式碼，當裝置收到雲端到裝置訊息時，會將訊息傳送至您的裝置，並處理意見反應訊息︰

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. 儲存並關閉 **SendCloudToDeviceMessage.js** 檔案。

## 執行應用程式

現在您已經準備好執行應用程式。

1. 在 **simulateddevice** 資料夾中的命令提示字元，執行下列命令以開始將遙測傳送至 IoT 中樞，並接聽雲端到裝置訊息：

    ```
    node SimulatedDevice.js 
    ```

    ![執行模擬裝置應用程式][img-simulated-device]

2. 在 **sendcloudtodevicemessage** 資料夾中的命令提示字元，執行下列命令來傳送雲端到裝置訊息並等候通知的意見反應︰

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![執行應用程式以傳送 c2d 命令][img-send-command]

    > [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在生產環境程式碼中，您應該如 MSDN 文章[暫時性錯誤處理]所建議，實作重試原則 (例如指數型輪詢)。

## 後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 套件]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]: media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[IoT 中心入門]: iot-hub-node-node-getstarted.md
[開始使用 IoT 中樞]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[IoT 中樞開發人員指南]: iot-hub-devguide.md
[Azure IoT 開發人員中樞]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 入口網站]: https://portal.azure.com
[Azure IoT 套件]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0928_2016-->