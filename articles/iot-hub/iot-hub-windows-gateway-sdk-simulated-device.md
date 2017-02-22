---
title: "使用 IoT 閘道 SDK 來模擬裝置 | Microsoft Docs"
description: "「Azure IoT 閘道 SDK」逐步解說，其中使用 Windows 來說明如何使用「Azure IoT 閘道 SDK」從模擬裝置傳送遙測。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: f5eeea933dbd5b63c6a8f2bd5b065a13286a4bae


---
# <a name="azure-iot-gateway-sdk--send-device-to-cloud-messages-with-a-simulated-device-app-using-windows"></a>Azure IoT 閘道 SDK – 使用 Windows 以模擬裝置應用程式傳送裝置到雲端訊息
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>建置並執行範例
開始之前，您必須：

* [設定開發環境][lnk-setupdevbox]以在 Windows 上使用 SDK。
* 於 Azure 訂用帳戶中[建立 IoT 中樞][lnk-create-hub]時，您將需要中樞名稱才能完成此逐步解說。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* 將兩個裝置加入 IoT 中樞中，並記下其識別碼和裝置金鑰。 您可以使用[裝置總管或 iothub-explorer][lnk-explorer-tools] 工具將您的裝置加入您在上一個步驟中建立的 IoT 中樞，並擷取其金鑰。

建置範例：

1. 開啟 [VS2015 的開發人員命令提示字元]  命令提示字元。
2. 瀏覽至 **azure-iot-gateway-sdk** 儲存機制本機複本中的根資料夾。
3. 執行 **tools\\build.cmd** 指令碼。 此指令碼會建立 Visual Studio 方案檔、建置方案，並執行測試。 您可以在 **azure-iot-gateway-sdk** 存放庫本機複本的 **build** 資料夾中找到此 Visual Studio 方案。

執行範例：

在文字編輯器中，開啟 **azure-iot-gateway-sdk** 存放庫本機複本中的 **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** 檔案。 這個檔案設定範例閘道中的模組︰

* **IoTHub** 模組會連接至您的 IoT 中樞。 您必須設定它，以將資料傳送至您的 IoT 中樞。 特別是將 **IoTHubName** 值設定為 IoT 中樞的名稱，並將 **IoTHubSuffix** 值設定為 **azure-devices.net**。 將 **Transport** 值設定為 "HTTP" 、"AMQP" 或 "MQTT" 其中一個。 請注意，目前只有 "HTTP" 會在所有裝置訊息共用一個 TCP 連線。 如果您設定為 "AMQP" 或 "MQTT" 值時，閘道會維持每個裝置有一個連至 IoT 中樞的個別 TCP 連線。
* **mapping** 模組會將您模擬裝置的 MAC 位址對應到您 IoT 中樞裝置識別碼。 請確定 **deviceId** 值符合您新增至 IoT 中樞之兩個裝置的識別碼，而且 **deviceKey** 值包含這兩個裝置的金鑰。
* **BLE1** 和 **BLE2** 模組是模擬裝置。 請注意，其 MAC 位址如何符合 **mapping** 模組中的 MAC 位址。
* **Logger** 模組會將閘道活動記錄到檔案。
* 下面顯示的 **module path** 值假設您已將「IoT 閘道 SDK」儲存機制複製到 **C:** 磁碟機的根目錄。 如果您已將它下載到另一個位置，則需要據此調整「模組路徑」  值。
* JSON 檔案底部的 **links** 陣列會將 **BLE1** 和 **BLE2** 模組連接至 **mapping** 模組，再將 **mapping** 模組連接至 **IoTHub** 模組。 它也可確保 **Logger** 模組會記錄所有訊息。

```
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01"
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02"
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

儲存您對組態檔進行的任何變更。

執行範例：

1. 在命令提示字元中，瀏覽至 **azure-iot-gateway-sdk** 儲存機制本機複本中的根資料夾。
2. 執行以下命令：
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. 您可以使用[裝置總管或 iothub-explorer][lnk-explorer-tools] 工具監視 IoT 中樞接收自閘道的訊息。

## <a name="next-steps"></a>後續步驟
如果您想要更進一步了解「IoT 閘道 SDK」並實驗一些程式碼範例，請瀏覽下列開發人員教學課程和資源：

* [使用 IoT 閘道 SDK 從實體裝置傳送裝置到雲端訊息][lnk-physical-device]
* [Azure IoT 閘道 SDK][lnk-gateway-sdk]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [開發人員指南][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


