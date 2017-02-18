---
title: "將 Arduino (C) 連接到 Azure IoT - 第 3 課：執行範例 | Microsoft Docs"
description: "將範例應用程式部署至 Adafruit Feather M0 WiFi，並執行該應用程式以傳送訊息至 IoT 中樞並讓 LED 閃爍。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 雲端服務, arduino 傳送資料到雲端"
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 631a6677e4801b1c3475b9e8f2726219dd101132


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>執行範例應用程式以傳送裝置到雲端訊息
## <a name="what-you-will-do"></a>將執行的作業
本文將說明如何將範例應用程式部署至 Adafruit Feather M0 WiFi Arduino 面板，並執行該應用程式以傳送訊息至 IoT 中樞。

如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
您將了解如何使用 gulp 工具將範例 Arduino 應用程式部署在 Arduino 面板上並執行。

## <a name="what-you-need"></a>您需要什麼
* 開始這項工作之前，您必須先成功完成[建立 Azure 函式應用程式與儲存體帳戶以處理與儲存 IoT 中樞訊息][process-and-store-iot-hub-messages]。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>取得 IoT 中樞與裝置連接字串
裝置連接字串用於將 Arduino 面板連線至 IoT 中樞。 IoT 中樞連接字串用於將 IoT 中樞連線至在 IoT 中樞內代表 Arduino 面板的裝置識別。

* 執行下列 Azure CLI 命令，列出您的資源群組中的所有 IoT 中樞：

```bash
az iot hub list -g iot-sample --query [].name
```

如果您未變更值，請使用 `iot-sample` 作為 `{resource group name}` 的值。

* 執行下列 Azure CLI 命令取得 IoT 中樞連接字串：

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` 是您建立 IoT 中樞並登錄 Arduino 面板時所指定的名稱。

* 執行下列命令來取得裝置連接字串：

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

如果您未變更值，請使用 `mym0wifi` 作為 `{device id}` 的值。
## <a name="configure-the-device-connection"></a>設定裝置連線
若要設定裝置連線，請遵循下列步驟：

1. 透過裝置探索 cli 取得裝置的序列連接埠︰

   ```bash
   devdisco list --usb
   ```

   您應該會看到類似下列的輸出，並找到 Arduino 面板的 usb COM 連接埠︰

   ![裝置探索][device-discovery]

2. 開啟課程資料夾中的 `config.json` 檔案並新增找到的 COM 連接埠號碼值︰

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > 若為 COM 連接埠，其在 Windows 平台上的格式為 `COM1, COM2, ...`。 在 macOS 或 Ubuntu 上，它是以 `/dev/` 開頭。

3. 執行下列命令初始化組態檔：

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. 執行下列命令在 Visual Studio Code 中開啟裝置組態檔 `config-arduino.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config-arduino.json][config-arduino-json]

5. 在 `config-arduino.json` 檔案中進行下列取代：

   * 以連線至網際網路的 Wi-Fi SSID 取代 **[Wi-Fi SSID]**。
   * 以 Wi-Fi 密碼取代 **[Wi-Fi password]**。 如果 Wi-Fi 不需要密碼，請移除字串。
   * 以您取得的 `device connection string` 來取代 **[IoT device connection string]**。
   * 以您取得的 `iot hub connection string` 來取代 **[IoT hub connection string]**。

   > [!NOTE]
   > 您在本文中不需要 `azure_storage_connection_string`。 請讓它保持原狀。

## <a name="deploy-and-run-the-sample-application"></a>部署和執行範例應用程式
執行下列命令，以在 Arduino 面板上部署和執行範例應用程式：

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> 預設 gulp 工作會依序執行 `install-tools` 和 `run` 工作。 當您[部署了閃爍應用程式][deployed-the-blink-app]時，您需要個別執行這些工作。

## <a name="verify-that-the-sample-application-works"></a>確認範例應用程式可運作
您應該會看到 GPIO #0 面板上的 LED 每兩秒閃爍一次。 每次 LED 閃爍時，範例應用程式就會將訊息傳送至 IoT 中樞，並驗證該訊息已成功傳送至 IoT 中樞。 此外，IoT 中樞所收到的每則訊息都會列印在主控台視窗中。 在傳送 20 則訊息後，範例應用程式會自動終止。

![具有所傳送和接收之訊息的範例應用程式][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>摘要
您已在 Arduino 面板上部署新的閃爍範例應用程式，並執行該應用程式以將裝置到雲端訊息傳送至 IoT 中樞。 現在，您可以在訊息寫入儲存體帳戶時加以監視。

## <a name="next-steps"></a>後續步驟
[讀取保存在 Azure 儲存體中的訊息][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md


<!--HONumber=Jan17_HO4-->


