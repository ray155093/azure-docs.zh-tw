---
title: "將 Raspberry Pi (C) 連接到 Azure IoT - 第 2 課：Azure 工具 (Windows) | Microsoft Docs"
description: "在 Windows 7 和更新版本上安裝 Python 和 Azure 命令列介面 (Azure CLI)。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 雲端服務, azure cli"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: a3c083b5-0d76-46af-bc77-2ad7d8aadc1e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 1ae210489b8d369a5fca33e1824b9dc224ad33d2
ms.contentlocale: zh-tw
ms.lasthandoff: 01/24/2017

---
<a id="get-azure-tools-windows-7-and-later" class="xliff"></a>

# 取得 Azure 工具 (Windows 7 和更新版本)
> [!div class="op_single_selector"]
> * [Windows 7 和更新版本](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-mac.md)

<a id="what-you-will-do" class="xliff"></a>

## 將執行的作業
安裝 Python 和 Azure 命令列介面 (Azure CLI)。 如果您有任何問題，請在[疑難排解頁面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)尋求解決方案。

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

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. 執行下列命令以驗證安裝：

   ```bash
   az iot -h
   ```

如果已成功安裝，您將會看見下列輸出。

![表示成功的輸出](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

<a id="summary" class="xliff"></a>

## 摘要
您已安裝 Azure CLI。 下一個工作是使用 Azure CLI 建立 Azure IoT 中樞和裝置身分識別。

<a id="next-steps" class="xliff"></a>

## 後續步驟
[建立 IoT 中樞並登錄 Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)


