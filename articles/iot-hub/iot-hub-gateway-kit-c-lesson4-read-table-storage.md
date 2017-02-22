---
title: "SensorTag 裝置與 Azure IoT 閘道 - 第 4 課：表格儲存體 | Microsoft Docs"
description: "將訊息從 Intel NUC 儲存到您的 IoT 中樞，然後將訊息寫入 Azure 表格儲存體，並讀取雲端中的訊息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "從雲端擷取資料, iot 雲端服務"
ms.assetid: 8ca78045-ad92-4a6a-90f1-05f9668e6f0e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 293343e5db58bebc8c2e27d54d6396212fe6d3a1


---

# <a name="read-messages-persisted-in-azure-table-storage"></a>讀取保存在 Azure 表格儲存體中的訊息

## <a name="what-you-will-do"></a>將執行的作業

- 在您的閘道器上執行閘道器範例應用程式，將訊息傳送至您的 IoT 中樞。
- 然後在您的主機電腦上執行範例程式碼，以讀取 Azure表格儲存體中的訊息。 

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標

如何使用 gulp 工具執行範例應用程式，以讀取在您的 Azure 表格儲存體中的訊息。

## <a name="what-you-need"></a>您需要什麼

您已成功完成下列工作︰

- [建立 Azure 函數應用程式與 Azure 儲存體帳戶](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)。
- [執行閘道器範例應用程式](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)。
- [讀取來自 IoT 中樞的傳入訊息](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)。

## <a name="get-your-azure-storage-connection-strings"></a>取得您的 Azure 儲存體連接字串

稍早在本課程中，您已成功建立 Azure 儲存體帳戶。 若要取得 Azure 儲存體帳戶的連接字串，請執行下列命令：

* 列出您的所有儲存體帳戶。

```bash
az storage account list -g iot-gateway --query [].name
```

* 取得 Azure 儲存體連接字串。

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

如果您在第 2 課中沒有變更值，請使用 iot-gateway 作為 `{resource group name}` 的值。

## <a name="configure-the-device-connection"></a>設定裝置連線

更新 `config-azure.json` 檔案，使在主機電腦上執行的範例程式碼可以讀取 Azure 表格儲存體中的訊息。 若要設定裝置連線，請遵循下列步驟：

1. 執行下列命令來產生裝置組態檔 `config-azure.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![組態](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. 將 `[Azure storage connection string]` 取代為您取得的 Azure 儲存體連接字串。

   `[IoT hub connection string]` 應該已經在第&3; 課的[讀取來自 Azure IoT 中樞的傳入訊息](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)中被取代。

## <a name="read-messages-in-your-azure-table-storage"></a>讀取 Azure 表格儲存體中的訊息

執行閘道器範例應用程式，並使用下列命令讀取 Azure 表格儲存體訊息︰

```bash
gulp run --table-storage
```

您的 IoT 中樞會觸發您的 Azure 函數應用程式，當有新訊息進來時，後者會將訊息儲存到您的 Azure 表格儲存體。
`gulp run` 命令會執行閘道器範例應用程式將訊息傳送至 IoT 中樞。 使用 `table-storage` 參數，它也會繁衍子程序來接收儲存在 Azure表格儲存體中的訊息。

傳送和接收的所有訊息皆會立即顯示在主機電腦的同一主控台視窗中。 範例應用程式執行個體會在 40 秒後自動終止。

   ![gulp 讀取](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table.png)


## <a name="summary"></a>摘要

您已執行範例程式碼來讀取由您的 Azure 函數應用程式儲存在您的 Azure 表格儲存體中的訊息。


<!--HONumber=Jan17_HO4-->


