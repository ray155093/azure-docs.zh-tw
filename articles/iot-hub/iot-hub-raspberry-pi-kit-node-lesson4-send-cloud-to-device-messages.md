---
featureFlags:
- usabilla
title: "將 Raspberry Pi (節點) 連接到 Azure IoT - 第 4 課：雲端到裝置 | Microsoft Docs"
description: "範例應用程式會在 Pi 上執行，並監視來自 IoT 中樞的傳入訊息。 新的 Gulp 工作會從 IoT 中樞將訊息傳送到 Pi 來使 LED 閃爍。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "雲端到裝置, 來自雲端的訊息"
ms.assetid: 6ae6539e-1163-4490-8d72-fdf7803e3054
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 92ee9d6faae9f539c663395e47714609a146f2df


---
# <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>執行範例應用程式以接收雲端到裝置訊息
在本文中，您將在 Raspberry Pi 3 上部署範例應用程式。 範例應用程式會監視來自 IoT 中樞的傳入訊息。 您也會在電腦上執行 Gulp 工作，以從 IoT 中樞將訊息傳送到 Pi。 當範例應用程式收到訊息時，便會使 LED 閃爍。 如果您有任何問題，請在[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-do"></a>將執行的作業
* 將範例應用程式連接到 IoT 中樞。
* 部署並執行範例應用程式。
* 從 IoT 中樞將訊息傳送到 Pi 來使 LED 閃爍。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：
* 如何監視來自 IoT 中樞的傳入訊息
* 如何從 IoT 中樞將「雲端到裝置」訊息傳送到 Pi。

## <a name="what-you-need"></a>您需要什麼
* Raspberry Pi 3，以完成使用設定。 若要了解如何設定 Pi，請參閱[設定裝置](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)。
* 在您的 Azure 訂用帳戶中建立的 IoT 中樞。 若要了解如何建立 IoT 中樞，請參閱[建立 IoT 中樞並登錄 Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)。

## <a name="connect-the-sample-application-to-your-iot-hub"></a>將範例應用程式連接到 IoT 中樞
1. 請確定您已位於存放庫資料夾 `iot-hub-node-raspberrypi-getting-started`。 執行下列命令來在 Visual Studio Code 中開啟範例應用程式︰
   
   ```bash
   cd Lesson4
   code .
   ```
   
   請注意到 `app` 子資料夾中的 `app.js` 檔案。 `app.js` 檔案為關鍵的來源檔案，這個來源檔案包含監視來自 IoT 中樞之傳入訊息的程式碼。 `blinkLED` 函數會使 LED 閃爍。
   
   ![範例應用程式中的儲存機制結構](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)
2. 使用下列命令來初始化組態檔：
   
   ```bash
   npm install
   gulp init
   ```
   
   如果您已在此電腦上完成[建立 Azure 函式應用程式與儲存體帳戶](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)中的步驟，則會繼承所有組態，因此您可以略過部署和執行範例應用程式的工作。 如果您已在不同電腦上完成[建立 Azure 函式應用程式與儲存體帳戶](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)中的步驟，您必須取代 `config-raspberrypi.json` 檔案中的預留位置。 `config-raspberrypi.json` 檔案位於您主資料夾的子資料夾中。
   
   ![config-raspberrypi.json 檔案的內容](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* 以透過執行 `devdisco list --eth` 命令所取得的 Pi IP 位址或主機名稱取代 **[device hostname or IP address]**。
* 以透過執行 `az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}` 命令所取得的裝置連接字串取代 **[IoT device connection string]**。
* 以透過執行 `az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}` 命令所取得的 IoT 中樞連接字串取代 **[IoT hub connection string]**。

> [!NOTE]
> 也請執行 **gulp install-tools** (如果您未在第 1 課這麼做)。

## <a name="deploy-and-run-the-sample-application"></a>部署和執行範例應用程式
執行下列命令，以在 Pi 上部署和執行範例應用程式：

```bash
gulp deploy && gulp run
```

命令會將範例應用程式部署到 Pi。 然後，它會在 Pi 上執行應用程式，並在主機電腦上執行個別的工作，以從 IoT 中樞將 20 個閃爍訊息傳送到 Pi。

在範例應用程式執行後，它便會開始接聽來自 IoT 中樞的訊息。 同時，gulp 工作會從 IoT 中樞將數個「閃爍」訊息傳送到 Pi。 每當 Pi 收到閃爍訊息時，範例應用程式便會呼叫 `blinkLED` 函式來使 LED 閃爍。

當 gulp 工作從 IoT 中樞將 20 個訊息傳送到 Pi 時，LED 每兩秒應該會閃爍一次。 最後將會有一個「停止」訊息，來告訴應用程式停止執行。

![具有 gulp 命令和閃爍訊息的範例應用程式](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="summary"></a>摘要
您已成功從 IoT 中樞將訊息傳送到 Pi 來使 LED 閃爍。 下一個工作是「選讀：變更 LED 的開與關行為」。

## <a name="next-steps"></a>後續步驟
[變更 LED 的開與關行為](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)




<!--HONumber=Jan17_HO4-->


