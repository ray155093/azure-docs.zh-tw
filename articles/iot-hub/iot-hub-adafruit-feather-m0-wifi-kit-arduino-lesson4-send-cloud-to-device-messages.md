---
title: "將 Arduino (C) 連接到 Azure IoT - 第 4 課：雲端到裝置 | Microsoft Docs"
description: "範例應用程式會在 Adafruit Feather M0 WiFi 上執行，並監視從 IoT 中樞傳入的訊息。 新的 Gulp 工作會從 IoT 中樞將訊息傳送到 Adafruit Feather M0 WiFi 來使 LED 閃爍。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "arduino 從 web 控制 led, arduino 透過 web 控制 led"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: a0bf53fb-29fb-485f-ba4a-6c715057b1a2
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f9feb665ac93028023df86889ca53a64ac2ec0e
ms.openlocfilehash: 63113841ca836681232e0aa43b15b444f8bb92e2
ms.contentlocale: zh-tw
ms.lasthandoff: 01/28/2017

---
<a id="run-a-sample-application-to-receive-cloud-to-device-messages" class="xliff"></a>

# 執行範例應用程式以接收雲端到裝置訊息
在本文中，您將在 Adafruit Feather M0 WiFi Arduino 面板上部署範例應用程式。

範例應用程式會監視來自 IoT 中樞的傳入訊息。 您也會在電腦上執行 Gulp 工作，以從 IoT 中樞將訊息傳送到 Arduino 面板。 當範例應用程式收到訊息時，便會使 LED 閃爍。 如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

<a id="what-you-will-do" class="xliff"></a>

## 將執行的作業
* 將範例應用程式連接到 IoT 中樞。
* 部署並執行範例應用程式。
* 從 IoT 中樞將訊息傳送到 Arduino 面板來使 LED 閃爍。

<a id="what-you-will-learn" class="xliff"></a>

## 學習目標
在本文中，您將了解：
* 如何監視來自 IoT 中樞的傳入訊息。
* 現在，您可以從 IoT 中樞將雲端到裝置訊息傳送至 Arduino 面板。

<a id="what-you-need" class="xliff"></a>

## 您需要什麼
* 已設定並可供使用的 Arduino 面板。 若要了解如何設定 Arduino 面板，請參閱[設定裝置][configure-your-device]。
* 在您的 Azure 訂用帳戶中建立的 IoT 中樞。 若要了解如何建立 IoT 中樞，請參閱[建立您的 Azure IoT 中樞][create-your-azure-iot-hub]。

<a id="connect-the-sample-application-to-your-iot-hub" class="xliff"></a>

## 將範例應用程式連接到 IoT 中樞

1. 請確定您已位於存放庫資料夾 `iot-hub-c-feather-m0-getting-started`。

   執行下列命令來在 Visual Studio Code 中開啟範例應用程式︰

   ```bash
   cd Lesson4
   code .
   ```

   請注意到 `app` 子資料夾中的 `app.ino` 檔案。 `app.ino` 檔案為關鍵的來源檔案，這個來源檔案包含監視來自 IoT 中樞之傳入訊息的程式碼。 `blinkLED` 函數會使 LED 閃爍。

   ![範例應用程式中的儲存機制結構][repo-structure]

2. 透過裝置探索 cli 取得裝置的序列連接埠︰

   ```bash
   devdisco list --usb
   ```

   您應該會看到類似下列的輸出，並找到 Arduino 面板的 usb COM 連接埠︰

   ![裝置探索][device-discovery]

3. 開啟課程資料夾中的 `config.json` 檔案並輸入找到的 COM 連接埠號碼值︰

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > 針對 COM 連接埠，它在 Windows 平台上的格式為 `COM1, COM2, ...`。 在 macOS 或 Ubuntu 上，它將以 `/dev/` 做為開頭。

4. 執行下列命令初始化組態檔：

   ```bash
   # For Windows command prompt
   npm install
   gulp init
   gulp install-tools
   ```

5. 在 `config-arduino.json` 檔案中進行下列取代：

   如果您已在此電腦上完成[建立 Azure 函數應用程式與儲存體帳戶][create-an-azure-function-app-and-storage-account]中的步驟，則會繼承所有組態，因此您可以略過部署和執行範例應用程式之工作的步驟。 如果您是在不同電腦上完成[建立 Azure 函數應用程式與儲存體帳戶][create-an-azure-function-app-and-storage-account]中的步驟，您必須取代 `config-arduino.json` 檔案中的預留位置。 `config-arduino.json` 檔案位於您主資料夾的子資料夾中。

   ![config-arduino.json 檔案的內容][config-arduino-json]

   * 以連線至網際網路的 Wi-Fi SSID 取代 **[Wi-Fi SSID]**。
   * 以 Wi-Fi 密碼取代 **[Wi-Fi password]**。 如果 Wi-Fi 不需要密碼，請移除字串。
   * 以透過執行 `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}` 命令所取得的裝置連接字串取代 **[IoT device connection string]**。
   * 以透過執行 `az iot hub show-connection-string --name {my hub name}` 命令所取得的 IoT 中樞連接字串取代 **[IoT hub connection string]**。

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## 部署和執行範例應用程式
執行下列命令，以在 Arduino 面板上部署和執行範例應用程式：

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

gulp 命令會將範例應用程式部署到 Arduino 面板。 然後，它會在 Arduino 面板上執行應用程式，並在主機電腦上執行個別的工作，以從 IoT 中樞將 20 個閃爍訊息傳送到 Arduino 面板。

在範例應用程式執行後，它便會開始接聽來自 IoT 中樞的訊息。 同時，Gulp 工作會從 IoT 中樞將數個「閃爍」訊息傳送到 Arduino 面板。 每當面板收到閃爍訊息時，範例應用程式便會呼叫 `blinkLED` 函數來使 LED 閃爍。

當 gulp 工作從 IoT 中樞將 20 個訊息傳送到 Arduino 面板時，LED 每兩秒應該會閃爍一次。 最後將會有一個「停止」訊息，讓應用程式停止執行。

![具有 gulp 命令和閃爍訊息的範例應用程式][sample-application]

<a id="summary" class="xliff"></a>

## 摘要
您已成功從 IoT 中樞將訊息傳送到 Arduino 面板來使 LED 閃爍。 下一個工作是「選讀：變更 LED 的開與關行為」。

<a id="next-steps" class="xliff"></a>

## 後續步驟
[變更 LED 的開與關行為][change-the-on-and-off-led-behavior]


<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/repo_structure_arduino.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[create-an-azure-function-app-and-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/config-arduino.png
[sample-application]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_blink_arduino.png
[change-the-on-and-off-led-behavior]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
