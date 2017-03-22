---
title: "將 Arduino (C) 連接到 Azure IoT - 第 1 課：設定裝置 | Microsoft Docs"
description: "設定 Adafruit Feather M0 WiFi 以進行首次使用。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 設定, 將 arduino 連接到電腦, 設定 arduino, arduino 面板"
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3789c07cd6142ba5b6a58a67813c0ead6b87bd76
ms.lasthandoff: 01/24/2017


---
# <a name="configure-your-device"></a>設定裝置
## <a name="what-you-will-do"></a>將執行的作業
設定 Adafruit Feather M0 WiFi Arduino 面板以進行首次使用，方法是組裝面板、接上電源。 如果您有任何問題，請在[疑難排解頁面](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)尋求解決方案。

## <a name="what-you-need"></a>您需要什麼
若要完成此作業，您需要 Adafruit Feather M0 WiFi 入門套件的下列零件：

* Adafruit Feather M0 WiFi 面板
* Micro B 轉 Type A 的 USB 纜線

![套件][kit]

您也會需要：

* 一部執行 Windows、Mac 或 Linux 的電腦。
* 可供 Arduino 面板連線的無線連線。
* 用來下載組態工具的網際網路連線。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：

* 如何組裝 Arduino 面板並接上電源，以進行接下來的課程。
* 如何在 Ubuntu 上新增序列連接埠權限。

## <a name="connect-your-arduino-board-to-your-computer"></a>將 Arduino 面板連線到電腦

1. 將 micro USB 纜線插入最上方的 micro USB 連接埠。

   ![最上方的 micro USB 連接埠][top-micro-usb-port]

2. 將 USB 纜線的另一端插入電腦。

   ![電腦 USB][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>在 Ubuntu 上新增序列連接埠權限

如果您使用 Windows 或 macOS，則可略過本節。 對於 Ubuntu，您需要執行下列步驟，以確定一般的 linux 使用者有權對 Arduino 面板的 USB 連接埠進行操作。

1. 現在從終端機以一般使用者身分︰

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   您會看到類似下面的內容：

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   "0" 可能是不同的數字，或可能會傳回多個項目。 在第一個案例中，我們所需的資料是 `uucp`，在第二個案例中則是 `dialout`，這是檔案的群組擁有者。

2. 將使用者新增至群組︰

   ```bash
   sudo usermod -a -G group-name username
   ```

   其中 `group-name` 是第一個步驟中找到的資料，`username` 則是您的 linux 使用者名稱。

3. 您必須先登出再登入以讓此變更生效，並完成設定。

## <a name="summary"></a>摘要
在本文中，您已了解如何設定 Arduino 面板。 下一個工作是安裝必要的工具和軟體，以準備在 Arduino 面板上執行範例應用程式。

![硬體已準備完畢][hardware-is-ready]

## <a name="next-steps"></a>後續步驟
[取得工具][get-the-tools]
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
