---
title: "將 Raspberry Pi (C) 連接到 Azure IoT - 第 4 課：修改應用程式 | Microsoft Docs"
description: "自訂訊息以變更 LED 的開與關行為。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "控制 raspberry pi 的 led, raspberry pi led 控制, raspberry pi 控制 led"
ms.assetid: 0201b8ed-d5e6-4445-9a4d-1305003d1eff
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: c1c999e40ecc92a1018067822be66f14b61a8c36
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>變更 LED 的開與關行為
## <a name="what-you-will-do"></a>將執行的作業
自訂訊息以變更 LED 的開與關行為。 如果您有任何問題，請在[疑難排解頁面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
使用其他 Node.js 函式以變更 LED 的開與關行為。

## <a name="what-you-need"></a>您需要什麼
您必須已成功完成[在 Raspberry Pi 上執行範例應用程式以接收雲端到裝置訊息](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md)。

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>在 main.c 和 gulpfile.js 中新增函式
1. 執行下列命令，以在 Visual Studio Code 中開啟範例應用程式︰

   ```bash
   cd Lesson4
   code .
   ```
2. 開啟 `main.c` 檔案，然後在 blinkLED() 函式後新增下列函式：

   ```c
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![已新增函式的 main.c 檔案](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_c.png)
3. 在 `receiveMessageCallback` 函式的 `if` 區塊中，於預覽條件前面新增下列條件：

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
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
   }
   ```

   ![具有所新增函式的 Gulpfile.js 檔案](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile_c.png)
5. 在 `sendMessage` 函式中，將 `var message = buildMessage(sentMessageCount);` 這行取代為下列片段中所顯示的新行：

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 儲存所有變更。

### <a name="deploy-and-run-the-sample-application"></a>部署和執行範例應用程式
執行下列命令，以在 Pi 上部署和執行範例應用程式：

```bash
gulp deploy && gulp run
```

您應該會看到 LED 開啟兩秒，然後再關閉兩秒。 最後一個 "stop" 訊息會停止執行範例應用程式。

![具有開啟和關閉訊息的範例應用程式](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off_c.png)

恭喜！ 您已成功自訂從 IoT 中樞傳送至 Pi 的訊息。

### <a name="summary"></a>摘要
此選讀小節示範如何自訂訊息，讓範例應用程式可以使用不同的方式控制 LED 的開與關行為。

