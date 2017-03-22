---
title: "將 Arduino 連接到 Azure IoT - 第 1 課：取得工具 (macOS) | Microsoft Docs"
description: "在 macOS 上針對 Adafruit Feather M0 WiFi 的第一個範例應用程式下載並安裝必要的工具和軟體。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 開發工具, iot 開發, iot 軟體, 物聯網軟體, 在 mac 上安裝 git, 在 mac 上安裝 node js"
ms.assetid: 0262f3dd-0259-4eb0-962f-9fb534f8359e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 41b2277a26f0e99052a699cfab8a8129a6c7ddb1
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-macos-1010"></a>取得工具 (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 或更新版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>將執行的作業

下載您 Adafruit Feather M0 WiFi Arduino 面板第一個範例應用程式的開發工具和軟體。 

如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

> [!NOTE]
> 雖然主要邏輯的程式語言是 Arduino，但這些課程中會使用 Node.js 工具來建置和部署範例應用程式。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：

* 如何安裝 Git 和 Node.js。
  * [Git](https://git-scm.com) 是一個開放原始碼分散式版本控制系統。 本文的範例應用程式將會儲存在 Git 上。
  * [Node.js](https://nodejs.org/en/) 是一個具有豐富套件生態系統的 JavaScript 執行階段。
* 如何使用 NPM 安裝其他 Node.js 開發工具。
  * Node.js 的版本最低需求為 4.5 LTS。
  * [NPM](https://www.npmjs.com) 是 Node.js 的其中一個套件管理員。

## <a name="what-you-need"></a>您需要什麼
若要完成此作業，您需要：
* 網際網路連線以下載開發工具和軟體。
* 執行 macOS Yosemite (10.10) 或更新版本的 Mac。

## <a name="install-git-and-nodejs"></a>安裝 Git 和 Node.js
若要安裝 Git 和 Node.js，請遵循下列步驟以使用 [Homebrew](http://brew.sh) 套件管理公用程式︰

1. 安裝 Homebrew。 如果您已經安裝 Homebrew，請移至步驟 2。

   1. 按 `Cmd + Space` 並輸入 `Terminal` 以開啟終端機。
   2. 執行以下命令：

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. 執行下列命令安裝 Git 和 Node.js：

   ```bash
   brew install node git
   ```

## <a name="install-additional-nodejs-development-tools"></a>安裝額外的 Node.js 開發工具
使用 [gulp.js](http://gulpjs.com) 自動將範例應用程式部署至 Arduino 面板。

在終端機中執行下列命令以安裝 `gulp`、`device-discovery-cli`：

```bash
sudo npm install -g gulp device-discovery-cli
```

如果您在 macOS 上安裝 Node.js 和這些額外的開發工具時遇到問題，請參閱[疑難排解指南][troubleshooting]以取得常見問題的解決方案。

## <a name="install-visual-studio-code"></a>安裝 Visual Studio Code
[下載](https://code.visualstudio.com/docs/setup/osx)並安裝 Visual Studio Code。 Visual Studio Code 是一個輕量且強大的原始程式碼編輯器，適用於 Windows、Linux 及 macOS。 您可以稍後於教學課程中使用此編輯器來編輯範例程式碼。

## <a name="summary"></a>摘要
您已安裝第一個範例應用程式所需的開發工具和軟體。 下一個工作是在 Arduino 面板上建立、部署和執行範例應用程式。

## <a name="next-steps"></a>後續步驟
[1.3 建立並部署閃爍應用程式][create-and-deploy-the-blink-application]
<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
