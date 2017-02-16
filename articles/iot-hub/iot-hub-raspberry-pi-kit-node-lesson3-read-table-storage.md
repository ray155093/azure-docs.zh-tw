---
featureFlags:
- usabilla
title: "將 Raspberry Pi (節點) 連接到 Azure IoT - 第 3 課：表格儲存體 | Microsoft Docs"
description: "當裝置到雲端訊息寫入您的 Azure 表格儲存體時對其進行監視。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "從雲端擷取資料, iot 雲端服務"
ms.assetid: 9965bd54-61da-479b-aaad-5604850a2be5
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 0e35ffda2d5f6698c4e9d96f1991998b36a7f230


---
# <a name="read-messages-persisted-in-azure-storage"></a>讀取保存在 Azure 儲存體中的訊息
## <a name="what-you-will-do"></a>將執行的作業
當從 Raspberry Pi 3 傳送至 IoT 中樞的裝置到雲端訊息寫入您的 Azure 表格儲存體時，對其進行監視。 如果您有任何問題，請在[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解如何使用 gulp 讀取訊息工作來讀取保留在您的 Azure 表格儲存體中的訊息。

## <a name="what-you-need"></a>您需要什麼
開始此程序之前，您必須已成功完成[在 Raspberry Pi 3 中執行 Azure 閃爍範例應用程式](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)。

## <a name="read-new-messages-from-your-storage-account"></a>從您的儲存體帳戶讀取新訊息
在上一篇文章中，您在 Pi 上執行範例應用程式。 範例應用程式會將訊息傳送至 Azure IoT 中樞。 傳送至 IoT 中樞的訊息會透過 Azure 函式應用程式儲存至您的 Azure 表格儲存體。 您需要 Azure 儲存體連接字串，來讀取您的 Azure 表格儲存體中的訊息。

若要讀取 Azure 表格儲存體中儲存的訊息，請遵循下列步驟︰

1. 執行下列命令來取得連接字串：

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   第一個命令會擷取 `storage name`，在第二個命令中用來取得連接字串。 如果您未變更值，請使用 `iot-sample` 作為 `{resource group name}` 的值。
2. 在 Visual Studio Code 中開啟組態檔 `config-raspberrypi.json`，方法是執行下列命令︰

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
3. 將 `[Azure storage connection string]` 取代為您在步驟 1 中取得的連接字串。
4. 儲存 `config-raspberrypi.json` 檔案。
5. 重新傳送訊息並且從您的 Azure 表格儲存體中讀取它們，方法是執行下列命令︰
   
   ```bash
   gulp run --read-storage
   ```
   
   從 Azure 表格儲存體讀取的邏輯是在 `azure-table.js` 檔案中。
   
    ![gulp run --read-storage](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message.png)

## <a name="summary"></a>摘要
您已成功將 Pi 連接至雲端中的 IoT 中樞，並且使用閃爍範例應用程式以傳送裝置到雲端訊息。 您也會使用 Azure 函式應用程式，將內送 IoT 中樞訊息儲存至 Azure 表格儲存體。 現在，您可以從 IoT 中樞將雲端到裝置訊息傳送至 Pi。

## <a name="next-steps"></a>後續步驟
[執行範例應用程式以接收雲端到裝置訊息](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)




<!--HONumber=Jan17_HO4-->


