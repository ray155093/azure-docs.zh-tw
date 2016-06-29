<properties
	pageTitle="適用於 Node.js 的 Azure IoT 中樞快速入門 | Microsoft Azure"
	description="採用 Node.js 的 Azure IoT 中樞快速入門教學課程。使用 Azure IoT 中樞與 Node.js 搭配 Microsoft Azure IoT SDK 來實作物聯網解決方案。"
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/16/2016"
     ms.author="dobett"/>

# 開始使用適用於 Node.js 的 Azure IoT 中樞

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教學課程結尾處，您將會有三個 Node.js 主控台應用程式：

* **CreateDeviceIdentity.js**，這會建立裝置身分識別和相關聯的安全性金鑰，來連線您的模擬裝置。
* **ReadDEviceToCloudMessages.js**，其中顯示模擬的裝置所傳送的遙測。
* **SimulatedDevice.js**，這會使用先前建立的裝置身分識別連接到您的 IoT 中樞，並使用 AMQPS 通訊協定每秒傳送遙測訊息。

> [AZURE.NOTE] 文章 [IoT 中樞 SDK][lnk-hub-sdks] 提供可讓您可以用來建置兩個應用程式，以在裝置和您的方案後端上執行的各種 SDK 的相關資訊。

若要完成此教學課程，您需要下列項目：

+ Node.js 0.12.x 版或更新版本。<br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。

+ 使用中的 Azure 帳戶。(如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

您現在已經建立 IoT 中樞。您具有完成本教學課程的其餘部分所需的 IoT 中樞主機名稱和連接字串。

## 建立裝置識別

在本節中，您將建立 Node.js 主控台應用程式，它會在 IoT 中樞的身分識別登錄中建立新的裝置身分識別。裝置無法連線到 IoT 中樞，除非它在裝置身分識別登錄中具有項目。如需詳細資訊，請參閱 [IoT 中心開發人員指南][lnk-devguide-identity]的**裝置識別登錄**一節。執行這個主控台應用程式時，它會產生唯一的裝置識別碼及金鑰，當裝置向 IoT 中樞傳送裝置對雲端訊息時，可以用來識別裝置本身。

1. 建立稱為 **createdeviceidentity** 的新的空資料夾。在 **createdeviceidentity** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。接受所有預設值：

    ```
    npm init
    ```

2. 在 **createdeviceidentity** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iothub** 封裝：

    ```
    npm install azure-iothub --save
    ```

3. 使用文字編輯器，在 **createdeviceidentity** 資料夾中建立新的 **CreateDeviceIdentity.js** 檔案。

4. 在 **CreateDeviceIdentity.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. 將下列程式碼加入至 **CreateDeviceIdentity.js** 檔案，並將預留位置的值替換為您在上一節中為 IoT 中樞所建立的連接字串：

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. 加入下列程式碼以在 IoT 中樞的裝置身分識別登錄建立新的裝置定義。如果登錄中沒有該裝置識別碼，此程式碼會建立新的裝置，否則會傳回現有裝置的金鑰：

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. 儲存並關閉 **CreateDeviceIdentity.js** 檔案。

8. 若要執行 **createdeviceidentity** 應用程式，請在命令提示字元中於 createdeviceidentity 資料夾內執行下列命令：

    ```
    node CreateDeviceIdentity.js 
    ```

9. 記下**裝置識別碼**和**裝置金鑰**。稍後在建立連線至做為裝置之 IoT 中樞的應用程式時，會需要這些資料。

> [AZURE.NOTE] IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對中樞的安全存取。它會儲存裝置識別碼和金鑰來做為安全性認證，以及啟用或停用旗標，讓您用來停用個別裝置的存取。如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]。

## 接收裝置到雲端的訊息

在本節中，您將建立 Node.js 主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。IoT 中樞會公開與[事件中樞][lnk-event-hubs-overview]相容的端點以讓您讀取裝置到雲端訊息。為了簡單起見，本教學課程會建立的基本讀取器不適合用於高輸送量部署。[處理裝置到雲端的訊息][lnk-process-d2c-tutorial]教學課程會說明如何大規模處理裝置到雲端的訊息。[開始使用事件中樞][lnk-eventhubs-tutorial]教學課程則會提供進一步資訊，說明如何處理來自事件中樞的訊息，而且此教學課程也適用於 IoT 中樞事件中樞相容端點。

> [AZURE.NOTE] 用於讀取裝置到雲端訊息的事件中樞相容端點一律會使用 AMQPS 通訊協定。

1. 建立稱為 **readdevicetocloudmessages** 的新的空資料夾。在 **readdevicetocloudmessages** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。接受所有預設值：

    ```
    npm init
    ```

2. 在 **readdevicetocloudmessages** 資料夾的命令提示字元中，執行下列命令以安裝 **azure-event-hubs** 封裝：

    ```
    npm install azure-event-hubs --save
    ```

3. 使用文字編輯器，在 **readdevicetocloudmessages** 資料夾中建立新的 **ReadDeviceToCloudMessages.js** 檔案。

4. 在 **ReadDeviceToCloudMessages.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. 新增下列變數宣告，並將預留位置值替換為您的 IoT 中樞的連接字串：

    ```
    var connectionString = '{iothub connection string}';
    ```

6. 新增下列兩個會將輸出列印到主控台的函數：

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. 加入下列程式碼以建立 **EventHubClient**、開啟您的 IoT 中樞的連線，並建立每個資料分割的接收者。在建立開始執行後只會讀取傳送到 IoT 中樞之訊息的收件者時，此應用程式會使用篩選器。這很適合測試環境，因此您就可以看到目前的訊息集，但在生產環境中，您的程式碼應該要確定它能處理所有訊息，如需詳細資訊，請參閱[如何處理 IoT 中樞裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程：

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. 儲存並關閉 **ReadDeviceToCloudMessages.js** 檔案。

## 建立模擬裝置應用程式

在本節中，您會撰寫 Node.js 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中樞。

1. 建立稱為 **simulateddevice** 的新的空資料夾。在 **simulateddevice** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。接受所有預設值：

    ```
    npm init
    ```

2. 在 **simulateddevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device-amqp** 封裝：

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. 使用文字編輯器，在 **simulateddevice** 資料夾中建立新的 **SimulatedDevice.js** 檔案。

4. 在 **SimulatedDevice.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. 新增 **connectionString** 變數，並用它來建立裝置用戶端。將 **{youriothubname}** 取代為 IoT 中樞名稱，並將 **{yourdeviceid}** 和 **{yourdevicekey}** 取代為您在「建立裝置身分識別」一節中產生的裝置值：

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. 新增下列函數來顯示應用程式的輸出：

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 建立回呼，並使用 **setInterval** 函數每秒將新訊息傳送至 IoT 中樞：

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. 開啟您 IoT 中樞的連線，並開始傳送訊息︰

    ```
    client.open(connectCallback);
    ```

9. 儲存並關閉 **SimulatedDevice.js** 檔案。

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。


## 執行應用程式

現在您已經準備好執行應用程式。

1. 在 **readdevicetocloudmessages** 資料夾的命令提示字元中，執行下列命令以開始監視 IoT 中樞：

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. 在 **simulateddevice** 資料夾的命令提示字元中，執行下列命令以開始將遙測資料傳送至 IoT 中樞：

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. [Azure 入口網站][lnk-portal]中的 [使用量] 圖格會顯示傳送至中樞的訊息數目︰

    ![][43]

## 後續步驟

在本教學課程中，您在入口網站中設定了新的 IoT 中樞，然後在中樞的身分識別登錄中建立了裝置身分識別。您會將此裝置身分識別用於啟用模擬的裝置應用程式，以將裝置對雲端訊息傳送至中樞，並建立一個應用程式來顯示中樞所接收的訊息。您可以利用下列教學課程繼續探索 IoT 中樞功能和其他 IoT 案例：

- [使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d-tutorial]，示範如何將訊息傳送到裝置，並處理 IoT 中樞所產生的傳送意見反應。
- [處理裝置到雲端訊息][lnk-process-d2c-tutorial]，示範如何可靠地處理來自裝置的遙測和互動式訊息。
- [從裝置上傳檔案][lnk-upload-tutorial]說明使用雲端到裝置訊息來幫助從裝置上傳檔案的模式。

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0622_2016-->