---
title: "準備好您的主機電腦和 Azure IoT 中樞 | Microsoft Docs"
description: "在執行 Windows 的主機電腦上安裝工具和軟體，建立 IoT 中樞，在 IoT 中樞登錄您的裝置。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 開發, iot 軟體, iot 雲端服務, 物聯網軟體, azure cli, 在 windows 上安裝 git, gulp 執行, 安裝 node js windows, 在 windows 上安裝 npm, 在 windows 上安裝 python"
ms.assetid: c16eee4c-8756-454b-82bf-3eb0dd51aa5f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: fd00d36e55109dfa4c6ddfab7d92872854696ba0


---
# <a name="get-the-tools-windows-7-and-later"></a>取得工具 (Windows 7 和更新版本)
> [!div class="op_single_selector"]
> * [Windows 7 或更新版本](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>將執行的作業

- 安裝 Git、Node.js、Gulp、Python。
- 安裝 Azure 命令列介面 (Azure CLI)。 

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-sim-troubleshooting.md)尋求解決方案。

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
- Windows 電腦。

## <a name="install-git-and-nodejs"></a>安裝 Git 和 Node.js

按一下下列連結以下載並安裝 Git for Windows 和適用於 Windows 的 Node.js LTS。

- [取得 Git for Windows](https://git-scm.com/download/win/)
- [取得適用於 Windows 的 Node.js LTS](https://nodejs.org/en/)

## <a name="install-nodejs-development-tools"></a>安裝 Node.js 開發工具

您使用 [gulp.js](http://gulpjs.com/) 來自動化部署和執行指令碼。

按 `Windows + R`，輸入 `cmd`，然後按 `Enter` 開啟命令提示字元視窗，執行下列命令：

```cmd
npm install -g gulp
```

如果安裝遇到問題，請參閱[疑難排解指南](iot-hub-gateway-kit-c-sim-troubleshooting.md)，裡面有常見問題的解決辦法。

> [!Note]
> 需有節點、NPM、Gulp 才能執行 Node.js 開發的自動化指令碼。

## <a name="install-python"></a>安裝 Python

您可以選擇 Python 2.7、3.4 或 3.5。 在本教學課程中，我們使用 Python 2.7。 如果您已經安裝 Python，請移至下一節。

[取得適用於 Windows 的 Python](https://www.python.org/downloads/)

您也必須將安裝 python.exe 和 pip.exe 的資料夾路徑新增到系統 `PATH` 環境變數。 根據預設，python.exe 是安裝在 `C:\Python27`，而 pip.exe 是安裝在 `C:\Python27\Scripts`。

## <a name="install-the-azure-cli"></a>安裝 Azure CLI

若要安裝 Azure CLI，請遵循下列步驟：

1. 以系統管理員身分開啟 [命令提示字元] 視窗。

2. 執行以下命令來安裝 Azure CLI：

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```

   安裝可能需要 5 分鐘。

3. 執行下列命令以驗證安裝：

   ```cmd
   az iot -h
   ```

   如果已成功安裝，您應該會看見下列輸出。

   ![確認 Azure CLI 安裝](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_win.png)

## <a name="install-visual-studio-code"></a>安裝 Visual Studio Code

您在本教學課程稍後章節會使用 Visual Studio Code 來編輯設定檔。

[下載](https://code.visualstudio.com/docs/setup/windows)並安裝 Visual Studio Code。

## <a name="summary"></a>摘要

您已在主機電腦上安裝所有必要的工具和軟體。 下一步是使用 Azure CLI 建立 IoT 中樞，並且在 IoT 中樞登錄您的裝置。

## <a name="next-steps"></a>後續步驟
[建立 IoT 中樞並登錄您的裝置](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)



<!--HONumber=Dec16_HO3-->


