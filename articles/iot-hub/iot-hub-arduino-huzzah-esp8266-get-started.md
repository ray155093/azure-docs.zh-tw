---
title: "ESP8266 連線到雲端 - 將 Feather HUZZAH ESP8266 連線到 Azure IoT 中樞 | Microsoft Docs"
description: "將 Arduino 裝置 (Adafruit Feather HUZZAH ESP8266) 連線到 Azure IoT 中樞 (可協助管理 IoT 資產的 Microsoft 雲端服務) 的指南。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 544f98afc1769f75bd4e06dc7b2bf8a1a0d91371
ms.lasthandoff: 03/30/2017


---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>將 Adafruit Feather HUZZAH ESP8266 連線到位於雲端的 Azure IoT 中樞

![DHT22、Feather HUZZAH ESP8266 與 IoT 中樞之間的連線](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-will-do"></a>將執行的作業

將 Adafruit Feather HUZZAH ESP8266 連接到 IoT 中樞。 然後，在 ESP8266 上執行範例應用程式，以收集 DHT22 感應器中的溫度和溼度資料。 最後，將感應器資料傳送至 IoT 中樞。

> [!NOTE]
>如果您使用其他 ESP8266 電路版，仍可遵循下列步驟來將它們連接到 IoT 中樞。 根據您使用的 ESP8266 電路板，您可能需要重新設定 `LED_PIN`。 例如，如果您使用 AI-Thinker 的 ESP8266，您可以將它從 `0` 變更為 `2`。 還沒有電路板嗎？ [取得 Microsoft Azure IoT 入門套件](http://azure.com/iotstarterkits)。

## <a name="what-you-will-learn"></a>學習目標

* 如何建立 IoT 中樞並為 Feather HUZZAH ESP8266 註冊裝置。
* 如何連接 Feather HUZZAH ESP8266 與感應器和電腦。
* 如何在 Feather HUZZAH ESP8266 上執行範例應用程式來收集感應器資料。
* 如何將感應器資料傳送至 IoT 中樞。

## <a name="what-you-will-need"></a>必要元件

![本教學課程所需的零件](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

若要完成此作業，您需要 Feather HUZZAH ESP8266 入門套件中的下列零件：

* Feather HUZZAH ESP8266 電路板。
* Micro USB 轉 Type A 的 USB 纜線。

您的開發環境還需要下列事項：

* 執行 Windows 或 Ubuntu 的 Mac 或 PC。
* 供 Feather HUZZAH ESP8266 連線到的無線網路。
* 用來下載組態工具的網際網路連線。
* [Arduino IDE](https://www.arduino.cc/en/main/software) 1.6.8 版 (或更新版本)，舊版無法與 AzureIoT 程式庫搭配運作。


如果您沒有感應器，下列項目可供選用。 您也可以選擇使用模擬的感應器資料。

* Adafruit DHT22 溫度和溼度感應器。
* 麵包板。
* M/M 跳線。

## <a name="create-an-iot-hub-and-register-a-device-for-feather-huzzah-esp8266"></a>建立 IoT 中樞並為 Feather HUZZAH ESP8266 註冊裝置

### <a name="create-your-azure-iot-hub-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure IoT 中樞

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 按一下 [新增] > [物聯網] > [IoT 中樞]。

   ![建立 IoT 中樞](media/iot-hub-arduino-huzzah-esp8266-get-started/3_iot-hub-creation.png)

1. 在 [IoT 中樞] 窗格中，輸入 IoT 中樞的必要資訊︰

   ![用於建立 IoT 中樞的基本資訊](media/iot-hub-arduino-huzzah-esp8266-get-started/4_iot-hub-provide-basic-info.png)

   * **名稱**：它是 IoT 中樞的名稱。 如果您輸入的名稱有效，則會出現綠色核取記號。
   * **定價與級別層**︰選取 F1 免費層。 對此範例而言，此選項就已足夠。 請參閱[定價與級別層](https://azure.microsoft.com/pricing/details/iot-hub/)。

   * **資源群組**：建立用以裝載 IoT 中樞的資源群組，或使用現有資源群組。 請參閱[使用資源群組來管理您的 Azure 資源](../azure-resource-manager/resource-group-portal.md)。
   * **位置**︰選取與您最接近的位置，以在其中建立 IoT 中樞。
   * **釘選儀表板**︰核取此選項可讓您從儀表板輕鬆地存取 IoT 中樞。
1. 按一下 [建立] 。 可能需要幾分鐘的時間才能建立 IoT 中樞。 您可以在 [通知] 窗格中看到進度。

   ![在 [通知] 窗格中監視 IoT 中樞的建立進度](media/iot-hub-arduino-huzzah-esp8266-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. IoT 中樞建立好之後，從儀表板對它按一下。 請記下**主機名稱**，然後按一下 [共用存取原則]。

   ![取得 IoT 中樞的主機名稱](media/iot-hub-arduino-huzzah-esp8266-get-started/6_iot-hub-get-hostname.png)

1. 在 [共用存取原則] 窗格中，按一下 [iothubowner] 原則，然後複製並記下 IoT 中樞的**連接字串**以供稍後使用。 如需詳細資訊，請參閱[控制 IoT 中樞的存取權](iot-hub-devguide-security.md)。

   ![取得 IoT 中樞連接字串](media/iot-hub-arduino-huzzah-esp8266-get-started/7_iot-hub-get-connection-string.png)

### <a name="register-a-device-for-feather-huzzah-esp8266-in-your-iot-hub"></a>在 IoT 中樞內為 Feather HUZZAH ESP8266 註冊裝置

每個 IoT 中樞都有身分識別登錄，可儲存允許連線至 IoT 中樞之裝置的相關資訊。 若要讓裝置可以連線到 IoT 中樞，IoT 中樞的身分識別登錄中必須先有該裝置的項目。

在本節中，您會使用 CLI 工具 iothub explorer，在 IoT 中樞的身分識別登錄中註冊 Feather HUZZAH ESP8266 適用的裝置。

> [!NOTE]
> iothub explorer 需要 Node.js 4.x 或更新版本才能正確運作。

若要為 Feather HUZZAH ESP8266 註冊裝置，請遵循下列步驟：

1. [下載](https://nodejs.org/en/download/)並安裝 Node.js 的最新 LTS 版本 (內含 NPM)。
1. 使用 NPM 安裝 iothub explorer。

   * Windows 7 或更新版本

     以系統管理員身分開啟命令提示字元。 執行下列命令以安裝 iothub explorer：

     ```bash
     npm install -g iothub-explorer
     ```
   * Ubuntu 16.04 或更新版本

     使用鍵盤快速鍵 Ctrl + Alt + T 開啟終端機，然後執行下列命令：

     ```bash
     sudo npm install -g iothub-explorer
     ```
   * macOS 10.1 或更新版本

     開啟終端機，然後執行下列命令：

     ```bash
     npm install -g iothub-explorer
     ```
1. 執行下列命令來登入 IoT 中樞：

   ```bash
   iothub-explorer login [your iot hub connection string]
   ```
1. 註冊新裝置並將 `deviceID` 設為 `new-device`，然後執行下列命令來取得其連接字串：

   ```bash
   iothub-explorer create new-device --connection-string
   ```

記下已註冊裝置的連接字串。

> [!NOTE]
> 若要檢視已註冊裝置的連接字串，請執行 `iothub-explorer list` 命令。

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>連接 Feather HUZZAH ESP8266 與感應器和電腦

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>將 DHT22 溫度和溼度感應器連接至 Feather HUZZAH ESP8266

使用麵包板和跳線來進行連接，如下所示。 如果您沒有感應器，請略過本節，因為您可以改為使用模擬的感應器資料。

![連接參考](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)

針對感應器針腳使用下列接線方式：

| 開始 (感應器)           | 結束 (電路版)           | 纜線顏色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (針腳 31F)            | 3V (針腳 58H)           | 紅色纜線     |
| DATA (針腳 32F)           | GPIO 2 (針腳 46A)       | 藍色纜線    |
| GND (針腳 34F)            | GND (針腳 56I)          | 黑色纜線   |

如需詳細資訊，請參閱︰[Adafruit DHT22 感應器安裝](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor)和 [Adafruit Feather HUZZAH Esp8266 接腳圖](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts)

現在，Feather Huzzah ESP8266 應該已經和作用中的感應器連接。

![連接 dht22 與 feather huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>將 Feather HUZZAH ESP8266 連接到電腦

請使用「Micro USB 轉 Type A 的 USB 纜線」將 Feather HUZZAH ESP8266 連接到電腦，如下所示。

![將 feather huzzah 連接到電腦](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>新增序列埠權限 - 僅限 Ubuntu

如果您使用 Ubuntu，請確定您具有操作 Feather HUZZAH ESP826 之 USB 連接埠的權限。 若要新增序列埠權限，請遵循下列步驟：

1. 在終端機執行下列命令：

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   您會得到下列其中一個輸出︰

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   在輸出中，請注意 `uucp` 或 `dialout` 是 USB 連接埠的群組擁有者名稱。

1. 執行下列命令，將使用者新增至群組︰

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` 是您在上一個步驟中取得的群組擁有者名稱。 `<username>` 是 Ubuntu 使用者名稱。

1. 先登出 Ubuntu 然後重新登入，以讓變更生效。

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>收集感應器資料，並將它傳送至 IoT 中樞

在本節中，您可以在 Feather HUZZAH ESP8266 上部署和執行範例應用程式。 範例應用程式會在 Feather HUZZAH ESP8266 上閃爍 LED，並將收集自 DHT22 感應器的溫度和溼度資料傳送至 IoT 中樞。

### <a name="get-the-sample-application-from-github"></a>從 GitHub 取得範例應用程式

範例應用程式會裝載在 GitHub 上。 請從 GitHub 複製包含範例應用程式的範例存放庫。 若要複製範例存放庫，請遵循下列步驟︰

1. 開啟命令提示字元或終端機視窗。
1. 移至想要儲存範例應用程式的資料夾。
1. 執行以下命令：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

在 Arduino IDE 中安裝 Feather HUZZAH ESP8266 的套件︰

1. 開啟範例應用程式儲存所在的資料夾。
1. 在 Arduino IDE 中開啟應用程式資料夾中的 app.ino 檔案。

   ![在 Arduino IDE 中開啟範例應用程式](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. 在 Arduino IDE 中，按一下 [檔案] > [喜好設定]。
1. 在 [喜好設定] 對話方塊中，按一下 [其他電路板管理員 URL] 文字方塊旁的圖示。
1. 在快顯視窗中，輸入下列 URL，然後按一下 [確定]。

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![在 arduino ide 中指向套件 url](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. 在 [喜好設定] 對話方塊中，按一下 [確定]。
1. 按一下 [工具] > [電路板] > [電路板管理員]，然後搜尋 esp8266。

   [電路板管理員] 指出已安裝的 ESP8266 版本為 2.2.0 版或更新版本。

   ![已安裝 esp8266 套件](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. 按一下 [工具] > [電路板] > [Adafruit HUZZAH ESP8266]。

### <a name="install-necessary-libraries"></a>安裝必要的程式庫

1. 在 Arduino IDE 中，按一下 [草圖] > [包含程式庫] > [管理程式庫]。
1. 逐一搜尋下列程式庫名稱。 對找到的每個程式庫按一下 [安裝]。
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>沒有真正的 DHT22 感應器嗎？

如果您沒有真正的 DHT22 感應器，範例應用程式可以模擬溫度和溼度資料。 若要讓範例應用程式使用模擬資料，請遵循下列步驟︰

1. 開啟 `app` 資料夾中的 `config.h` 檔案。
1. 找出下面這行程式碼，並將值從 `false` 變更為 `true`：
   ```c
   define SIMULATED_DATA true
   ```
   ![設定範例應用程式以使用模擬資料](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. 使用 `Control-s` 儲存檔案。

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>將範例應用程式部署至 Feather HUZZAH ESP8266

1. 在 Arduino IDE 中，按一下 [工具] > [連接埠]，然後按一下 Feather HUZZAH ESP8266 的序列埠。
1. 按一下 [草圖] > [上傳]，以建置範例應用程式並將其部署至 Feather HUZZAH ESP8266。

### <a name="enter-your-credentials"></a>輸入認證

上傳程序成功完成後，請遵循步驟來輸入認證︰

1. 在 Arduino IDE 中，按一下 [工具] > [序列監視器]。
1. 在 [序列監視器] 視窗中，請注意右下角的兩個下拉式清單。
1. 在左邊的下拉式清單中選取 [無行尾結束符號]。
1. 在右邊的下拉式清單中選取 [115200 傳輸速率]。
1. 在位於 [序列監視器] 視窗頂端的輸入方塊中，輸入系統要求您提供的下列資訊，然後按一下 [傳送]。
   * Wi-Fi SSID
   * Wi-Fi 密碼
   * 裝置連接字串

> [!Note]
> 認證資訊會儲存在 Feather HUZZAH ESP8266 的 EEPROM 中。 如果您按一下 Feather HUZZAH ESP8266 電路板上的重設按鈕，範例應用程式會詢問您是否要清除資訊。 輸入 `Y` 以清除資訊，此時系統會再次要求您提供資訊。

### <a name="verify-the-sample-application-is-running-successfully"></a>確認範例應用程式已成功執行

如果您在 [序列監視器] 視窗看到下列輸出，並在 Feather HUZZAH ESP8266 上看到閃爍的 LED，則表示範例應用程式已成功執行。

![arduino ide 中的最終輸出](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>後續步驟

您已成功將 Feather HUZZAH ESP8266 連線到 IoT 中樞，並將擷取到的感應器資料傳送至 IoT 中樞。 

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

- [透過 iothub-explorer 管理雲端裝置傳訊](iot-hub-explorer-cloud-device-messaging.md)
- [將 IoT 中樞IoT 中樞儲存至 Azure 資料儲存體](iot-hub-store-data-in-azure-table-storage.md)
- [使用 Power BI 將來自 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。
- [使用 Azure Web Apps 將來自 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-web-apps.md)。

