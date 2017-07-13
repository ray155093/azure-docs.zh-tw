---
title: "將 Intel Edison (節點) 連接到 Azure IoT - 第 2 課：Azure 工具 (Windows) | Microsoft Docs"
description: "在 Windows 7 和更新版本上安裝 Python 和 Azure 命令列介面 (Azure CLI)。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure cli, iot 雲端服務, arduino 雲端"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: 60631b54-6d2e-4e8a-88bf-7c2f8e7e1f29
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 181d7113bda6e9d39bc24c1892940803fc03fd6b
ms.contentlocale: zh-tw
ms.lasthandoff: 01/25/2017

---
<a id="get-azure-tools-windows-7-and-later" class="xliff"></a>

# 取得 Azure 工具 (Windows 7 和更新版本)
> [!div class="op_single_selector"]
> * [Windows 7 和更新版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## 將執行的作業
安裝 Python 和 Azure 命令列介面 (Azure CLI)。 如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

<a id="what-you-will-learn" class="xliff"></a>

## 學習目標
在本文中，您將了解：
* 如何安裝 Python。
* 如何安裝 Azure CLI。

<a id="what-you-need" class="xliff"></a>

## 您需要什麼
* 一部具有網際網路連線的 Windows 電腦。
* 有效的 Azure 訂用帳戶。 如果您沒有 Azure 帳戶，請花幾分鐘的時間建立[免費的 Azure 試用帳戶](http://azure.microsoft.com/pricing/free-trial/)。

<a id="install-python" class="xliff"></a>

## 安裝 Python
在 Windows 電腦上[安裝 Python](https://www.python.org/downloads/)。 您可以安裝 Python 2.7、3.4 或 3.5。 本教學課程是以 Python 2.7 為依據。 如果您已安裝 Python，請移至下一節，並安裝 Azure CLI。

您也必須將安裝 python.exe 和 pip.exe 的資料夾路徑新增到系統 `PATH` 環境變數。 根據預設，python.exe 是安裝在 `C:\Python27`，而 pip.exe 是安裝在 `C:\Python27\Scripts`。

<a id="install-the-azure-cli" class="xliff"></a>

## 安裝 Azure CLI
Azure CLI 提供適用於 Azure 的多平台命令列體驗。 您可直接從命令列工作以佈建和管理資源。

若要安裝 Azure CLI，請遵循下列步驟：

1. 以系統管理員身分開啟 [命令提示字元] 視窗。
2. 執行以下命令：

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. 執行下列命令以驗證安裝：

   ```cmd
   az iot -h
   ```

如果已成功安裝，您將會看見下列輸出。

![表示成功的輸出](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_win.png)

<a id="summary" class="xliff"></a>

## 摘要
您已安裝 Azure CLI。 下一個工作是使用 Azure CLI 建立 Azure IoT 中樞和裝置身分識別。

<a id="next-steps" class="xliff"></a>

## 後續步驟
[建立 IoT 中樞並登錄 Intel Edison][create-your-iot-hub-and-register-intel-edison]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-mac.md

