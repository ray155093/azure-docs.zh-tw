---
title: "將 Intel Edison (節點) 連接到 Azure IoT - 第 4 課：讓 LED 閃爍 | Microsoft Docs"
description: "自訂訊息以變更 LED 的開與關行為。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "使用 arduino 控制 led"
ms.assetid: 387cd97e-b05e-43c4-b252-f68ad45d524a
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 9a2c6dfe5d85a4da9714f9b8942cb2baf1a98466


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>變更 LED 的開與關行為
## <a name="what-you-will-do"></a>將執行的作業
自訂訊息以變更 LED 的開與關行為。 如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
使用其他函式變更 LED 的開與關行為。

## <a name="what-you-need"></a>您需要什麼
您必須已成功完成[在 Intel Edison 上執行範例應用程式以接收雲端到裝置訊息][receive-cloud-to-device-messages]。

## <a name="add-functions-to-appjs-and-gulpfilejs"></a>在 app.js 和 gulpfile.js 中新增函式
1. 執行下列命令，以在 Visual Studio Code 中開啟範例應用程式︰

   ```bash
   cd Lesson4
   code .
   ```
2. 開啟 `app.js` 檔案，然後在 blinkLED() 函式後新增下列函式：

   ```javascript
   function turnOnLED() {
     myLed.write(1);
   }

   function turnOffLED() {
     myLed.write(0);
   }
   ```

   ![具有所新增函式的 App.js 檔案](media/iot-hub-intel-edison-lessons/lesson4/updated_app_node.png)
3. 在 `receiveMessageCallback` 函式中 switch-case 區塊的 'blink' 案例前面新增下列條件：

   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
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

   ![具有所新增函式的 Gulpfile.js 檔案][gulpfile]
5. 在 `sendMessage` 函式中，將 `var message = buildMessage(sentMessageCount);` 這行取代為下列片段中所顯示的新行：

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 儲存所有變更。

### <a name="deploy-and-run-the-sample-application"></a>部署和執行範例應用程式
執行下列命令，在 Edison 上部署和執行範例應用程式：

```bash
gulp deploy && gulp run
```

您應該會看到 LED 開啟兩秒，然後再關閉兩秒。 最後一個 "stop" 訊息會停止執行範例應用程式。

![on 和 off][on-and-off]

恭喜！ 您已成功自訂從 IoT 中樞傳送至 Edison 的訊息。

### <a name="summary"></a>摘要
此選讀小節示範如何自訂訊息，讓範例應用程式可以使用不同的方式控制 LED 的開與關行為。

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_node.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_node.png



<!--HONumber=Jan17_HO4-->


