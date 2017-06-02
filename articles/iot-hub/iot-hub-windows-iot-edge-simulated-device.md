---
title: "使用 Azure IoT Edge 來模擬裝置 (Windows) | Microsoft Docs"
description: "如何在 Windows 上使用 Azure IoT Edge 來建立模擬裝置，以透過 Azure IoT Edge 閘道將遙測資料傳送至 IoT 中樞。"
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
ms.date: 05/18/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 21cc2f3575a1e93ffd3b245371069f1498f6b63b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/18/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>使用 Azure IoT Edge 搭配模擬裝置來傳送裝置到雲端訊息 (Windows)
[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>建置並執行範例
開始之前，您必須：

* [設定開發環境][lnk-setupdevbox]以在 Windows 上使用 SDK。
* 於 Azure 訂用帳戶中[建立 IoT 中樞][lnk-create-hub]時，您需要中樞名稱才能完成此逐步解說。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* 將兩個裝置加入 IoT 中樞中，並記下其識別碼和裝置金鑰。 您可以使用[裝置總管][lnk-device-explorer]或 [iothub-explorer][lnk-iothub-explorer] 工具，將您的裝置新增到在上一個步驟中建立的 IoT 中樞並擷取其金鑰。

建置範例：

1. 開啟「VS 2015 開發人員命令提示字元」或「VS 2017 開發人員命令提示字元」命令提示字元。
2. 瀏覽至 **iot-edge** 存放庫的本機複本中的根資料夾。
3. 執行 **tools\\build.cmd** 指令碼。 此指令碼會建立 Visual Studio 方案檔及建置方案。 您可以在 **iot-edge** 存放庫本機複本的 **build** 資料夾中找到此 Visual Studio 方案。 您可以為指令碼指定其他參數，以建置並執行單元測試和端對端測試。 這些參數分別是 **--run-unittests** 和 **--run-e2e-tests**。

執行範例：

在文字編輯器中，開啟 **iot-edge** 存放庫本機複本中的 **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** 檔案。 這個檔案會設定閘道範例中的 IoT Edge 模組︰

* **IoTHub** 模組會連接至您的 IoT 中樞。 您必須設定它，以將資料傳送至您的 IoT 中樞。 特別是將 **IoTHubName** 值設定為 IoT 中樞的名稱，並將 **IoTHubSuffix** 值設定為 **azure-devices.net**。 將 **Transport** 值設定為 "HTTP"、"AMQP" 或 "MQTT" 其中一個。 目前只有 "HTTP" 會為所有裝置訊息共用一個 TCP 連線。 如果您將值設定為 "AMQP" 或 "MQTT"，閘道會為每個裝置維持一個連至 IoT 中樞的個別 TCP 連線。
* **mapping** 模組會將您模擬裝置的 MAC 位址對應到您 IoT 中樞裝置識別碼。 請確定 **deviceId** 值符合您新增至 IoT 中樞之兩個裝置的識別碼，而且 **deviceKey** 值包含這兩個裝置的金鑰。
* **BLE1** 和 **BLE2** 模組是模擬裝置。 請注意模組 MAC 位址如何符合 **mapping** 模組中的 MAC 位址。
* **Logger** 模組會將閘道活動記錄到檔案。
* 下列範例中顯示的「模組路徑」值假設您已將 IoT Edge 存放庫複製到 **C:** 磁碟機的根目錄。 如果您已將它下載到另一個位置，則需要據此調整「模組路徑」  值。
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

1. 在命令提示字元中，瀏覽至 **iot-edge** 存放庫本機複本的根資料夾。
2. 執行以下命令：
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. 您可以使用[裝置總管][lnk-device-explorer]或 [iothub-explorer][lnk-iothub-explorer] 工具，以監視 IoT 中樞從閘道接收的訊息。

## <a name="next-steps"></a>後續步驟
如果您想要更進一步了解 IoT Edge 並實驗一些程式碼範例，請瀏覽下列開發人員教學課程和資源：

* [使用 IoT Edge 從實體裝置傳送裝置到雲端訊息][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [徹底保護您的 IoT 解決方案][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-iot-edge]: https://github.com/Azure/iot-edge/

[lnk-physical-device]: iot-hub-iot-edge-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md
