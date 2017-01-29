---
title: "將 Intel NUC 設定為 IoT 閘道器 | Microsoft Docs"
description: "將 Intel NUC 設定為在感應器和 Azure IoT 中樞之間做為 IoT 閘道器，以收集感應器資訊，並將資訊傳送至 IoT 中樞。"
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: "iot 閘道, intel nuc, nuc 電腦, DE3815TYKE"
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: ef479f503ac4e911f3a9d311d0f436d95396eedd


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>將 Intel NUC 設定為 IoT 閘道器

## <a name="what-you-will-do"></a>將執行的作業

- 將 Intel NUC 設定為 IoT 閘道器。
- 在 Intel NUC 上安裝 Azure IoT 閘道器 SDK 套件。
- 在 Intel NUC 上執行 "hello_world" 範例應用程式以確認閘道器的功能。
如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標

在這一課，您將了解：

- 如何連接 Intel NUC 與週邊設備。
- 如何使用智慧型套件管理員 (Smart Package Manager) 安裝及更新 Intel NUC 上的所需套件。
- 如何執行 "hello_world" 範例應用程式以確認閘道器的功能。

## <a name="what-you-need"></a>您需要什麼

- 預先安裝的 Intel NUC 套件 DE3815TYKE 與 Intel IoT 閘道器軟體套件 (Wind River Linux *7.0.0.13)。
- 乙太網路連接線。
- 鍵盤。
- HDMI 或 VGA 連接線。
- 具有 HDMI 或 VGA 連接埠的螢幕。

![閘道器套件](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>連接 Intel NUC 與週邊設備

下圖是 Intel NUC 與各種週邊設備連接的範例︰

1. 連接到鍵盤。
2. 用 VGA 連接線或 HDMI 連接線連接到螢幕。
3. 透過乙太網路連接線連接到有線網路。
4. 用電源線連接到電源供應器。

![連接到週邊設備的 Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>從主機電腦透過安全殼層 (SSH) 連線至 Intel NUC 系統

此時您需要鍵盤和螢幕來取得 NUC 裝置的 IP 位址。 如果您已經知道 IP 位址，可以跳至本節的步驟 3。

1. 按下電源按鈕開啟 Intel NUC，登入系統。

   預設的使用者名稱和密碼都是 `root`。

2. 執行 `ifconfig` 命令取得 NUC 的 IP 位址。 這個步驟是在 NUC 裝置上進行。

   以下是命令的範例輸出。

   ![ifconfig 輸出顯示 NUC IP](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   在此範例中，`inet addr:` 後的值是 IP 位址，您打算從主機電腦遠端連線 Intel NUC 時需要此值。

3. 使用下列其中一個 SSH 用戶端，從主機電腦連線至 Intel NUC。

   - [PuTTY](http://www.putty.org/) 適用於 Windows。
   - 在 Ubuntu 或 macOS 上有內建 SSH 用戶端。

   從主機電腦操作 Intel NUC 會更有效率且更有生產力。 您需要 IP 位址、使用者名稱和密碼才能透 SSH 用戶端連線至 NUC。 以下是在 macOS 上 SSH 用戶端的範例。
   ![在 macOS 上執行的 SSH 用戶端](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>安裝 Azure IoT 閘道器 SDK 套件

Azure IoT 閘道器 SDK 套件包含預先編譯的 SDK 二進位檔及其相依性。 這些二進位檔是 Azure IoT 閘道器 SDK、Azure IoT SDK 和對應的工具。 套件中也有 "hello_world" 範例應用程式，用以確認閘道器的功能。 此 SDK 是閘道器的核心部分。 若要安裝套件，請遵循下列步驟：

1. 在終端機視窗中執行下列命令，新增 IoT 雲端存放庫：

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   `rpm` 命令會匯入 rpm 索引鍵。 `smart channel` 命令會將 rpm 通道新增至智慧型套件管理員。 執行 `smart update` 命令前，您會看到如下的輸出。

   ![rpm 和 smart channel 命令的輸出](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. 執行下列命令安裝套件：

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` 是套件的名稱。 `smart install` 命令是用於安裝套件。

   安裝套件後，Intel NUC 應該可以當做閘道運作。

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>執行 Azure IoT 閘道器 SDK "hello_world" 範例應用程式

移至 `azureiotgatewaysdk/samples`，執行 "hello_world" 範例應用程式。 此範例應用程式會從 `hello_world.json` 檔案建立閘道器，並使用 Azure IoT 閘道器 SDK 架構中的基礎元件，每隔 5 秒將 hello world 訊息記錄到檔案中。

您可以執行下列命令，執行 "hello_world" 範例應用程式：

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

如果閘道器功能正常運作，範例應用程式會產生下列輸出︰

![應用程式的輸出](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-troubleshooting.md)尋求解決方案。

## <a name="summary"></a>摘要

恭喜！ 您已完成將 Intel NUC 設定為閘道器。 現在您可以開始進行下一課，設定主機電腦、建立 Azure IoT 中樞，並登錄您的 Azure IoT 中樞邏輯裝置。

## <a name="next-steps"></a>後續步驟
[準備好您的主機電腦和 Azure IoT 中樞](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)



<!--HONumber=Dec16_HO3-->


