---
title: "模擬裝置與 Azure IoT 閘道 - 開始使用 | Microsoft Docs"
description: "開始使用 IoT 閘道器入門套件，建立 Azure IoT 中樞，並將閘道器連接到 IoT 中樞"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot 中樞, iot 閘道器, 開始使用物聯網, iot 工具套件"
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 06161c2950fbe99d2e4f9c1b416695a5f9f18ca4
ms.lasthandoff: 01/25/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-simulated-device"></a>開始使用 IoT 閘道器入門套件與模擬裝置

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [模擬裝置](iot-hub-gateway-kit-c-sim-get-started.md)

在本教學課程中，您會開始了解使用 [IoT 閘道器入門套件](https://aka.ms/gateway-kit)的基本知識。 您將使用執行 Wind River Linux 的 Intel NUC。 您將了解如何使用 Azure IoT 中樞讓您的裝置順暢地與雲端連線。

***
**沒有套件嗎？**按一下[這裡](https://aka.ms/gateway-kit)。
***

## <a name="lesson-1-configure-your-nuc"></a>第 1 課：設定 NUC
![第&1; 課端對端圖表](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

在這一課，您要將套件中的 Intel NUC (Next Unit of Computing) 設定為 Azure IoT 閘道器、在 NUC 上安裝 Azure IoT 閘道器 SDK 套件，執行範例應用程式以確認閘道器的功能。

*預估完成時間：15 分鐘*

前往[將 Intel NUC 設定為 IoT 閘道器](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md)

## <a name="lesson-2-create-your-iot-hub"></a>第 2 課：建立 IoT 中樞
![第&2; 課端對端圖表](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

在這一課，您要在主機電腦上安裝工具和軟體。 然後會建立一個免費的 Azure 帳戶、佈建您的 Azure IoT 中樞，並在 IoT 中樞建立您的第一個裝置。

開始本課程前，請先完成第 1 課。

### <a name="get-the-tools"></a>取得工具
在您的主機電腦上安裝工具和軟體。

*預估完成時間：20 分鐘*

前往[取得工具](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)

### <a name="create-an-iot-hub-and-register-your-device"></a>建立 IoT 中樞並登錄您的裝置
建立資源群組、佈建第一個 Azure IoT 中樞，並使用 Azure CLI 將第一個裝置新增至 IoT 中樞。

*預估完成時間：10 分鐘*

前往[建立 IoT 中樞並登錄您的裝置](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub"></a>第 3 課︰接收來自模擬裝置的訊息，並讀取來自 IoT 中樞的傳入訊息
在這一課，您將使用指令碼將閘道器中模擬裝置的設定和執行自動化。 模擬裝置應用程式會產生範例溫度資料，並將它傳送到 IoT 中樞模組。 IoT 中樞模組會封裝收到的資料，並透過 Azure IoT 閘道器 SDK 中提供的閘道器架構將資料傳送至 IoT 中樞。

![第 3 課端對端圖表](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

### <a name="configure-and-run-a-simulated-device"></a>設定並執行模擬裝置
準備範例程式碼。 然後設定和執行模擬裝置範例應用程式。

*預估完成時間：15 分鐘*

前往[設定並執行模擬裝置](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)

### <a name="read-messages-from-your-iot-hub"></a>讀取來自 IoT 中樞的傳入訊息
在主機電腦上執行範例程式碼，以讀取來自 IoT 中樞的傳入訊息。

*預估完成時間：15 分鐘*

前往[讀取來自 IoT 中樞的傳入訊息](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>第 4 課︰將訊息儲存到 Azure 表格儲存體
建立 Azure 函數應用程式，此應用程式會從 IoT 中樞取得傳入訊息，並將這些訊息寫入 Azure 表格儲存體。

![第 4 課端對端圖表](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>建立 Azure 函數應用程式與 Azure 儲存體帳戶
使用 Azure Resource Manager 範本來建立 Azure 函數應用程式及 Azure 儲存體帳戶。

*預估完成時間：10 分鐘*

前往[建立 Azure 函數應用程式與 Azure 儲存體帳戶](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)

### <a name="read-messages-persisted-in-azure-table-storage"></a>讀取保存在 Azure 表格儲存體中的訊息
當閘道器到雲端訊息寫入 Azure 表格儲存體時對其進行監視。

*預估完成時間：5 分鐘*

前往[讀取保存在 Azure 表格儲存體中的訊息](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md)。

## <a name="troubleshooting"></a>疑難排解
如果在課程期間遇到任何問題，您可以在[疑難排解](iot-hub-gateway-kit-c-sim-troubleshooting.md)一文中尋求解決方案。

## <a name="explore-more"></a>探索更多
若要深入了解，請瀏覽 [Intel IoT 閘道器套件開發人員區域](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit)。
