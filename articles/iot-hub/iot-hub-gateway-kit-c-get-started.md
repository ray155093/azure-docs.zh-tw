---
title: "SensorTag 裝置與 Azure IoT 閘道 - 開始使用 | Microsoft Docs"
description: "開始使用 IoT 閘道器入門套件、建立 Azure IoT 中樞，以及將 SensorTag 和閘道器連接到 IoT 中樞"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot 中樞, iot 閘道器, 開始使用物聯網, iot 工具套件"
ms.assetid: 56d05f4e-f2c1-4b22-8701-f01e14deead6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/21/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: dd14d137c23fbccb3616b60db8a63e668ae7ea80
ms.lasthandoff: 01/25/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-sensortag"></a>開始使用 IoT 閘道器入門套件與 SensorTag

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [模擬裝置](iot-hub-gateway-kit-c-sim-get-started.md)

在本教學課程中，您會開始了解使用 [IoT 閘道器入門套件](https://aka.ms/gateway-kit)的基本知識。 您會使用執行 Wind River Linux 的 Intel NUC 和 [TI SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html#main)。 您將了解如何使用 Azure IoT 中樞讓您的裝置順暢地與雲端連線。

***
**沒有套件嗎？**按一下[這裡](https://aka.ms/gateway-kit)。 **沒有 SensorTag？** [從模擬裝置開始](iot-hub-gateway-kit-c-sim-get-started.md)或[購買 SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/?INTC=SensorTag&HQS=sensortag)
***

## <a name="lesson-1-configure-your-nuc"></a>第 1 課：設定 NUC
![第&1; 課端對端圖表](media/iot-hub-gateway-kit-lessons/e2e-lesson1.png)

在這一課，您要將套件中的 Intel NUC (Next Unit of Computing) 設定為 Azure IoT 閘道器、在 NUC 上安裝 Azure IoT 閘道器 SDK 套件，執行範例應用程式以確認閘道器的功能。

*預估完成時間：15 分鐘*

前往[將 Intel NUC 設定為 IoT 閘道器](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)

## <a name="lesson-2-create-your-iot-hub"></a>第 2 課：建立 IoT 中樞
![第&2; 課端對端圖表](media/iot-hub-gateway-kit-lessons/e2e-lesson2.png)

在這一課，您要在主機電腦上安裝工具和軟體。 然後會建立一個免費的 Azure 帳戶、佈建您的 Azure IoT 中樞，並在 IoT 中樞建立您的第一個裝置。

開始本課程前，請先完成第 1 課。

### <a name="get-the-tools"></a>取得工具
在您的主機電腦上安裝工具和軟體。

*預估完成時間：20 分鐘*

前往[取得工具](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)

### <a name="create-an-iot-hub-and-register-your-device"></a>建立 IoT 中樞並登錄您的裝置
建立資源群組、佈建第一個 Azure IoT 中樞，並使用 Azure CLI 將第一個裝置新增至 IoT 中樞。

*預估完成時間：10 分鐘*

前往[建立 IoT 中樞並登錄您的裝置](iot-hub-gateway-kit-c-lesson2-register-device.md)

## <a name="lesson-3-receive-messages-from-sensortag-and-read-messages-from-your-iot-hub"></a>第 3 課︰接收來自 SensorTag 的訊息，並從您的 IoT 中心讀取訊息
在這一課，您將使用指令碼將閘道器中 BLE 範例應用程式的設定和執行自動化。 這類應用程式使用模組集合來彙總及轉換資料的、處理命令，或執行任意數目的相關工作。 模組之間透過訊息代理程式相互通訊。 範例應用程式有 BLE 模組和 IoT 中樞模組。 BLE 模組從 BLE SensorTag 接收資料。 IoT 中樞模組會封裝收到的資料，並透過 Azure IoT 閘道器 SDK 中提供的閘道器架構將資料傳送至 IoT 中樞。

![第 3 課端對端圖表](media/iot-hub-gateway-kit-lessons/e2e-lesson3.png)

### <a name="configure-and-run-the-ble-sample-app"></a>設定和執行 BLE 範例應用程式
設定 SensorTag 與閘道器之間的連線。 然後完成設定，並執行 BLE 範例應用程式。

*預估完成時間：15 分鐘*

前往[設定和執行 BLE 範例應用程式](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

### <a name="read-messages-from-your-iot-hub"></a>讀取來自 IoT 中樞的傳入訊息
在主機電腦上執行範例程式碼，以讀取來自 IoT 中樞的傳入訊息。

*預估完成時間：15 分鐘*

前往[讀取來自 IoT 中樞的傳入訊息](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>第 4 課︰將訊息儲存到 Azure 表格儲存體
建立 Azure 函數應用程式，此應用程式會從 IoT 中樞取得傳入訊息，並將這些訊息寫入 Azure 表格儲存體。

![第 4 課端對端圖表](media/iot-hub-gateway-kit-lessons/e2e-lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>建立 Azure 函數應用程式與 Azure 儲存體帳戶
使用 Azure Resource Manager 範本來建立 Azure 函數應用程式及 Azure 儲存體帳戶。

*預估完成時間：10 分鐘*

前往[建立 Azure 函數應用程式與 Azure 儲存體帳戶](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)

### <a name="read-messages-persisted-in-azure-table-storage"></a>讀取保存在 Azure 表格儲存體中的訊息
當閘道器到雲端訊息寫入 Azure 表格儲存體時對其進行監視。

*預估完成時間：5 分鐘*

前往[讀取保存在 Azure 表格儲存體中的訊息](iot-hub-gateway-kit-c-lesson4-read-table-storage.md)。

## <a name="troubleshooting"></a>疑難排解
如果在課程期間遇到任何問題，您可以在[疑難排解](iot-hub-gateway-kit-c-troubleshooting.md)一文中尋求解決方案。

## <a name="explore-more"></a>探索更多
若要深入了解，請瀏覽 [Intel IoT 閘道器套件開發人員區域](http://software.intel.com/iot/microsoft-azure)。
