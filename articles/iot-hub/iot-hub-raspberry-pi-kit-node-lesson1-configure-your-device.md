---
title: "將 Raspberry Pi (節點) 連接到 Azure IoT - 第 1 課：設定裝置 | Microsoft Docs"
description: Configure Raspberry Pi 3 for first-time use and install the Raspbian OS, a free operating system that is optimized for the Raspberry Pi hardware.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "安裝 raspbian, raspbian 下載, 如何安裝 raspbian, raspbian 安裝程式, raspberry pi 安裝 raspbian, raspberry pi 安裝 os, raspberry pi sd 記憶卡安裝, raspberry pi 連線, 連線至 raspberry pi, raspberry pi 連線能力"
ms.assetid: 43f7c2cf-f1a5-4dd5-93f0-7e546c6dc91e
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 5b80d471c78973f8c2f6b1ad4e1240c66f0505ef
ms.openlocfilehash: 5afe1110944641daaeace5ef02b2c9fa8d2a8e05
ms.lasthandoff: 02/21/2017


---
# <a name="configure-your-device"></a>設定裝置
## <a name="what-you-will-do"></a>將執行的作業
設定 Pi 以便進行首次使用，並安裝 Raspbian 作業系統。 Raspbian 是最適合用於 Raspberry Pi 硬體的免費作業系統。 如果您有任何問題，可在[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：

* 如何在 Pi 上安裝 Raspbian。
* 如何使用 USB 纜線開啟 Pi 的電源。
* 如何使用乙太網路纜線或無線網路將 Pi 連接到網路。
* 如何將 LED 新增到麵包板並將它連接到 Pi。

## <a name="what-you-will-need"></a>必要元件
若要完成此作業，您需要 Raspberry Pi 3 入門套件中的下列零件：

* Raspberry Pi 3 電路板
* 16 GB 的 microSD 記憶卡
* 具備 6 英呎 micro USB 纜線的 5V 2A 電源供應器
* 麵包板
* 接頭電線
* 一顆 560 歐姆的電阻
* 一顆漫射型 10 mm LED
* 乙太網路纜線

![入門套件中的零件](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

您也會需要：

* 可供 Pi 連線的有線或無線連線。
* 一個 USB-SD 配接器或 miniSD 記憶卡，以將作業系統映像燒錄到 microSD 記憶卡中。
* 一部執行 Windows、Mac 或 Linux 的電腦。 該電腦是用來將 Raspbian 安裝到 microSD 記憶卡上。
* 網際網路連線，以下載必要的工具和軟體。

## <a name="install-raspbian-on-the-microsd-card"></a>將 Raspbian 安裝到 microSD 記憶卡上
準備好用來安裝 Raspbian 映像的 microSD 記憶卡。

1. 下載 Raspbian。
   1. [下載](https://www.raspberrypi.org/downloads/raspbian/)具備 Pixel 的 Raspbian Jessie .zip 檔案。
   2. 將 Raspbian 映像解壓縮到您電腦上的資料夾。
2. 將 Raspbian 安裝到 microSD 記憶卡。
   1. [下載](https://www.etcher.io)並安裝 Etcher SD 記憶卡燒錄器公用程式。
   2. 執行 Etcher 並選取您在步驟 1 中解壓縮的 Raspbian 映像。
   3. 選取 microSD 記憶卡磁碟機。
      注意：Etcher 可能已經選取正確的磁碟機。
   4. 按一下 [Flash] 以將 Raspbian 安裝到 microSD 記憶卡。
   5. 安裝完成時，請將 microSD 記憶卡從電腦移除。
      您可以放心地直接移除 microSD 記憶卡，因為 Etcher 會在完成時自動退出或卸載 microSD 記憶卡。
   6. 將 microSD 記憶卡插入 Pi。

![插入 SD 記憶卡](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="turn-on-pi"></a>開啟 Pi
透過 micro USB 纜線和電源供應器來開啟 Pi。

![開啟](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [!NOTE]
> 請務必使用套件中具有至少 2A 電流強度的電源供應器，來確保 Raspberry 具有足夠的電力以正常運作。

## <a name="enable-ssh"></a>啟用 SSH
截至 2016 年 11 月版本，Raspbian 預設已停用 SSH 伺服器。 您必須手動進行啟用。 您可以參考[官方指示 (英文)](https://www.raspberrypi.org/documentation/remote-access/ssh/)，或者連接監視器並移至 [喜好設定] -> [Raspberry Pi 組態] 來啟用 SSH。

## <a name="connect-raspberry-pi-3-to-the-network"></a>將 Raspberry Pi 3 連接到網路
您可以將 Pi 連接到有線網路或無線網路。 請確定 Pi 是連接到與您電腦相同的網路。 例如，您可以將 Pi 連接到已和電腦連線的交換器。

### <a name="connect-to-a-wired-network"></a>連接到有線網路
使用乙太網路纜線將 Pi 連接到有線網路。 Pi 上的兩顆 LED 將會在連線建立時開啟。

![使用乙太網路纜線連接](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="connect-to-a-wireless-network"></a>連接到無線網路
請遵循來自 Raspberry Pi Foundation 的[指示](https://www.raspberrypi.org/learning/software-guide/wifi/)，將 Pi 連接到無線網路。 這些指示需要您先將 Pi 連接到監視器和鍵盤。

## <a name="connect-the-led-to-pi"></a>將 LED 連接到 Pi
若要完成此工作，請使用[麵包板](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard)、接頭電線、LED 及電阻。 將它們連接到 Pi 的 [general-purpose input/output](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) 連接埠。

![麵包板、LED 及電阻](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. 將 LED 的短腳連接到 **GPIO GND** (針腳 6)。
2. 將 LED 的長腳連接到電阻的其中一隻腳。
3. 將電阻的另一隻腳連接到 **GPIO 4** (針腳 7)。

請注意，LED 的極性很重要。 此極性設定通稱為低態動作。

![接腳圖](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

恭喜！ 您已經成功設定 Pi。

## <a name="summary"></a>摘要
在本文中，您已了解如何透過安裝 Raspbian、將 Pi 連接到網路，以及將 LED 連接到 Pi 來設定 Pi。 請注意，LED 目前尚未亮起。 下一個工作是安裝必要的工具和軟體，以準備在 Pi 上執行範例應用程式。

![硬體已準備完畢](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>後續步驟
[取得工具](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)


