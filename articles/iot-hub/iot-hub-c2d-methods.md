<properties
 pageTitle="使用直接方法 |Microsoft Azure"
 description="本教學課程說明如何使用直接方法"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>


# <a name="tutorial:-use-direct-methods"></a>教學課程：使用直接方法

## <a name="introduction"></a>簡介

Azure IoT 中樞是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。 先前的教學課程 ([IoT 中樞入門]和[使用 IoT 中樞傳送雲端到裝置訊息]) 說明如何使用 IoT 中樞的裝置到雲端和雲端到裝置的基本傳訊功能。 IoT 中樞也能讓您從雲端的裝置上叫用非持續性方法。 方法代表與裝置的要求-回覆互動，類似於 HTTP 呼叫，因為會立即成功或失敗 (在使用者指定的逾時之後)，讓使用者知道呼叫的狀態。 [裝置上叫用直接方法][lnk-devguide-methods] 描述更詳細的方法，並提供關於使用方法與雲端-裝置訊息的指導方針。

本教學課程說明如何：

- 使用 Azure 入口網站來建立 IoT 中樞，並且在 IoT 中樞建立裝置身分識別。
- 建立模擬裝置，具有可以由雲端呼叫的直接方法。
- 建立主控台應用程式，會透過您的 IoT 中樞在模擬裝置上呼叫直接方法。

> [AZURE.NOTE] 目前，直接方法只能從使用 MQTT 通訊協定連接至 IoT 中樞的裝置存取。 請參閱 [MQTT 支援][lnk-devguide-mqtt] 文章，以取得如何將現有裝置應用程式轉換為使用 MQTT 的指示。

在本教學課程結尾處，您會有兩個 Node.js 主控台應用程式：

* **CallMethodOnDevice.js**，會在模擬裝置上呼叫方法，並顯示回應。
* **SimulatedDevice.js**，會使用先前建立的裝置身分識別連接到您的 IoT 中樞，並且回應雲端呼叫的方法。

> [AZURE.NOTE] 文章 [IoT 中樞 SDK][lnk-hub-sdks] 提供可讓您可以用來建置兩個應用程式，以在裝置和您的方案後端上執行的各種 SDK 的相關資訊。

若要完成此教學課程，您需要下列項目：

+ Node.js 0.10.x 版或更新版本。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您建立 Node.js 主控台應用程式，回應雲端所呼叫的方法。

1. 建立稱為 **simulateddevice**的新的空資料夾。 在 **simulateddevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：

    ```
    npm init
    ```

2. 在 **simulateddevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device** 裝置 SDK 套件以及 **azure-iot-device-mqtt** 套件：

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器，在 **simulateddevice** 資料夾中建立新的 **SimulatedDevice.js** 檔案。

4. 在 **SimulatedDevice.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. 新增 **connectionString** 變數，並用它來建立裝置用戶端。 將 **{裝置連接字串}** 取代為您在*建立裝置身分識別*章節中產生的連接字串：

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. 新增下列函式，在裝置上實作方法︰

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. 開啟您 IoT 中樞的連線，並開始初始化方法接聽程式︰

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. 儲存並關閉 **SimulatedDevice.js** 檔案。

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如 MSDN 文章 [暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如連接重試)。

## <a name="call-a-method-on-a-device"></a>在裝置上呼叫方法

在本節中，您會建立 Node.js 主控台應用程式，在模擬裝置上呼叫方法，然後顯示回應。

1. 建立稱為 **callmethodondevice**的新的空資料夾。 在 **callmethodondevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：

    ```
    npm init
    ```

2. 在 **callmethodondevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iothub** 套件：

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. 使用文字編輯器，在 **callmethodondevice** 資料夾中建立 **CallMethodOnDevice.js** 檔案。

4. 在 **CallMethodOnDevice.js** 檔案開頭新增下列 `require` 陳述式：

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. 新增下列變數宣告，並將預留位置值替換為您的 IoT 中樞的連接字串：

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. 建立用戶端以開啟到您的 IoT 中樞的連接。

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. 新增下列函式來叫用裝置方法，並列印對主控台的裝置回應︰

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. 儲存並關閉 **CallMethodOnDevice.js** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在 **simulateddevice** 資料夾的命令提示字元中，執行下列命令以開始接聽來自您的 IoT 中樞的方法呼叫：

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. 在 **callmethodondevice** 資料夾的命令提示字元中，執行下列命令以開始監視 IoT 中樞：

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. 您會看到裝置對方法的反應，方法是列印訊息和應用程式，它們會呼叫方法並且顯示來自裝置的回應：

    ![][9]
    
## <a name="next-steps"></a>後續步驟

在本教學課程中，您在入口網站中設定了新的 IoT 中樞，然後在中樞的身分識別登錄中建立了裝置識別。 您會將此裝置識別用於啟用模擬的裝置應用程式，以將雲端所叫用的方法進行反應。 您也會建立應用程式，在裝置上叫用方法，並且顯示來自裝置的回應。 

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

- [開始使用 IoT 中樞]
- [排程多個裝置上的作業][lnk-devguide-jobs]

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs] 教學課程。

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[使用 IoT 中樞傳送雲端到裝置訊息]: iot-hub-csharp-csharp-c2d.md
[處理裝置到雲端訊息]: iot-hub-csharp-csharp-process-d2c.md
[IoT 中心入門]: iot-hub-node-node-getstarted.md



<!--HONumber=Oct16_HO2-->


