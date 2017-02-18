---
title: "將 Raspberry Pi (節點) 連接到 Azure IoT - 開始使用 | Microsoft Docs"
description: "開始使用 Raspberry Pi 3 裝置、建立 Azure IoT 中樞，以及將 Pi 連接至 IoT 中樞。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot 中樞, 開始使用物聯網, iot 工具組"
experimental: true
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 358a2a4fd448660c3a8ef0d11d7d373dd7d9a569


---
# <a name="get-started-with-raspberry-pi-3-nodejs"></a>開始使用 Raspberry Pi 3 (Node.js)
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-raspberry-pi-kit-node-get-started.md)
> * [C](iot-hub-raspberry-pi-kit-c-get-started.md)

在本教學課程中，您會開始了解執行 Raspbian 的 Raspberry Pi 3 在使用方面的基本知識。 接著會了解如何使用 [Azure IoT 中樞](iot-hub-what-is-iot-hub.md)讓您的裝置順暢地與雲端連線。 如需 Windows 10 IoT 核心範例，請移至 [Windows 開發人員中心](http://www.windowsondevices.com/)。

還沒有套件嗎？ 從[這裡](https://azure.microsoft.com/develop/iot/starter-kits)開始。

## <a name="lesson-1-configure-your-device"></a>第 1 課：設定裝置
![第 1 課端對端圖表](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

在本課程中，您會設定 Raspberry Pi 3 裝置及作業系統、設定開發環境，並將應用程式部署至 Pi。

### <a name="configure-your-device"></a>設定裝置
設定 Raspberry Pi 3 以便進行首次使用，並安裝 Raspbian。 Raspbian 是最適合用於 Raspberry Pi 硬體的免費作業系統。

*預估完成時間：30 分鐘*

前往[設定裝置](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)。

### <a name="get-the-tools"></a>取得工具
下載工具與軟體建置，以建置並部署您第一個適用於 Raspberry Pi 3 的應用程式。

*預估完成時間：20 分鐘*

前往[取得工具](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)。

### <a name="create-and-deploy-the-blink-application"></a>建立並部署閃爍應用程式
從 GitHub 複製範例 Node.js 閃爍應用程式，並使用 gulp 以將此應用程式部署至您的 Raspberry Pi 3 面板。 此範例應用程式會讓與面板連接的 LED 每兩秒閃爍一次。

*預估完成時間：5 分鐘*

前往[建立並部署閃爍應用程式](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)。

## <a name="lesson-2-create-your-iot-hub"></a>第 2 課：建立 IoT 中樞
![第 2 課端對端圖表](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

在本課程中，您會建立一個免費的 Azure 帳戶、佈建您的 Azure IoT 中樞，並在 IoT 中樞建立您的第一個裝置。

開始本課程前，請先完成第 1 課。

### <a name="get-the-azure-tools"></a>取得 Azure 工具
安裝 Azure 命令列介面 (Azure CLI)。

*預估完成時間：10 分鐘*

前往[取得 Azure 工具](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)。

### <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>建立 IoT 中樞並登錄 Raspberry Pi 3
建立資源群組、佈建您的第一個 Azure IoT 中樞，並使用 Azure CLI 將您的第一個裝置新增至 IoT 中樞。

*預估完成時間：10 分鐘*

前往[建立 IoT 中樞並登錄 Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)。

## <a name="lesson-3-send-device-to-cloud-messages"></a>第 3 課：傳送裝置到雲端訊息
![第 3 課端對端圖表](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

在本課中，您會將訊息從 Pi 傳送到 IoT 中樞。 您也會建立 Azure 函式應用程式，此應用程式會從 IoT 中樞取得傳入訊息，並將這些訊息寫入 Azure 表格儲存體。

開始本課程前，請先完成第 1 課和第 2 課。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>建立 Azure 函數應用程式與 Azure 儲存體帳戶
使用 Azure Resource Manager 範本來建立 Azure 函數應用程式及 Azure 儲存體帳戶。

*預估完成時間：10 分鐘*

前往[建立 Azure 函式應用程式與 Azure 儲存體帳戶](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>執行範例應用程式以傳送裝置到雲端訊息
將範例應用程式部署至 Raspberry Pi 3 裝置，並執行該應用程式以傳送訊息至 IoT 中樞。

*預估完成時間：10 分鐘*

前往[執行範例應用程式以傳送裝置到雲端訊息](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)。

### <a name="read-messages-persisted-in-azure-storage"></a>讀取保存在 Azure 儲存體中的訊息
當裝置到雲端訊息寫入 Azure 儲存體時對其進行監視。

*預估完成時間：5 分鐘*

前往[讀取保存在 Azure 儲存體中的訊息](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)。

## <a name="lesson-4-send-cloud-to-device-messages"></a>第 4 課：傳送雲端到裝置訊息
![第 4 課端對端圖表](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

本課程示範如何將訊息從 Azure IoT 中樞傳送至 Raspberry Pi 3。 這些訊息會針對與 Pi 連接的 LED，控制其開啟與關閉行為。 本課程中已為您準備好範例應用程式以完成這項工作。

開始本課程前，請先完成第 1 課、第 2 課和第 3 課。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>執行範例應用程式以接收雲端到裝置訊息
＜課程 4＞中的範例應用程式會在 Pi 上執行，並監視來自 IoT 中樞的傳入訊息。 新的 Gulp 工作會從 IoT 中樞將訊息傳送到 Pi 來使 LED 閃爍。

*預估完成時間：10 分鐘*

前往[執行範例應用程式以接收雲端到裝置訊息](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>選讀區段：變更 LED 的開起與關閉行為
自訂訊息以變更 LED 的開啟與關閉行為。

*預估完成時間：10 分鐘*

前往[選讀區段：變更 LED 的開啟與關閉行為](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)。

## <a name="troubleshooting"></a>疑難排解
如果在課程期間遇到任何問題，您可以在[疑難排解](iot-hub-raspberry-pi-kit-node-troubleshooting.md)一文中尋求解決方案。




<!--HONumber=Jan17_HO4-->


