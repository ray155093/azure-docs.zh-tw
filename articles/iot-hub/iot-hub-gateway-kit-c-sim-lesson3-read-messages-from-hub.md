---
title: "讀取來自 Azure IoT 中樞的傳入訊息 | Microsoft Docs"
description: "在主機電腦上執行範例程式碼，以讀取來自 IoT 中樞的傳入訊息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "在雲端的資料, 雲端資料收集, iot 雲端服務, iot 資料"
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: c92b9a86ceee306f713df5bcffca87071c905393


---

# <a name="read-messages-from-your-iot-hub"></a>讀取來自 IoT 中樞的傳入訊息

## <a name="what-you-will-do"></a>將執行的作業

- 在主機電腦上執行範例程式碼，以讀取來自 IoT 中樞的傳入訊息。

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-sim-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標

如何使用 gulp 工具讀取來自 IoT 中樞的傳入訊息。

## <a name="what-you-need"></a>您需要什麼

- [設定及執行模擬裝置雲端上傳範例應用程式](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)中的模擬裝置。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>取得 IoT 中樞與裝置連接字串

您的模擬裝置會使用此裝置連接字串連接到您的 IoT 中樞。 IoT 中樞連接字串是用來連接到 IoT 中樞中的身分識別登錄，以便管理可連接到 IoT 中樞的裝置。

- 執行下列命令，列出您的資源群組中的所有 IoT 中樞：

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   如果您未變更值，請使用 `iot-gateway` 作為 `{resource group name}` 的值。
- 執行下列命令來取得 IoT 中樞連接字串：

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` 是您在第 2 課中指定的名稱。

## <a name="configure-the-device-connection-for-the-sample-code"></a>設定範例應用程式的裝置連線

執行下列步驟，更新 `config-azure.json` 中 IoT 中樞與裝置連線的組態︰

1. 在主控台視窗中執行下列命令，在 Visual Studio Code 中開啟 `config-azure.json`︰

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. 在 `config-azure.json` 檔案中進行下列取代：

   ![設定 azure 的螢幕擷取畫面](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   將 `[IoT hub connection string]` 取代為 IoT 中樞連接字串。

## <a name="read-messages-from-your-iot-hub"></a>讀取來自 IoT 中樞的傳入訊息

執行模擬裝置範例應用程式，並使用下列命令讀取 IoT 中樞訊息︰

```bash
gulp run --iot-hub
```

此命令會執行此應用程式，應用程式每 2 秒會將訊息傳送至 IoT 中樞。 它也會繁衍子程序來接收訊息。

傳送和接收的所有訊息皆會立即顯示在主機電腦的同一主控台視窗中。 應用程式會在 40 秒後結束。

![模擬的範例應用程式及傳送和接收的訊息](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

## <a name="summary"></a>摘要

您已成功執行範例應用程式，以模擬裝置將資料傳送至您的 IoT 中樞。 您也已經讀取傳送至 IoT 中樞的訊息。

## <a name="next-steps"></a>後續步驟
[建立 Azure 函數應用程式與 Azure 儲存體帳戶](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Dec16_HO3-->


