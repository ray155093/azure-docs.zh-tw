---
title: "執行模擬裝置範例應用程式將資料傳送至 Azure IoT 中樞 | Microsoft Docs"
description: "執行模擬裝置範例應用程式將溫度資料傳送至您的 Azure IoT 中樞"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "資料送至雲端"
ms.assetid: 5d051d99-9749-4150-b3c8-573b0bda9c52
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: 94bffa363f7979b7dc37363328a358f0b139307d


---
# <a name="configure-and-run-a-simulated-device-sample-app"></a>設定並執行模擬裝置範例應用程式

## <a name="what-you-will-do"></a>將執行的作業

- 複製範例存放庫。
- 使用 Azure CLI 取得模擬裝置範例應用程式的 IoT 中樞和邏輯裝置資訊。 設定及執行模擬裝置範例應用程式。

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-sim-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標

在本文中，您將了解：

- 如何設定及執行模擬裝置範例應用程式。

## <a name="what-you-need"></a>您需要什麼

您必須已成功完成

- [建立 IoT 中樞並登錄您的裝置](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>將範例存放庫複製至主機電腦

若要複製範例存放庫，在主機電腦上遵循下列步驟︰

1. 在 Windows 上開啟命令提示字元，或在 macOS 或 Ubuntu 上開啟終端機。
2. 執行以下命令：

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="configure-the-simulated-device-and-your-nuc"></a>設定模擬裝置和您的 NUC

1. 在 Visual Studio Code 中開啟組態檔 `config.json`，方法是執行下列命令︰

   ```bash
   code config.json
   ```

2. 將 `"has_sensortag": true` 取代為 `"has_sensortag": false`

   ![設定為您沒有 TI SensorTag 裝置](media/iot-hub-gateway-kit-lessons/lesson3/config_no_sensortag.png)

3. 執行下列命令初始化組態檔：

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

4. 執行下列命令在 Visual Studio Code 中開啟 `config-gateway.json`︰

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

5. 找到下列這行程式碼，並將 `[device hostname or IP address]` 取代為 Intel NUC 的 IP 位址或主機名稱。
   ![設定閘道器的螢幕擷取畫面](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

## <a name="get-the-connection-string-of-your-iot-hub-logical-device"></a>取得 IoT 中樞邏輯裝置的連接字串

若要取得邏輯裝置的 Azure IoT 中樞連接字串，在主機電腦上執行下列命令︰

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` 是您所使用的 IoT 中樞名稱。 如果您在第 2 課沒有變更值，使用 iot-gateway 作為 `{resource group name}` 的值，使用 mydevice 作為 `{device id}` 的值。

## <a name="configure-the-simulated-device-cloud-upload-sample-application"></a>設定模擬裝置雲端上傳範例應用程式

若要設定及執行模擬裝置雲端上傳範例應用程式，在主機電腦上執行下列步驟︰

1. 執行下列命令在 Visual Studio Code 中開啟 `config-sensortag.json`︰

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![設定 sensortag 的螢幕擷取畫面](media/iot-hub-gateway-kit-lessons/lesson3/config_simulated_device.png)

2. 取代程式碼中的下列內容：
   - 將 `[IoT hub name]` 取代為 IoT 中樞名稱。
   - 將 `[IoT device connection string]` 取代為您的 IoT 中樞邏輯裝置的連接字串。

3. 執行應用程式。

   執行下列命令，部署及執行應用程式：

   ```bash
   gulp run
   ```

## <a name="verify-the-sample-application-works"></a>確認範例應用程式可運作

您現在應該會看到如下的輸出：

![模擬裝置範例應用程式的輸出](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_simudev.png)

應用程式會將溫度資料傳送至您的 IoT 中樞，持續 40 秒。

## <a name="summary"></a>摘要

您已成功設定及執行模擬裝置雲端上傳範例應用程式，此程式會以模擬裝置將資料傳送至您的 IoT 中樞。

## <a name="next-steps"></a>後續步驟
[讀取來自 IoT 中樞的傳入訊息](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)


<!--HONumber=Dec16_HO3-->


