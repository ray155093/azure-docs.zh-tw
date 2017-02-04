---
title: "取得適用於 Azure IoT 入門套件 (macOS 10.10) 的 Azure 工具 | Microsoft Docs"
description: "在 macOS 上安裝 Python 與 Azure 命令列介面 (Azure CLI)。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure cli, iot 雲端服務, arduino 雲端"
ms.assetid: 9b719293-01d2-4a2d-9c49-476e67f4816d
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: de7d031a05110ccbd2d5ab18a7735a91df2bd05e


---
# <a name="get-azure-tools-macos-1010"></a>取得 Azure 工具 (macOS 10.10)

> [!div class="op_single_selector"]
> * [Windows 7 或更新版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>將執行的作業

安裝 Azure 命令列介面 (Azure CLI)。 如果您有任何問題，請在 Adafruit Feather M0 WiFi Arduino 面版的[疑難排解頁面](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：
* 如何安裝 Azure CLI。
* 如何新增 Azure CLI 的 IoT 子群組。

## <a name="what-you-need"></a>您需要什麼
* 有網際網路連線的 Mac。
* 有效的 Azure 訂用帳戶。 如果您沒有 Azure 帳戶，只需要幾分鐘的時間就可以建立[免費的 Azure 試用帳戶](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="install-python"></a>安裝 Python
雖然 macOS 有現成的 Python 2.7，但建議您透過 Homebrew 安裝 Python。 請參閱 [Installing Python on macOS](http://docs.python-guide.org/en/latest/starting/install/osx/) (在 macOS 上安裝 Python)。

執行下列命令安裝 Python 與 pip：

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>安裝 Azure CLI
Azure CLI 提供適用於 Azure 的多平台命令列體驗。 您可直接從命令列工作以佈建和管理資源。

若要安裝最新的 Azure CLI，請遵循下列步驟：

1. 在終端機視窗中執行下列命令。 安裝 Azure CLI 可能需要五分鐘的時間。

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. 執行下列命令以驗證安裝：

   ```bash
   az iot -h
   ```

如果已成功安裝，您應該會看見下列輸出。

![表示成功的輸出][output]

## <a name="summary"></a>摘要
您已安裝 Azure CLI。 下一個工作是使用 Azure CLI 建立 Azure IoT 中樞和裝置身分識別。

## <a name="next-steps"></a>後續步驟
[建立 IoT 中樞並登錄您的 Arduino 面板][create-your-iot-hub-and-register-your-arduino-board]


<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-mac.md
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_osx.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md


<!--HONumber=Dec16_HO2-->


