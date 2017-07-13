---
title: "將 Intel Edison (C) 連接到 Azure IoT - 第 4 課：讓 LED 閃爍 | Microsoft Docs"
description: "自訂訊息以變更 LED 的開與關行為。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "使用 arduino 控制 led"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 9826c55a-0e24-4296-ae54-29b7fe66436a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 278bdf74e2fa8f7074bb8f5ed8eae2d47402b299
ms.contentlocale: zh-tw
ms.lasthandoff: 01/25/2017

---
<a id="change-the-on-and-off-behavior-of-the-led" class="xliff"></a>

# 變更 LED 的開與關行為
<a id="what-you-will-do" class="xliff"></a>

## 將執行的作業
自訂訊息以變更 LED 的開與關行為。 如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

<a id="what-you-will-learn" class="xliff"></a>

## 學習目標
使用其他函式變更 LED 的開與關行為。

<a id="what-you-need" class="xliff"></a>

## 您需要什麼
您必須已成功完成[在 Intel Edison 上執行範例應用程式以接收雲端到裝置訊息][receive-cloud-to-device-messages]。

<a id="add-functions-to-mainc-and-gulpfilejs" class="xliff"></a>

## 在 main.c 和 gulpfile.js 中新增函式
1. 執行下列命令，以在 Visual Studio Code 中開啟範例應用程式︰

   ```bash
   cd Lesson4
   code .
   ```
2. 開啟 `main.c` 檔案，然後在 blinkLED() 函式後新增下列函式：

   ```c
   static void turnOnLED()
   {
     mraa_gpio_write(context, 1);
   }

   static void turnOffLED()
   {
     mraa_gpio_write(context, 0);
   }
   ```

   ![已新增函式的 main.c 檔案](media/iot-hub-intel-edison-lessons/lesson4/updated_app_c.png)

3. 在 `receiveMessageCallback` 函式的 `else if` 區塊前新增下列條件：

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

   ![具有所新增函式的 Gulpfile.js 檔案][gulpfile]
5. 在 `sendMessage` 函式中，將 `var message = buildMessage(sentMessageCount);` 這行取代為下列片段中所顯示的新行：

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 儲存所有變更。

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

### 部署和執行範例應用程式
執行下列命令，在 Edison 上部署和執行範例應用程式：

```bash
gulp deploy && gulp run
```

您應該會看到 LED 開啟兩秒，然後再關閉兩秒。 最後一個 "stop" 訊息會停止執行範例應用程式。

![on 和 off][on-and-off]

恭喜！ 您已成功自訂從 IoT 中樞傳送至 Edison 的訊息。

<a id="summary" class="xliff"></a>

### 摘要
此選讀小節示範如何自訂訊息，讓範例應用程式可以使用不同的方式控制 LED 的開與關行為。

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_c.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_c.png

