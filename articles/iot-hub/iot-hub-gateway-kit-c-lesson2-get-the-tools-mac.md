---
title: "SensorTag 裝置與 Azure IoT 閘道 - 第 2 課︰取得工具 (macOS) | Microsoft Docs"
description: "在您的 Mac 電腦上安裝工具、建立 IoT 中樞，並在 IoT 中樞登錄您的裝置。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 開發, iot 軟體, iot 雲端服務, 物聯網軟體, azure cli, 安裝 python mac, 在 mac 上安裝 git, gulp 執行, 安裝 node js mac"
ms.assetid: 94e538ef-9857-4023-9c3c-e92a0be7c395
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 8b105cebd5b3e6c5cae726abbf5f2c48c0cd7587


---
# <a name="get-the-tools-macos"></a>取得工具 (MacOS)
> [!div class="op_single_selector"]
> * [Windows 7 或更新版本](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>將執行的作業

- 安裝 Git、Node.js、Gulp、Python。
- 安裝 Azure 命令列介面 (Azure CLI)。 

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標

在這一課，您將了解：

- 如何安裝 [Git](https://git-scm.com/) 和 [Node.js](https://nodejs.org/en/)。
  - Git 是一個開放原始碼分散式版本控制系統。 本課程的範例應用程式將會儲存在 Git 上。
  - Node.js 是一個具有豐富套件生態系統的 JavaScript 執行階段。
- 如何使用 [NPM](https://www.npmjs.com/) 安裝 Node.js 開發工具。
  - Node.js 的版本最低需求為 4.5 LTS。
  - NPM 是 Node.js 的其中一個套件管理員。
- 如何安裝 Visual Studio Code。
  - Visual Studio Code 是一個跨平台、輕量且強大的原始程式碼編輯器，適用於 Windows、Linux 及 macOS。 它對偵錯、內嵌的 Git 控制項、語法反白顯示、智慧型程式碼完成、程式碼片段、程式碼重構也有絕佳的支援。
- 如何安裝 Python。
  - Python 是普遍使用的高階、一般用途、解譯、動態的程式設計語言。
- 如何安裝 Azure CLI。
  - Azure CLI 提供適用於 Azure 的多平台命令列體驗。 您可直接從命令列工作以佈建和管理資源。
- 如何使用 Azure CLI 建立 IoT 中樞。

## <a name="what-you-need"></a>您需要什麼

- 可下載工具和軟體的網際網路連線。
- 執行 OS X Yosemite (10.10) 或更新版本的 Mac 電腦。

## <a name="install-git-and-nodejs"></a>安裝 Git 和 Node.js

若要安裝 Git 和 Node.js，請遵循下列步驟，使用 Homebrew 套件管理公用程式︰

1. [下載](http://brew.sh/)並安裝 Homebrew。 如果您已經安裝 Homebrew，請移至步驟 2。
   1. 按 `Cmd + Space` 並輸入 `Terminal` 以開啟終端機。
   2. 執行以下命令：

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. 執行下列命令安裝 Git 和 Node.js：

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>安裝 Node.js 開發工具

您使用 [gulp.js](http://gulpjs.com/) 來自動化部署和執行指令碼。

若要安裝 gulp，在終端機中執行下列命令：

```bash
npm install -g gulp
```

如果安裝遇到問題，請參閱[疑難排解指南](iot-hub-gateway-kit-c-troubleshooting.md)，裡面有常見問題的解決辦法。

> [!Note]
> 需有節點、NPM、Gulp 才能執行 Node.js 開發的自動化指令碼。

## <a name="install-python"></a>安裝 Python

雖然 Mac OS X 有 Python 2.7，但建議您透過 Homebrew 安裝 Python。 請參閱[在 Mac OS X 上安裝 Python](http://docs.python-guide.org/en/latest/starting/install/osx/)。

執行下列命令安裝 Python 與 pip：

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>安裝 Azure CLI

若要安裝 Azure CLI，請遵循下列步驟：

1. 在終端機中執行下列命令：
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   安裝可能需要 5 分鐘。

2. 執行下列命令以驗證安裝：
   ```bash
   az iot -h
   ```
   如果已成功安裝，您應該會看見下列輸出。

   ![確認 Azure CLI 安裝](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>安裝 Visual Studio Code

您在本教學課程稍後章節會使用 Visual Studio Code 來編輯設定檔。

[下載](https://code.visualstudio.com/docs/setup/osx)並安裝 Visual Studio Code。

## <a name="summary"></a>摘要

您已在 Mac 電腦上安裝所有必要的工具和軟體。 下一步是使用 Azure CLI 建立 IoT 中樞，並且在 IoT 中樞登錄您的裝置。

## <a name="next-steps"></a>後續步驟
[建立 IoT 中樞並登錄裝置](iot-hub-gateway-kit-c-lesson2-register-device.md)



<!--HONumber=Jan17_HO4-->


