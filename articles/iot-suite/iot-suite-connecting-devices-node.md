---
title: "使用 Node.js 連接裝置 |Microsoft Docs"
description: "描述如何使用 Node.js 中已寫入的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 02247d679c9bffa01b8d2827ea57eba38894107e
ms.openlocfilehash: 698384bb5079feb937f423f9b6365dff20cfa3be


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>將裝置連接至遠端監視預先設定方案 (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>建置並執行 node.js 範例解決方案
1. 若要複製 *Microsoft Azure IoT SDK for Node.js* GitHub 儲存機制，並將其安裝在桌面環境中，請遵循[準備您的開發環境][lnk-github-prepare]指示。
2. 從 [azure-iot-sdk-node][lnk-github-repo] 儲存機制的本機複本，將下列兩個檔案從 device/samples 資料夾複製到您裝置的資料夾中：
   
   * package.json
   * remote_monitoring.js
3. 開啟 remote_monitoring.js 檔案並尋找下列變數：
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
4. 以您裝置的連接字串取代 **[IoT 中樞裝置連接字串]** 。 您可以在遠端監視方案的儀表板中找到 IoT 中樞主機名稱、裝置識別碼和裝置金鑰。 裝置連接字串使用的格式如下：
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    如果 IoT 中樞主機名稱是 **contoso** 且裝置識別碼是 **mydevice**，連接字串看起來如下：
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
5. 儲存檔案。 在命令提示字元中，於包含這些檔案的資料夾，執行下列命令以安裝必要的套件，然後執行範例應用程式：
   
    ```
    npm install --save
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md



<!--HONumber=Dec16_HO3-->


