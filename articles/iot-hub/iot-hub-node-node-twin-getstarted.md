<properties
    pageTitle="開始使用對應項 | Microsoft Azure"
    description="本教學課程說明如何使用對應項"
    services="iot-hub"
    documentationCenter="node"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>


# <a name="tutorial:-get-started-with-device-twins-(preview)"></a>教學課程：開始使用裝置對應項 (預覽)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程結束時，您將會有兩個 Node.js 主控台應用程式：

* **AddTagsAndQuery.js**，這是應該從後端執行的 Node.js 應用程式，可新增標籤並查詢裝置對應項。
* **TwinSimulatedDevice.js**，這是會模擬裝置的 Node.js 應用程式，此裝置會以稍早建立的身分識別連接到您的 IoT 中樞，並報告其連線狀況。

> [AZURE.NOTE] [IoT 中樞 SDK][lnk-hub-sdks] 一文提供可用來建置裝置和後端應用程式的各種 SDK 的相關資訊。

若要完成此教學課程，您需要下列項目：

+ Node.js 0.10.x 版或更新版本。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在本節中，您將建立一個 Node.js 主控台應用程式，此應用程式會將位置中繼資料新增至與 **myDeviceId** 相關聯的對應項。 接著，它會選取位於美國的裝置來查詢儲存在中樞的對應項，再查詢會報告行動電話連線的對應項。

1. 建立稱為 **addtagsandqueryapp** 的新空白資料夾。 在 **addtagsandqueryapp** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。 接受所有預設值：

    ```
    npm init
    ```

2. 在命令提示字元下，於 **addtagsandqueryapp** 資料夾中執行下列命令以安裝 **azure-iothub** 套件：

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. 使用文字編輯器，在 **addtagsandqueryapp** 資料夾中建立新的 **AddTagsAndQuery.js** 檔案。

4. 將下列程式碼新增至 **AddTagsAndQuery.js** 檔案，並以您建立中樞時所複製的連接字串，取代 **{service connection string}** 預留位置︰

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    **Registry** 物件會公開從服務來與裝置對應項進行互動時所需的所有方法。 先前的程式碼會先初始化 **Registry** 物件，然後擷取 **myDeviceId** 的對應項，最後會以所需的位置資訊來更新其標籤。

    更新標籤之後，它會呼叫 **queryTwins** 函式。

7. 在 **AddTagsAndQuery.js** 結尾處新增下列程式碼來實作 **queryTwins** 函式︰

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    先前的程式碼會執行兩個查詢︰第一個只選取位於 **Redmond43** 工廠的裝置的裝置對應項，第二個會修改查詢，只選取也透過行動電話網路來連接的裝置。

    請注意，先前的程式碼在建立 **查詢** 物件時，指定傳回的最大文件數。 **query** 物件包含 **hasMoreResults** 布林值屬性，可用來多次叫用 **nextAsTwin** 方法以擷取所有結果。 有一個稱為 **next** 的方法適用於不是裝置對應項的結果，例如彙總查詢的結果。

8. 使用下列命令執行應用程式：

        node AddTagsAndQuery.js

    如果是查詢所有位於 **Redmond43** 中的裝置，您在結果中會看到一個裝置，而如果查詢將結果限於使用行動電話網路的裝置，則您不會看到任何裝置。

    ![][1]

在下一節，您將建立一個裝置應用程式，以報告連線資訊並變更上一節的查詢結果。

## <a name="create-the-device-app"></a>建立裝置應用程式

在本節中，您將建立一個 Node.js 主控台應用程式，此應用程式會以 **myDeviceId** 來連接到您的中樞，然後更新其對應項所報告的屬性，以包含資訊來指出目前使用行動電話網路來連線。

> [AZURE.NOTE] 目前，裝置對應項只能從使用 MQTT 通訊協定連線至 IoT 中樞的裝置存取。 請參閱 [MQTT 支援][lnk-devguide-mqtt] 文章，以取得如何將現有裝置應用程式轉換為使用 MQTT 的指示。

1. 建立稱為 **reportconnectivity** 的新空白資料夾。 在 **reportconnectivity** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。 接受所有預設值：

    ```
    npm init
    ```

2. 在 **reportconnectivity** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device** 和 **azure-iot-device-mqtt** 套件：

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器，在 **reportconnectivity** 資料夾中建立新的 **ReportConnectivity.js** 檔案。

4. 將下列程式碼新增至 **ReportConnectivity.js** 檔案，並以您建立 **myDeviceId** 裝置身分識別時所複製的連接字串，取代 **{device connection string}** 預留位置︰

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 先前的程式碼 (在它初始化 **Client** 物件之後) 會擷取 **myDeviceId** 的對應項，並以連線資訊來更新它所報告的屬性。

5. 執行裝置應用程式

        node ReportConnectivity.js

    您應該會看見訊息 `twin state reported`。

6. 現在，裝置已回報其連線資訊，它應該會出現在這兩個查詢中。 回到 **addtagsandqueryapp** 資料夾，並再次執行查詢︰

        node AddTagsAndQuery.js

    這次，**myDeviceId** 應該會出現在這兩個查詢結果中。

    ![][3]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您在入口網站中設定了新的 IoT 中樞，然後在中樞的身分識別登錄中建立了裝置識別。 您已從後端應用程式將裝置中繼資料新增為標籤，並撰寫模擬裝置應用程式來報告裝置對應項中的裝置連線資訊。 您也學到如何使用 IoT 中樞類似 SQL 查詢語言來查詢此資訊。

使用下列資源來了解如何：

- 從裝置傳送遙測，請參閱[開始使用 IoT 中樞][lnk-iothub-getstarted]教學課程，
- 使用對應項的所需屬性來設定裝置，請參閱[使用所需的屬性來設定裝置][lnk-twin-how-to-configure]教學課程，
- 以互動方式控制裝置 (例如，從使用者控制的應用程式開啟風扇)，請參閱[使用直接方法][lnk-methods-tutorial]教學課程。

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md


<!--HONumber=Oct16_HO2-->


