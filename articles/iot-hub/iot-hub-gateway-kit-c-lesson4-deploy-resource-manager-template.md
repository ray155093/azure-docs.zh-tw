---
title: "SensorTag 裝置與 Azure IoT 閘道 - 第 4 課：建立函數應用程式 | Microsoft Docs"
description: "將訊息從 Intel NUC 儲存到您的 IoT 中樞，然後將訊息寫入 Azure 表格儲存體，並讀取雲端中的訊息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "將資料儲存在雲端, 儲存在雲端的資料, iot 雲端服務"
ms.assetid: f84f9a85-e2c4-4a92-8969-f65eb34c194e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 3672804218dbbe3a545536b8c4baccb2b9171cdc
ms.lasthandoff: 01/25/2017


---
# <a name="create-an-azure-function-app-and-storage-account"></a>建立 Azure 函式應用程式與儲存體帳戶

Azure Functions 是可在雲端輕鬆執行「函式」(一小段程式碼) 的解決方案。 Azure 函數應用程式可在 Azure 中主控函數的執行。 

## <a name="what-you-will-do"></a>將執行的作業

- 使用 Azure Resource Manager 範本來建立 Azure 函式應用程式及 Azure 儲存體帳戶。 Azure 函式應用程式會接聽 Azure IoT 中樞事件、處理傳入訊息，並將它們寫入 Azure 表格儲存體。

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-troubleshooting.md)尋求解決方案。


## <a name="what-you-will-learn"></a>學習目標

在這一課，您將了解：

- 如何使用 Azure Resource Manager 部署 Azure 資源。
- 如何使用 Azure 函數應用程式處理 IoT 中樞訊息，並將它們寫入 Azure 表格儲存體中的資料表。

## <a name="what-you-need"></a>您需要什麼

您必須已順利完成先前的課程︰

- [第 1 課：將 Intel NUC 設定為 IoT 閘道器](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
- [第 2 課：準備好您的主機電腦和 Azure IoT 中樞](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
- [第 3 課︰接收來自 SensorTag 的訊息，並讀取來自 IoT 中樞的傳入訊息](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

## <a name="open-a-sample-app"></a>開啟範例應用程式

前往您的 `iot-hub-c-intel-nuc-gateway-getting-started` 存放庫資料夾，初始化組態檔，然後執行下列命令在 Visual Studio Code 中開啟範例專案︰

```bash
cd Lesson4
npm install
gulp init
code .
```

![存放庫結構](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- `arm-template.json` 檔案為包含 Azure 函式應用程式及 Azure 儲存體帳戶的 Azure Resource Manager 範本。
- `arm-template-param.json` 檔案是 Azure Resource Manager 範本使用的組態檔。
- `ReceiveDeviceMessages` 子資料夾包含 Azure 函數的 Node.js 程式碼。

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>在 Azure 中設定 Azure Resource Manager 範本並建立資源

更新 Visual Studio Code 中的 `arm-template-param.json` 檔案。

![arm 範本 json](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- 將 `[your IoT Hub name]` 取代為您在第 2 課指定的 `{my hub name}`。

在您更新 `arm-template-param.json` 檔案後，請執行下列命令來將資源部署到 Azure：

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

如果您在第 2 課中沒有變更值，請使用 `iot-gateway` 作為 `{resource group name}` 的值。

## <a name="summary"></a>摘要

您已建立 Azure 函式應用程式以處理 IoT 中樞訊息，並建立 Azure 儲存體帳戶以儲存這些訊息。 您現在可以讀取由您的閘道器傳送至 IoT 中樞的訊息。

## <a name="next-steps"></a>後續步驟
[讀取保存在 Azure 儲存體中的訊息](iot-hub-gateway-kit-c-lesson4-read-table-storage.md)。

