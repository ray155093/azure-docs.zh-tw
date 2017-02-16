---
title: "建立 Azure 函式應用程式和 Azure 儲存體帳戶 | Microsoft Docs"
description: "Azure 函式應用程式會接聽 Azure IoT 中樞事件、處理傳入訊息，並將它們寫入 Azure 表格儲存體。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "將資料儲存在雲端, 儲存在雲端的資料, iot 雲端服務"
ms.assetid: 6c58de85-c5c4-4989-bb5e-08c45c549966
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: df446b1a6b244761f39bfdcebb7e404435c2c35d


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>建立 Azure 函式應用程式與 Azure 儲存體帳戶
[Azure Functions](../azure-functions/functions-overview.md) 是可在雲端輕鬆執行「函式」(一小段程式碼) 的解決方案。 Azure 函數應用程式可在 Azure 中主控函數的執行。

## <a name="what-you-will-do"></a>將執行的作業
使用 Azure Resource Manager 範本來建立 Azure 函式應用程式及 Azure 儲存體帳戶。 Azure 函式應用程式會接聽 Azure IoT 中樞事件、處理傳入訊息，並將它們寫入 Azure 表格儲存體。 如果您有任何問題，請在[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：

* 如何使用 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 部署 Azure 資源。
* 如何使用 Azure 函式應用程式處理 IoT 中樞訊息，並將它們寫入 Azure 表格儲存體中的表格。

## <a name="what-you-need"></a>您需要什麼
您必須已成功完成：
* [開始使用 Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md)
* [建立您的 Azure IoT 中樞](iot-hub-raspberry-pi-kit-node-get-started.md)

## <a name="open-the-sample-app"></a>開啟範例應用程式
執行下列命令來在 Visual Studio Code 中開啟範例專案︰

```bash
cd Lesson3
code .
```

![儲存機制結構](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

* `app` 子資料夾中的 `app.js` 檔案是關鍵的來源檔案。 這個來源檔案包含要將訊息傳送到 IoT 中樞 20 次，並於每次傳送訊息時使 LED 閃爍的程式碼。
* `arm-template.json` 檔案為包含 Azure 函式應用程式及 Azure 儲存體帳戶的 Azure Resource Manager 範本。
* `arm-template-param.json` 檔案是 Azure Resource Manager 範本使用的組態檔。
* `ReceiveDeviceMessages` 子資料夾包含 Azure 函數的 Node.js 程式碼。

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>在 Azure 中設定 Azure Resource Manager 範本並建立資源
更新 Visual Studio Code 中的 `arm-template-param.json` 檔案。

![Azure Resource Manager 範本參數](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

* 將 **[your IoT Hub name]** 替換為您在[建立 IoT 中樞並登錄 Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md) 時所指定的 **{my hub name}**。
* 將 **[prefix string for new resources]** 以您想要的任何前置詞取代。 前置詞能確保資源名稱為全域唯一以避免衝突。 請不要在前置詞中使用破折號或字首數字。

在您更新 `arm-template-param.json` 檔案後，請執行下列命令來將資源部署到 Azure：

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

建立這些資源大概需要五分鐘的時間。 當資源建立正在進行時，您可以移至下一篇文章。

## <a name="summary"></a>摘要
您已建立 Azure 函式應用程式以處理 IoT 中樞訊息，並建立 Azure 儲存體帳戶以儲存這些訊息。 您現在可以部署並執行範例，以在 Pi 上傳送「裝置到雲端」訊息。

## <a name="next-steps"></a>後續步驟
[執行範例應用程式以在 Raspberry Pi 3 上傳送「裝置到雲端」訊息](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)




<!--HONumber=Nov16_HO5-->


