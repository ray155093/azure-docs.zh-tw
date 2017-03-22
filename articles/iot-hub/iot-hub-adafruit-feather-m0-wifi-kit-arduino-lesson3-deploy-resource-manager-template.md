---
title: "將 Arduino (C) 連接到 Azure IoT - 第 3 課：範本部署 | Microsoft Docs"
description: "Azure 函式應用程式會接聽 Azure IoT 中樞事件、處理傳入訊息，並將它們寫入 Azure 表格儲存體。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "將資料儲存在雲端, 儲存在雲端的資料, iot 雲端服務"
ms.assetid: 9c8f4cd1-9511-4601-ad7e-51761a986753
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: cf666d17406a49036082a6a4dd15bad89c04aa8f
ms.lasthandoff: 01/24/2017


---

# <a name="create-an-azure-function-app-and-azure-storage-account"></a>建立 Azure 函式應用程式與 Azure 儲存體帳戶
[Azure Functions](../../articles/azure-functions/functions-overview.md) 是可在雲端輕鬆執行「函式」(一小段程式碼) 的解決方案。 Azure 函數應用程式可在 Azure 中主控函數的執行。

## <a name="what-will-you-do"></a>您將會怎麼做
使用 Azure Resource Manager 範本來建立 Azure 函式應用程式及 Azure 儲存體帳戶。 Azure 函式應用程式會接聽 Azure IoT 中樞事件、處理傳入訊息，並將它們寫入 Azure 表格儲存體。

如果您有任何問題，請在 [Adafruit Feather M0 WiFi Arduino 面版的疑難排解頁面](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)尋求解決方案。

## <a name="what-will-you-learn"></a>您將學到什麼
在本文中，您將了解：
* 如何使用 [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) 部署 Azure 資源。
* 如何使用 Azure 函式應用程式處理 IoT 中樞訊息，並將它們寫入 Azure 表格儲存體中的表格。

## <a name="what-do-you-need"></a>您需要什麼
您必須已成功完成：
- [開始使用您的 Arduino 面板][get-started]
- [建立您的 Azure IoT 中樞][create-iot-hub]

## <a name="open-the-sample-app"></a>開啟範例應用程式
執行下列命令來在 Visual Studio Code 中開啟範例專案︰

```bash
cd Lesson3
code .
```

![儲存機制結構][repo-structure]

* `app` 子資料夾中的 `app.ino` 檔案是關鍵的來源檔案。 這個來源檔案包含要將訊息傳送到 IoT 中樞 20 次，並於每次傳送訊息時使 LED 閃爍的程式碼。
* `config.json`包含必要的組態設定。
* `arm-template.json` 檔案為包含 Azure 函式應用程式及 Azure 儲存體帳戶的 Azure Resource Manager 範本。
* `arm-template-param.json` 檔案是 Azure Resource Manager 範本使用的組態檔。
* `ReceiveDeviceMessages` 子資料夾包含 Azure 函數的 Node.js 程式碼。

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>在 Azure 中設定 Azure Resource Manager 範本並建立資源
更新 Visual Studio Code 中的 `arm-template-param.json` 檔案。

![Azure Resource Manager 範本參數][arm-template-params]

* 將 **[your IoT Hub name]** 替換為您在[建立 IoT 中樞並註冊您的 Arduino 面板][created-iot-hub-and-registered-arduino-board]時所指定的 **{my hub name}**。
* 將 **[prefix string for new resources]** 以您想要的任何前置詞取代。 前置詞能確保資源名稱為全域唯一以避免衝突。 請不要在前置詞中使用破折號或字首數字。

在您更新 `arm-template-param.json` 檔案後，請執行下列命令來將資源部署到 Azure：

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

建立這些資源大概需要五分鐘的時間。 當資源建立正在進行時，您可以移至下一篇文章。

## <a name="summary"></a>摘要
您已建立 Azure 函式應用程式以處理 IoT 中樞訊息，並建立 Azure 儲存體帳戶以儲存這些訊息。 您現在可以部署並執行範例，以在 Arduino 面板上傳送裝置到雲端訊息。

## <a name="next-steps"></a>後續步驟
[執行範例應用程式以在 Arduino 面板上傳送裝置到雲端訊息][send-device-to-cloud-messages]

<!-- Images and links -->

[get-started]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[create-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/repo_structure_c.png
[arm-template-params]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/arm_para_arduino.png
[created-iot-hub-and-registered-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
