---
title: "將 Intel Edison (節點) 連接到 Azure IoT - 第 1 課：部署應用程式 | Microsoft Docs"
description: "從 GitHub 複製範例 C 應用程式，並執行 gulp 以將此應用程式部署至您的 Intel Edison 面板。 此範例應用程式會讓與面板連接的 LED 每兩秒閃爍一次。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino led 專案, arduino led 閃爍, arduino led 閃爍程式碼, arduino 閃爍程式, arduino 閃爍範例"
ms.assetid: ed2c21d0-c72c-4ac2-9e70-347e9a0711c0
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 182e63d40986b61a12885799b1b33bb570437a3c
ms.lasthandoff: 01/25/2017


---
# <a name="create-and-deploy-the-blink-application"></a>建立並部署閃爍應用程式
## <a name="what-you-will-do"></a>將執行的作業
從 GitHub 複製範例 C 應用程式，並使用 gulp 工具將範例應用程式部署至您的 Intel Edison。 範例應用程式會讓與面板連接的 LED 每兩秒閃爍一次。 如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
* 如何在 Edison 上部署和執行範例應用程式。

## <a name="what-you-need"></a>您需要什麼
您必須已順利完成下列作業︰

* [設定裝置][configure-your-device]
* [取得工具][get-the-tools]

## <a name="open-the-sample-application"></a>開啟範例應用程式
若要開啟範例應用程式，請遵循下列步驟：

1. 執行下列命令，從 GitHub 複製範例儲存機制︰

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-edison-getting-started.git
   ```
2. 執行下列命令來在 Visual Studio Code 中開啟範例應用程式︰

   ```bash
   cd iot-hub-node-edison-getting-started
   cd Lesson1
   code .
   ```

   ![儲存機制結構][repo-structure]

`app` 子資料夾中的檔案是包含 LED 控制程式碼的關鍵來源檔案。

### <a name="install-application-dependencies"></a>安裝應用程式相依性
執行下列命令，安裝範例應用程式需要的程式庫和其他模組︰

```bash
npm install
```

## <a name="configure-the-device-connection"></a>設定裝置連線
若要設定裝置連線，請遵循下列步驟：

1. 執行下列命令來產生裝置組態檔：

   ```bash
   gulp init
   ```

   組態檔 `config-edison.json` 包含您用來登入 Edison 的使用者認證。 為了避免使用者認證外流，組態檔會產生於電腦主資料夾的 `.iot-hub-getting-started` 子資料夾中。

2. 執行下列命令在 Visual Studio Code 中開啟裝置組態檔：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. 使用您在上一課所記下的 IP 位址和密碼取代預留位置 `[device hostname or IP address]` 和 `[device password]`。

   ![Config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

恭喜！ 您已成功建立 Edison 的第一個範例應用程式。

## <a name="deploy-and-run-the-sample-application"></a>部署和執行範例應用程式

### <a name="deploy-and-run-the-sample-app"></a>部署和執行範例應用程式
執行下列命令，部署和執行範例應用程式：

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>確認應用程式可以運作
在 LED 閃爍 20 次後，範例應用程式就會自動終止。 如果 LED 沒有閃爍，請參閱[疑難排解指南][troubleshooting]，裡面有常見問題的解決方案。

![LED 閃爍][led-blinking]

## <a name="summary"></a>摘要
您已安裝必要工具來使用 Edison，並已在 Edison 上部署範例應用程式來讓 LED 閃爍。 現在，您可以建立、部署和執行另一個範例應用程式，將 Edison 連線至 Azure IoT 中樞來傳送和接收訊息。

## <a name="next-steps"></a>後續步驟
[取得 Azure 工具][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md

