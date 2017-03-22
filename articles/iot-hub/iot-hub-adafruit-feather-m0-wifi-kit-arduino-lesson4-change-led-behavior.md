---
title: "將 Arduino (C) 連接到 Azure IoT - 第 4 課：修改應用程式 | Microsoft Docs"
description: "自訂訊息以變更 LED 的開啟與關閉行為。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "使用 arduino 控制 led"
ms.assetid: d7a25430-450e-43c4-a3ed-1eed995b8b7e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3585dfbac8816140c0a62931920aff1a6bf7d540
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>變更 LED 的開與關行為
## <a name="what-you-will-do"></a>將執行的作業
自訂訊息以變更 LED 的開與關行為。 如果您有任何問題，請在 Adafruit Feather M0 WiFi Arduino 面版的[疑難排解頁面](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
使用其他 Arduino 函式以變更 LED 的開與關行為。

## <a name="what-you-need"></a>您需要什麼
您必須已成功完成[在 Arduino 面板上執行範例應用程式以接收雲端到裝置訊息][receive-cloud-to-device-messages]。

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>在 main.c 和 gulpfile.js 中新增函式
1. 執行下列命令，以在 Visual Studio Code 中開啟範例應用程式︰

   ```bash
   cd Lesson4
   code .
   ```
2. 開啟 `app.ino` 檔案，然後在 blinkLED() 函式後新增下列函式：

   ```arduino
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![具有所新增函式的 app.ino 檔案][app-ino-file]
3. 在 `receiveMessageCallback` 函式的 `else if` 區塊前新增下列條件：

   ```arduino
   else if (strcmp((const char*)value, "\"on\"") == 0)
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\"") == 0)
   {
       turnOffLED();
   }
   ```

   現在，您已設定範例應用程式，透過訊息來回應其他指示。 "on" 指示會開啟 LED，"off" 指示則會關閉 LED。
4. 開啟 gulpfile.js 檔案，然後在 `sendMessage` 函式前面新增新的函式：

   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   };
   ```

   ![具有所新增函式的 Gulpfile.js 檔案][gulp-file-js]
5. 在 `sendMessage` 函式中，將 `var message = buildMessage(sentMessageCount);` 這行取代為下列片段中所顯示的新行：

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 儲存所有變更。

### <a name="deploy-and-run-the-sample-application"></a>部署和執行範例應用程式
執行下列命令，以在 Arduino 面板上部署和執行範例應用程式：

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

您應該會看到 LED 開啟兩秒，然後再關閉兩秒。 最後一個 "stop" 訊息會停止執行範例應用程式。

![on 和 off][on-and-off]

恭喜！ 您已成功自訂從 IoT 中樞傳送至 Arduino 面板的訊息。

### <a name="summary"></a>摘要
此選讀小節示範如何自訂訊息，讓範例應用程式可以使用不同的方式控制 LED 的開與關行為。

<!-- Images and links -->

[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[app-ino-file]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_app_ino.png
[gulp-file-js]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_gulpfile_js.png
[on-and-off]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_on_and_off_arduino.png
