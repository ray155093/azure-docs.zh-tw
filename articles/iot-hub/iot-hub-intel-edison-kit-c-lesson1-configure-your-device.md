---
title: "將 Intel Edison (C) 連接到 Azure IoT - 第 1 課：設定裝置 | Microsoft Docs"
description: "設定 Intel Edison 以進行首次使用。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 設定, 將 arduino 連接到電腦, 設定 arduino, arduino 面板"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: bb8aa45b-d3ff-4438-b9d6-a9725a45ade1
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 800f1aed6d30d2bb871a6a9b55b6b95308932211
ms.lasthandoff: 01/25/2017


---
# <a name="configure-your-intel-edison"></a>設定 Intel Edison
## <a name="what-you-will-do"></a>將執行的作業
設定 Intel Edison 以進行首次使用，方法是組裝面板、接上電源，並將組態工具安裝到桌面作業系統，以更新 Edison 的韌體、設定其密碼，然後將其連線至 Wi-Fi。 如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：

* 如何組裝 Edison 面板，並將它接上電源。
* 如何更新 Edison 的韌體、設定密碼及連線至 Wi-Fi。

## <a name="what-you-need"></a>您需要什麼
若要完成此作業，您需要 Intel Edison 入門套件中的下列零件：

* Intel® Edison 模組
* Arduino 擴充板
* 包裝內所含的任何間隔柱或螺絲，包括兩個用來將模組鎖到擴充板的螺絲，和四組螺絲和塑膠間隔柱。
* Micro B 轉 Type A 的 USB 纜線
* 直流電 (DC) 電源供應器。 電源供應器的額定值應如下所示︰
  - 7-15V DC
  - 至少 1500mA
  - 中心/內部插銷應該是電源供應器的正極

  ![入門套件中的零件](media/iot-hub-intel-edison-lessons/lesson1/kit.png)

您也會需要：

* 一部執行 Windows、Mac 或 Linux 的電腦。
* 可供 Edison 連線的無線連線。
* 用來下載組態工具的網際網路連線。

## <a name="assemble-your-board"></a>組裝面板

本節所含步驟會將 Intel® Edison 模組連接至擴充板。

1. 將 Intel® Edison 模組放在擴充板的白框內，讓模組上的螺絲孔對齊擴充板上的螺絲。

2. 在模組上的 `What will you make?` 字樣正下方往下按，直到您感覺到兩者咬合。

   ![組裝面板 2](media/iot-hub-intel-edison-lessons/lesson1/assemble_board2.jpg)

3. 使用兩個六角螺帽 (包裝內含) 將模組鎖緊到擴充板上。

   ![組裝面板 3](media/iot-hub-intel-edison-lessons/lesson1/assemble_board3.jpg)

4. 在擴充板四個角落的其中一個螺絲孔中插入螺絲。 將其中一個白色塑膠間隔柱旋緊到螺絲上。

   ![組裝面板 4](media/iot-hub-intel-edison-lessons/lesson1/assemble_board4.jpg)

5. 重複裝上其他三個角落的間隔柱。

   ![組裝面板 5](media/iot-hub-intel-edison-lessons/lesson1/assemble_board5.jpg)

面板現已組裝好。

   ![組裝好的面板](media/iot-hub-intel-edison-lessons/lesson1/assembled_board.jpg)

## <a name="power-up-edison"></a>將 Edison 接上電源

1. 插上電源供應器。

   ![插上電源供應器](media/iot-hub-intel-edison-lessons/lesson1/plug_power.jpg)

2. 綠色 LED (在 Arduino* 擴充板上標示為 DS1) 應該會亮起，並持續亮著。

3. 等候一分鐘，讓面板完成開機。

   > [!NOTE]
   > 如果您沒有 DC 電源供應器，也可以透過 USB 連接埠為面板供電。 如需詳細資訊，請參閱`Connect Edison to your computer`一節。 以此方式為面板供電，可能會讓面板發生無法預期的行為，尤其是在使用 Wi-Fi 或驅動馬達時。

## <a name="connect-edison-to-your-computer"></a>將 Edison 連接到電腦

1. 將微型開關往下扳動到靠近兩個 micro USB 連接埠的方向，使 Edison 進入裝置模式。 若要了解裝置模式與主機模式的差異，請參閱[這裡](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode)。

   ![將微型開關往下扳動](media/iot-hub-intel-edison-lessons/lesson1/toggle_down_microswitch.jpg)

2. 將 micro USB 纜線插入最上方的 micro USB 連接埠。

   ![最上方的 micro USB 連接埠](media/iot-hub-intel-edison-lessons/lesson1/top_usbport.jpg)

3. 將 USB 纜線的另一端插入電腦。

   ![電腦 USB](media/iot-hub-intel-edison-lessons/lesson1/computer_usb.jpg)

4. 當電腦掛接新的磁碟機 (很像將 SD 卡插入電腦) 時，您就會知道面板已完全初始化。

## <a name="download-and-run-the-configuration-tool"></a>下載並執行組態工具
從[此連結](https://software.intel.com/en-us/iot/hardware/edison/downloads)的 `Installers` 標題底下所列項目取得最新的組態工具。 執行工具，並依照其螢幕上的指示進行，視需要按 [Next]

### <a name="flash-firmware"></a>更新韌體
1. 在 `Set up options` 頁面上，按一下 `Flash Firmware`。
2. 執行下列其中一項，選取要更新至面板的映像︰
   - 若要下載 Intel 所提供之最新韌體映像並以此映像更新面板，請選取 `Download the latest image version xxxx`。
   - 若要以電腦上已儲存的映像更新面板，請選取 `Select the local image`。 瀏覽並選取您要更新至面板的映像。
3. 安裝工具會嘗試更新面板。 整個更新程序最多可能需要 10 分鐘。

### <a name="set-password"></a>設定密碼
1. 在 `Set up options` 頁面上，按一下 `Enable Security`。
2. 您可以為 Intel® Edison 面板設定自訂名稱。 這是選擇性。
3. 輸入面板的密碼，然後按一下 `Set password`。
4. 記下密碼，以供稍後使用。

### <a name="connect-wi-fi"></a>連接 Wi-Fi
1. 在 `Set up options` 頁面上，按一下 `Connect Wi-Fi`。 最多請等候一分鐘，讓電腦掃描可用的 Wi-Fi 網路。
2. 從 `Detected Networks` 下拉式清單中選取網路。
3. 從 `Security` 下拉式清單中選取網路的安全性類型。
4. 提供登入和密碼資訊，然後按一下 `Configure Wi-Fi`。
5. 記下 IP 位址，以供稍後使用。

> [!NOTE]
> 請確定 Edison 是連接到與您電腦相同的網路。 電腦會使用該 IP 位址連接到 Edison。

恭喜！ 您已經成功設定 Edison。

## <a name="summary"></a>摘要
在本文中，您已了解如何組裝 Edison 面板、更新其韌體、設定密碼，以及使用組態工具將它連接至 Wi-Fi。 請注意，LED 目前尚未亮起。 下一個工作是安裝必要的工具和軟體，以準備在 Edison 上執行範例應用程式。

## <a name="next-steps"></a>後續步驟
[取得工具][get-the-tools]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
