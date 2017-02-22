---
title: "將 Arduino 連接到 Azure IoT - 第 1 課：部署應用程式 | Microsoft Docs"
description: "從 GitHub 複製範例 Arduino 應用程式，並執行 gulp 以將此應用程式部署至您的 Adafruit Feather M0 WiFi。 此範例應用程式會閃爍 GPIO"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino led 專案, arduino led 閃爍, arduino led 閃爍程式碼, arduino 閃爍程式, arduino 閃爍範例"
ms.assetid: b0a7d076-d580-4686-9f7d-c0712750b615
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a2058611cecb33eab04de9249ad73b1c4ec6c2a5


---
# <a name="create-and-deploy-the-blink-application"></a>建立並部署閃爍應用程式
## <a name="what-you-will-do"></a>將執行的作業
從 GitHub 複製範例 Arduino 應用程式，並使用 gulp 工具將範例應用程式部署至您的 Adafruit Feather M0 WiFi Arduino 面板。 範例應用程式會讓 GPIO #13 面板上的 LED 每兩秒閃爍一次。

如果您有任何問題，請在[疑難排解頁面][troubleshooting-page]尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
* 如何在 Arduino 面板上部署和執行範例應用程式。

## <a name="what-you-need"></a>您需要什麼
您必須已順利完成下列作業︰

* [設定裝置][configure-your-device]
* [取得工具][get-the-tools]

## <a name="open-the-sample-application"></a>開啟範例應用程式
若要開啟範例應用程式，請遵循下列步驟：

1. 執行下列命令，從 GitHub 複製範例儲存機制︰

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-feather-m0-getting-started.git
   ```
2. 執行下列命令來在 Visual Studio Code 中開啟範例應用程式︰

   ```bash
   cd iot-hub-c-feather-m0-getting-started
   cd Lesson1
   code .
   ```

   ![儲存機制結構][repo-structure]

`app` 子資料夾中的 `app.ino` 檔案是包含 LED 控制程式碼的關鍵來源檔案。

### <a name="install-application-dependencies"></a>安裝應用程式相依性
執行下列命令，安裝範例應用程式需要的程式庫和其他模組︰

```bash
npm install
```

## <a name="configure-the-device-connection"></a>設定裝置連線
若要設定裝置連線，請遵循下列步驟：

1. 透過裝置探索 cli 取得裝置的序列連接埠︰

   ```bash
   devdisco list --usb
   ```

   您應該會看到類似下列的輸出，並尋找 Arduino 面板的 usb COM 連接埠︰![裝置探索][device-discovery]

2. 開啟課程資料夾中的 `config.json` 檔案並新增找到的 COM 連接埠號碼值︰

   ```json
   {
       "device_port" : "COM1"
   }
   ```
   ![config.json][config-json]
   > [!NOTE]
   > 若為 COM 連接埠，其在 Windows 平台上的格式為 `COM1, COM2, ...`。 在 macOS 或 Ubuntu 上，它是以 `/dev/` 開頭。

## <a name="deploy-and-run-the-sample-application"></a>部署和執行範例應用程式
### <a name="install-the-required-tools-for-your-arduino-board"></a>安裝 Arduino 面板的必要工具

執行下列命令，為 Arduino 面板安裝 Azure IoT 中樞 SDK：

```bash
gulp install-tools
```

視您的網路連線而定，這項工作可能需要很長的時間才會完成。

> [!NOTE]
> 執行 gulp 工作時，請結束執行 Arduino IDE 執行個體︰`install-tools`、`run`。

### <a name="deploy-and-run-the-sample-app"></a>部署和執行範例應用程式
執行下列命令，部署和執行範例應用程式：

```bash
gulp run

# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

### <a name="verify-the-app-works"></a>確認應用程式可以運作
如果 LED 沒有閃爍，請參閱[疑難排解指南][troubleshooting-page]，裡面有常見問題的解決方案。

![LED 閃爍][led-blinking]

## <a name="summary"></a>摘要
您已安裝必要工具來使用 Arduino 面板，並已在 Arduino 面板上部署範例應用程式來讓 LED 閃爍。 現在，您可以建立、部署和執行另一個範例應用程式，將 Arduino 面板連接至 Azure IoT 中樞來傳送和接收訊息。

## <a name="next-steps"></a>後續步驟
[取得 Azure 工具][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting-page]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-blink-arduino-mac.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[led-blinking]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md


<!--HONumber=Jan17_HO4-->


