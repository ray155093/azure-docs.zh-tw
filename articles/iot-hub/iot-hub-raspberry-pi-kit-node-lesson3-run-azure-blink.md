---
title: "將裝置到雲端訊息傳送至 Azure IoT 中樞 | Microsoft Docs"
description: "將範例應用程式部署至 Raspberry Pi 3 裝置，並執行該應用程式以傳送訊息至 IoT 中樞並讓 LED 閃爍。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "閃爍 led 雲端 pi, 來自雲端的 led 閃爍"
ms.assetid: 427d8e5e-8af8-4249-8607-44edc90a4972
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: c0ddd7e68accf28ee65ba70ea992acd413da6f5b
ms.openlocfilehash: ac844b94151c4f9f5105ece63075529779f0efb6


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>執行範例應用程式以傳送裝置到雲端訊息
## <a name="what-you-will-do"></a>將執行的作業
本文將說明如何將範例應用程式部署至 Raspberry Pi 3，並執行該應用程式以傳送訊息至 IoT 中樞。 如果您有任何問題，請在[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
您將了解如何使用 gulp 工具將範例 Node.js 應用程式部署在 Pi 上並執行。

## <a name="what-you-need"></a>您需要什麼
* 開始這項工作之前，您必須先成功完成[建立 Azure 函式應用程式與儲存體帳戶以處理與儲存 IoT 中樞訊息](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>取得 IoT 中樞與裝置連接字串
Pi 使用此裝置連接字串來連接至 IoT 中樞。 IoT 中樞連接字串是用來連接到 IoT 中樞中的身分識別登錄，以便管理可連接到 IoT 中樞的裝置。 

* 執行下列 Azure CLI 命令，列出您的資源群組中的所有 IoT 中樞：

```bash
az iot hub list -g iot-sample --query [].name
```

如果您未變更值，請使用 `iot-sample` 作為 `{resource group name}` 的值。

* 執行下列 Azure CLI 命令取得 IoT 中樞連接字串：

```bash
az iot hub show-connection-string --name {my hub name} -g iot-sample
```

`{my hub name}` 是您建立 IoT 中樞並登錄 Pi 時所指定的名稱。

* 執行下列命令來取得裝置連接字串：

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myraspberrypi -g iot-sample
```

如果您未變更值，請使用 `myraspberrypi` 作為 `{device id}` 的值。

## <a name="configure-the-device-connection"></a>設定裝置連線
1. 執行下列命令初始化組態檔：
   
   ```bash
   npm install
   gulp init
   ```
2. 執行下列命令在 Visual Studio Code 中開啟裝置組態檔 `config-raspberrypi.json`：
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
  
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
  
   ![config.json](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)
3. 在 `config-raspberrypi.json` 檔案中進行下列取代：
   
   * 以您從 `device-discovery-cli` 取得的裝置 IP 位置或主機名稱，或以設定裝置時所繼承的值，來取代 **[device hostname or IP address]**。
   * 以您取得的 `device connection string` 來取代 **[IoT device connection string]**。
   * 以您取得的 `iot hub connection string` 來取代 **[IoT hub connection string]**。

更新 `config-raspberrypi.json` 檔案，讓您能夠從電腦部署範例應用程式。

## <a name="deploy-and-run-the-sample-application"></a>部署和執行範例應用程式
執行下列命令，以在 Pi 上部署和執行範例應用程式：

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>確認範例應用程式可運作
您應該會看到與 Pi 連接的 LED 每兩秒閃爍一次。 每次 LED 閃爍時，範例應用程式就會將訊息傳送至 IoT 中樞，並驗證該訊息已成功傳送至 IoT 中樞。 此外，IoT 中樞所收到的每則訊息都會列印在主控台視窗中。 在傳送 20 則訊息後，範例應用程式會自動終止。

![具有所傳送和接收之訊息的範例應用程式](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="summary"></a>摘要
您已在 Pi 上部署新的閃爍範例應用程式，並執行該應用程式以將裝置到雲端訊息傳送至 IoT 中樞。 現在，您可以在訊息寫入儲存體帳戶時加以監視。

## <a name="next-steps"></a>後續步驟
[讀取保留在 Azure 儲存體中的訊息](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)




<!--HONumber=Dec16_HO1-->


