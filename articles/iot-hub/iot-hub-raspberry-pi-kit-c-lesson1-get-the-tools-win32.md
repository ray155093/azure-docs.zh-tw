---
title: "將 Raspberry Pi (C) 連接到 Azure IoT - 第 1 課：取得工具 (Windows) | Microsoft Docs"
description: "在 Windows 7 和更新版本上，針對 Pi 的第一個範例應用程式下載並安裝必要的工具和軟體。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 開發, iot 軟體, 物聯網軟體, 在 windows 上安裝 git, 安裝 node js windows, 在 windows 上安裝 npm"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: bd765ddd-65b7-4241-a391-dc77cb3af1c0
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: d53efc7d25714427e25d4f88279f3b0d4f61c150
ms.contentlocale: zh-tw
ms.lasthandoff: 01/24/2017

---
<a id="get-the-tools-windows-7-or-later" class="xliff"></a>

# 取得工具 (Windows 7 或更新版本)

> [!div class="op_single_selector"]
> * [Windows 7 或更新版本](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-mac.md)

<a id="what-you-will-do" class="xliff"></a>

## 將執行的作業
下載您 Raspberry Pi 3 第一個範例應用程式的開發工具和軟體。 如果您有任何問題，請在[疑難排解頁面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)尋求解決方案。

> [!NOTE]
> 雖然主要邏輯的程式語言是 C，但這些課程中會使用 Node.js 工具來探索裝置，以及建置和部署範例應用程式。

<a id="what-you-will-learn" class="xliff"></a>

## 學習目標
在本文中，您將了解：

* 如何安裝 Git 和 Node.js。
  * [Git](https://git-scm.com) 是一個開放原始碼分散式版本控制系統。 本文的範例應用程式將會儲存在 Git 上。
  * [Node.js](https://nodejs.org/en/) 是一個具有豐富套件生態系統的 JavaScript 執行階段。
* 如何使用 NPM 安裝額外的 Node.js 開發工具。
  * Node.js 的最低版本需求為 4.5 LTS。
  * [NPM](https://www.npmjs.com) 是 Node.js 的其中一個套件管理員。

<a id="what-you-need" class="xliff"></a>

## 您需要什麼

若要完成此作業，您需要：

* 網際網路連線以下載開發工具和軟體。
* 一部執行 Windows 的電腦。

<a id="install-git-and-nodejs" class="xliff"></a>

## 安裝 Git 和 Node.js

按一下下列連結以下載並安裝 Git for Windows 和適用於 Windows 的 Node.js LTS。

* [取得 Git for Windows](https://git-scm.com/download/win/)
* [取得適用於 Windows 的 Node.js LTS](https://nodejs.org/en/)

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## 安裝額外的 Node.js 開發工具

使用 [gulp.js](http://gulpjs.com) 對 Pi 自動部署範例應用程式。 使用 [device-discovery-cli](https://github.com/Azure/device-discovery-cli) 來擷取關於您 IoT 裝置的網路資訊。

以系統管理員身分開啟命令提示字元。 執行下列命令以安裝 `gulp` 和 `device-discovery-cli`：

```bash
npm install -g device-discovery-cli gulp
```

如果您於電腦上安裝 Node.js 和這些額外的 Node.js 開發工具時遇到問題，請參閱[疑難排解指南](iot-hub-raspberry-pi-kit-c-troubleshooting.md)以取得常見問題的解決方案。

<a id="install-visual-studio-code" class="xliff"></a>

## 安裝 Visual Studio Code

[下載](https://code.visualstudio.com/docs/setup/windows)並安裝 Visual Studio Code。 Visual Studio Code 是一個輕量且強大的原始程式碼編輯器，適用於 Windows、Linux 及 macOS。 您可以稍後於教學課程中使用此編輯器來編輯範例程式碼。

<a id="summary" class="xliff"></a>

## 摘要

您已安裝第一個範例應用程式所需的開發工具和軟體。 下一個工作是在 Pi 上建立、部署和執行範例應用程式。

<a id="next-steps" class="xliff"></a>

## 後續步驟

[1.3 建立並部署閃爍應用程式](iot-hub-raspberry-pi-kit-c-lesson1-deploy-blink-app.md)

