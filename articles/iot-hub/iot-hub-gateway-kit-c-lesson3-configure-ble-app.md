---
title: "執行範例應用程式來接收 SensorTag 資料並傳送至 Azure IoT 中樞 | Microsoft Docs"
description: "執行 BLE 範例應用程式，從 SensorTag 和您的 IoT 中樞接收資料。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "ble 應用程式, 感應器監視應用程式, 感應器資料收集, 來自感應器的資料, 送至雲端的感應器資料"
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: fab1eb0dc765ced95e3bec7c12c7dfa0ca91bb19


---
# <a name="configure-and-run-a-ble-sample-application"></a>設定和執行 BLE 範例應用程式

## <a name="what-you-will-do"></a>將執行的作業

- 複製範例存放庫。 
- 設定 SensorTag 與 Intel NUC 之間的連線。 
- 使用 Azure CLI 取得 IoT 中樞和 SensorTag 的 BLE (Bluetooth Low Energy) 範例應用程式的資訊。 然後設定並執行 BLE 範例應用程式。 

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標

在本文中，您將了解：

- 如何設定和執行 BLE 範例應用程式。

## <a name="what-you-need"></a>您需要什麼

您必須已成功完成

- [建立 IoT 中樞並登錄裝置](iot-hub-gateway-kit-c-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>將範例存放庫複製至主機電腦

若要複製範例存放庫，在主機電腦上遵循下列步驟︰

1. 在 Windows 上開啟命令提示字元視窗，或在 macOS 或 Ubuntu 上開啟終端機。
2. 執行以下命令：

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="set-up-the-connectivity-between-sensortag-and-intel-nuc"></a>設定 SensorTag 與 Intel NUC 之間的連線

若要設定連線，在主機電腦上遵循下列步驟︰

1. 執行下列命令初始化組態檔：

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. 執行下列命令在 Visual Studio Code 中開啟 `config-gateway.json`︰

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. 找到下列這行程式碼，並將 `[device hostname or IP address]` 取代為 Intel NUC 的 IP 位址或主機名稱。
   ![設定閘道器的螢幕擷取畫面](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. 執行下列命令，在 Intel NUC 上安裝協助程式工具︰

   ```bash
   gulp install-tools
   ```

5. 按下電源按鈕 (如下圖) 開啟 SensorTag，綠色 LED 應該會閃爍。

   ![開啟 SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. 執行下列命令來掃描 SensorTag 裝置：

   ```bash
   gulp discover-sensortag
   ```

7. 執行下列命令測試 SensorTag 和 Intel NUC 之間的連線︰

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   將 `{mac address}` 取代為您在先前步驟取得的 MAC 位址。

## <a name="get-the-connection-string-of-sensortag"></a>取得 SensorTag 的連接字串

若要取得 SensorTag 的 Azure IoT 中樞連接字串，在主機電腦上執行下列命令︰

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` 是您所使用的 IoT 中樞名稱。 如果您在第 2 課沒有變更值，使用 iot-gateway 作為 `{resource group name}` 的值，使用 mydevice 作為 `{device id}` 的值。

## <a name="configure-the-ble-sample-application"></a>設定 BLE 範例應用程式

若要設定及執行 BLE 範例應用程式，在主機電腦上遵循下列步驟︰

1. 執行下列命令在 Visual Studio Code 中開啟 `config-sensortag.json`︰

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![設定 sensortag 的螢幕擷取畫面](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. 取代程式碼中的下列內容：
   - 將 `[IoT hub name]` 取代為您使用的 IoT 中樞名稱。
   - 將 `[IoT device connection string]` 取代為您取得的 SensorTag 連接字串。
   - 將 `[device_mac_address]` 取代為您取得的 MAC 位址。

3. 執行 BLE 範例應用程式。

   若要執行 BLE 範例應用程式，在主機電腦上遵循下列步驟︰

   1. 開啟 SensorTag。

   2. 執行下列命令在 Intel NUC 上部署及執行 BLE 範例應用程式：
   
      ```bash
      gulp run
      ```

## <a name="verify-that-the-ble-sample-application-works"></a>確認 BLE 範例應用程式可運作

您現在應該會看到如下的輸出：

![BLE 範例應用程式的輸出](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

範例應用程式會收集溫度資料，並傳送至您的 IoT 中樞。 範例應用程式會在傳送 40 則訊息後自動終止。

## <a name="summary"></a>摘要

您已成功設定 SensorTag 和 Intel NUC 之間的連線，並且執行 BLE 範例應用程式從 SensorTag 收集資料並將資料傳送至您的 IoT 中樞。 您可以開始了解如何確認 IoT 中樞是否已收到資料。

## <a name="next-steps"></a>後續步驟
[讀取來自 IoT 中樞的傳入訊息](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)


<!--HONumber=Dec16_HO3-->


