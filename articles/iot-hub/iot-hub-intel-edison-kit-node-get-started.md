---
title: "將 Intel Edison (節點) 連接到 Azure IoT - 開始使用 | Microsoft Docs"
description: "開始使用 Intel Edison、建立 Azure IoT 中樞，以及將 Edison 與 IoT 中樞連線"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "intel edison 開發, azure iot 中樞, 開始使用物聯網, 物聯網教學課程, adafruit 物聯網, intel edison arduino, 開始使用 arduino"
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: 43b6efb64c24405ae3638f6d04da5d21e30a55e1
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-intel-edison-device-to-your-iot-hub-using-nodejs"></a>使用 Node.js 將 Intel Edison 裝置連接到 IoT 中樞
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-intel-edison-kit-node-get-started.md)
> * [C](iot-hub-intel-edison-kit-c-get-started.md)

在本教學課程中，您一開始會先了解使用 Intel Edison 的基本知識。 接著會了解如何使用 [Azure IoT 中樞](iot-hub-what-is-iot-hub.md)讓您的裝置順暢地與雲端連線。

還沒有套件嗎？ 從[這裡](https://azure.microsoft.com/develop/iot/starter-kits)開始

## <a name="lesson-1-configure-your-device"></a>第 1 課：設定裝置
![第 1 課端對端圖表](media/iot-hub-intel-edison-lessons/e2e-lesson1.png)

在本課程中，您會設定 Intel Edison 及作業系統、設定開發環境，並將應用程式部署至 Edison。

### <a name="configure-your-device"></a>設定裝置
設定 Intel Edison 以進行首次使用，方法是組裝面板、接上電源，並將組態工具安裝到桌面作業系統，以更新 Edison 的韌體、設定其密碼，然後將其連線至 Wi-Fi。  

*預估完成時間：30 分鐘*

前往[設定裝置][configure-your-device]。

### <a name="get-the-tools"></a>取得工具
下載工具與軟體建置，以建置並部署您第一個適用於 Intel Edison 的應用程式。

*預估完成時間：20 分鐘*

前往[取得工具][get-the-tools]。

### <a name="create-and-deploy-the-blink-application"></a>建立並部署閃爍應用程式
從 GitHub 複製範例閃爍應用程式，並使用 gulp 以將此應用程式部署至您的 Intel Edison 面板。 此範例應用程式會讓與面板連接的 LED 每兩秒閃爍一次。

*預估完成時間：5 分鐘*

前往[建立並部署閃爍應用程式][create-and-deploy-the-blink-application]。

## <a name="lesson-2-create-your-iot-hub"></a>第 2 課：建立 IoT 中樞
![第 2 課端對端圖表](media/iot-hub-intel-edison-lessons/e2e-lesson2.png)

在本課程中，您會建立免費的 Azure 帳戶、佈建 Azure IoT 中樞，並在 IoT 中樞建立第一個裝置。

開始本課程前，請先完成第 1 課。

### <a name="get-the-azure-tools"></a>取得 Azure 工具
安裝 Azure 命令列介面 (Azure CLI)。

*預估完成時間：10 分鐘*

前往[取得 Azure 工具][get-azure-tools]。

### <a name="create-your-iot-hub-and-register-intel-edison"></a>建立 IoT 中樞並登錄 Intel Edison
建立資源群組、佈建第一個 Azure IoT 中樞，並使用 Azure CLI 將第一個裝置新增至 IoT 中樞。

*預估完成時間：10 分鐘*

前往[建立 IoT 中樞並登錄 Intel Edison](iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md)。

## <a name="lesson-3-send-device-to-cloud-messages"></a>第 3 課：傳送裝置到雲端訊息
![第 3 課端對端圖表](media/iot-hub-intel-edison-lessons/e2e-lesson3.png)

在本課中，您會將訊息從 Edison 傳送到 IoT 中樞。 您也會建立 Azure 函式應用程式，此應用程式會從 IoT 中樞取得傳入訊息，並將這些訊息寫入 Azure 表格儲存體。

開始本課程前，請先完成第 1 課和第 2 課。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>建立 Azure 函數應用程式與 Azure 儲存體帳戶
使用 Azure Resource Manager 範本來建立 Azure 函數應用程式及 Azure 儲存體帳戶。

*預估完成時間：10 分鐘*

前往[建立 Azure 函式應用程式與 Azure 儲存體帳戶][create-an-azure-function-app-and-azure-storage-account]。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>執行範例應用程式以傳送裝置到雲端訊息
將範例應用程式部署至 Intel Edison 裝置，並執行該應用程式以傳送訊息至 IoT 中樞。

*預估完成時間：10 分鐘*

前往[執行範例應用程式以傳送裝置到雲端訊息][send-device-to-cloud-messages]。

### <a name="read-messages-persisted-in-azure-storage"></a>讀取保存在 Azure 儲存體中的訊息
當裝置到雲端訊息寫入 Azure 儲存體時對其進行監視。

*預估完成時間：5 分鐘*

前往[讀取保存在 Azure 儲存體中的訊息][read-messages-persisted-in-azure-storage]。

## <a name="lesson-4-send-cloud-to-device-messages"></a>第 4 課：傳送雲端到裝置訊息
![第 4 課端對端圖表](media/iot-hub-intel-edison-lessons/e2e-lesson4.png)

本課程示範如何將訊息從 Azure IoT 中樞傳送至 Intel Edison。 這些訊息會針對與 Edison 連接的 LED，控制其開啟與關閉行為。 本課程中已為您準備好範例應用程式以完成這項工作。

開始本課程前，請先完成第 1 課、第 2 課和第 3 課。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>執行範例應用程式以接收雲端到裝置訊息
＜課程 4＞中的範例應用程式會在 Edison 上執行，並監視來自 IoT 中樞的傳入訊息。 新的 Gulp 工作會從 IoT 中樞將訊息傳送到 Edison 來使 LED 閃爍。

*預估完成時間：10 分鐘*

前往[執行範例應用程式以接收雲端到裝置訊息][receive-cloud-to-device-messages]。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>選讀區段：變更 LED 的開起與關閉行為
自訂訊息以變更 LED 的開啟與關閉行為。

*預估完成時間：10 分鐘*

前往[選讀區段：變更 LED 的開啟與關閉行為][change-the-on-and-off-behavior-of-the-led]。

## <a name="troubleshooting"></a>疑難排解
如果在課程期間遇到任何問題，您可以在[疑難排解][troubleshooting]一文中尋求解決方案。
<!-- Images and links -->

[configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[get-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-node-lesson3-read-table-storage.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-node-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
