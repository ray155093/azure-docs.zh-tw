---
title: 使用閘道 SDK 的裝置管理 | Microsoft Docs
description: Azure IoT 中樞閘道 SDK 逐步解說，示範如何使用閘道 SDK 實作裝置管理
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2016
ms.author: dobett

---
# IoT 閘道 SDK (beta) – 使用閘道 SDK 的裝置管理
本教學課程示範如何搭配使用 IoT 中樞的[裝置管理][lnk-device-management]功能與 [Azure IoT 中樞閘道 SDK][lnk-gateway-sdk]。本教學課程使用 Intel Edison 計算模組來裝載閘道，而閘道有可將遙測傳送到 IoT 中樞的模擬裝置。您可使用 IoT 中樞的裝置管理功能，在 Edison 面板上執行遠端韌體更新。

本教學課程涵蓋下列項目：

* **架構**︰有關裝置管理範例的重要架構資訊。
* **建置和執行**︰建置和執行範例所需的步驟。

## 架構
在本教學課程中，您會佈建 Intel Edison 面板，當作已連接到 IoT 中樞的 IoT 裝置閘道。您也會設定 Edison 面板，以便透過 IoT 中樞進行管理。下圖顯示您在本教學課程中建置的解決方案的重要元素︰

![閘道與裝置管理架構][img-architecture]

### 裝置
下列三個 IoT 裝置會連接到此解決方案中的 IoT 中樞︰

* Edison 面板是名為 **GW-device** 的裝置。在本教學課程中，您可使用 IoT 中樞的裝置管理功能來更新此實體裝置的韌體。
* 兩個模擬裝置 (**GW-ble1-demo** 和 **GW-ble2-demo**)。這些裝置會模擬藍牙低功耗裝置，以透過閘道連接到 IoT 中樞，並將溫度遙測傳送至您的中樞。

### 閘道軟體
閘道軟體會在 Edison 面板上以服務形式執行。兩個模擬裝置會產生溫度遙測。對應模組會將這些模擬裝置對應至已向 IoT 中樞註冊的裝置，而 IoT 中樞模組會處理與 IoT 中樞端點的通訊。[IoT 閘道 SDK – 使用模擬裝置傳送裝置到雲端訊息][lnk-gateway-scenario]一文會詳細說明此案例。

### 裝置管理用戶端
[裝置管理用戶端][lnk-device-management]會在 Edison 面板上以服務形式執行。這可讓您在 Edison 面板上執行遠端作業，例如[韌體更新][lnk-dm-jobs]、重新開機和組態更新，以及查詢裝置屬性。在本教學課程中，裝置管理用戶端會接收並處理要求，以在 Edison 面板上執行韌體更新。

### IoT 中樞
[Azure IoT 中樞][lnk-iot-hub]是一項完全受管理的服務，可在數百萬個 IoT 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。在本教學課程中，IoT 中樞︰

* 可讓三個裝置連接到雲端。
* 接收閘道中的模擬裝置所產生的遙測資料。
* 可讓您將韌體更新要求傳送至 Edison 面板。
* 監視韌體更新作業的進度。

### 裝置管理範例 UI
[裝置管理範例 UI][lnk-dm-sample-ui] 是一個範例 Web 應用程式，可讓您在互動式 Web UI 中使用 IoT 中樞的裝置管理功能。例如，您可以使用範例 UI 來查詢已連接到 IoT 中樞的裝置，並將韌體更新作業提交至您的裝置。在本教學課程中，您可使用範例 UI 將韌體更新作業提交至 Edison 面板，並監視該作業的完成進度。

## 建置並執行
若要執行此範例，您必須建置 Edison 面板的自訂映像，其中包含 IoT 中樞閘道軟體和裝置管理用戶端。

開始之前，請務必確定您可以將 Edison 面板連接到您的無線網路。若要設定 Edison 面板，您必須將它連接到主機電腦。之後，您會使用主機電腦，以您建立的自訂映像刷新 Edison 面板。Intel 提供一組快速入門指南，其中包含適用於下列作業系統的指南︰

* [在 Windows 64 位元上開始使用 Intel Edison 開發面板][lnk-setup-win64]。
* [在 Windows 32 位元上開始使用 Intel Edison 開發面板][lnk-setup-win32]。
* [在 Linux 上開始使用 Intel® Edison 面板][lnk-setup-linux]。

若要設定 Edison 面板並熟悉它，您必須完成這些「開始使用」文章中的所有步驟，但下列步驟除外：

* 刷新最新韌體。您會在本教學課程中更新韌體，所以此時不需要完成這個步驟。
* 目前的教學課程不需要最後一個步驟「選擇 IDE」。

在您設定 Edison 面板並在主機電腦上安裝必要的驅動程式後，您應確定可使用序列終端機來連接到 Edison 面板。Intel 網站上的 [Setting up a serial terminal (設定序列終端機)][lnk-serial-connection] 頁面有連結可設定主機作業系統 (例如 Windows 和 Linux) 的相關指示。

您也必須完成下列工作

* 在您的 Azure 訂用帳戶中[建立 IoT 中樞][lnk-create-hub]。您需要您的中樞名稱才能完成本教學課程。如果您還沒有 Azure 訂用帳戶，可以取得[免費帳戶][lnk-free-trial]。
* 將三個裝置 (**GW-ble1-demo**、**GW-ble2-demo** 和 **GW-device**) 加入至您的 IoT 中樞，並記下其識別碼和裝置金鑰。您可以使用[裝置總管或 iothub-explorer][lnk-explorer-tools] 工具，將這些裝置加入您在上一個步驟中建立的 IoT 中樞，並擷取其金鑰。您可以使用其中兩個裝置 (**GW-ble1-demo** 和 **GW-ble2-demo**) 做為已連接至閘道的 BLE 模擬裝置，而使用另一個裝置 (**GW-device**) 將 Edison 閘道裝置視為您可從 IoT 中樞管理的裝置管理用戶端。

### 準備您的建置環境，並確認您可以建立自訂映像
若要建立 Edison 面板的自訂映像，您需要 Linux 環境。這些步驟假設您使用 Ubuntu 14.04，這是撰寫時建議使用的平台。您的主要磁碟分割上至少要有 40 GB 的可用空間。

> [!NOTE]
> 建立自訂映像的指令碼，最多可能需要在 4 個 CPU 核心的機器上執行 6 小時。您可以使用具有更多 CPU 核心的更強大機器，以減少此時間。
> 
> 

對於本節中的步驟，我們會參考下列文章︰[Intel Edison Board Support Package (Intel Edison 面板支援封裝)][lnk-inteledison-bsp]、[Manually Building Yocto Images for the Intel Edison Board from Source (從來源手動建立 Intel Edison 面板的 Yocto 映像)][lnk-hackgnar] 和 [Creating a Custom Linux Kernel for the Edison (release 2.1) (建立 Edison (2.1 版) 的自訂 Linux 核心)][lnk-shawnhymel]。

1. 登入 Ubuntu 14.04 機器並在主資料夾中執行下列命令，以下載 Edison 來源封裝︰
   
    ```
    curl -O http://downloadmirror.intel.com/25028/eng/edison-src-ww25.5-15.tgz
    ```
2. 將來源封裝解壓縮，以使用下列命令在主資料夾中建立 **edison-src** 資料夾，並巡覽至新的 **edison-src** 資料夾︰
   
    ```
    tar xfvz edison-src-ww25.5-15.tgz
    cd edison-src/
    ```
3. 安裝必要條件套件：
   
    ```
    sudo apt-get -y install build-essential git diffstat gawk chrpath texinfo libtool gcc-multilib libsdl1.2-dev u-boot-tools
    ```
4. 在 **edison-src** 資料夾中建立兩個新目錄︰
   
    ```
    mkdir bitbake_download_dir
    mkdir bitbake_sstate_dir 
    ```
5. 執行下列指令碼，以下載您的建置環境。如果您有 4 個以上的 CPU 核心，請將 **-parallel\_make** 和 **-bb\_number\_thread** 指令碼引數設定為您可用的核心數目︰
   
    ```
    ./meta-intel-edison/setup.sh --dl_dir=bitbake_download_dir  --sstate_dir=bitbake_sstate_dir 
    ```
6. 更新 Paho git 儲存機制的位置。編輯 **~/edison-src/out/linux64/poky/meta-intel-iot-middleware/recipes-connectivity/paho-mqtt/paho-mqtt\_3.1.bb** 檔案並將 URL `git://git.eclipse.org/gitroot/paho/org.eclipse.paho.mqtt.c.git/` 取代為 `git://github.com/eclipse/paho.mqtt.c.git`。
7. 使用下列命令初始化您的建置環境︰
   
    ```
    cd out/linux64/
    source poky/oe-init-build-env
    ```
8. 使用下列命令來建立自訂映像。第一次執行此命令時，最多可能需要在 4 個 CPU 核心的機器上執行 6 小時。加入您自己的自訂項目之後，後續重建會快很多︰
   
    ```
    bitbake edison-image
    ```
9. 執行下列命令來完成建置：
   
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

您需要用於刷新 Edison 面板的檔案目前位於 **~/edison-src/out/linux64/build/toFlash/** 資料夾中。

### 將閘道 SDK 加入至自訂映像
本節中的步驟會引導您完成將閘道 SDK 加入至自訂映像的程序，以便 Edison 在開機時當作 IoT 中樞閘道。在本教學課程中，閘道包含可模擬兩個藍牙低功耗 (BLE) 裝置的模組，而這些裝置會產生閘道的溫度遙測以轉送至 IoT 中樞。

在您在上一節中用來建立 Edison 映像的相同 Ubuntu 14.04 電腦上完成下列步驟。

1. 將閘道 SDK 複製到您的主資料夾︰
   
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-gateway-sdk.git --recursive
    ```
2. 設定此閘道，以連接到您的 IoT 中樞並模擬兩個裝置。編輯 **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_intel\_edison.json** 檔案，並以您設定 IoT 中樞時所記下的值取代 **IoTHubName**、**IoTHubSuffix**、**deviceID** 和 **deviceKey** 預留位置。使用您稍早建立的 **GW-ble1-demo** 和 **GW-ble2-demo** 裝置。
3. 建立資料夾以包含您的新配方︰
   
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk
    ```
4. 將名為 **azure-iot-field-gateway-sdk.bb** 的配方檔案從 **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/** 資料夾複製到您剛才建立的 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk/** 資料夾。編輯此檔案，以您目前的使用者名稱取代出現兩次的 `<<userName>>`。
5. 編輯 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb**，為您的新配方新增一個項目。在檔案結尾處加入下列這一行︰
   
    ```
    IMAGE_INSTALL += "azure-iot-field-gateway-sdk"
    ```
6. 您現在可藉由執行 **bitbake** 命令來建立新的配方，以測試您的變更︰
   
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake azure-iot-field-gateway-sdk
    ```

### 將 Azure IoT 中樞裝置管理用戶端加入至您的自訂映像
本節中的步驟會引導您完成將 IoT 中樞裝置管理用戶端加入至自訂映像的程序，以便從您的 IoT 中樞管理 Edison 閘道裝置。在本教學課程中，裝置管理用戶端包含範例程式碼，可在 Edison 閘道裝置上啟用韌體更新。

在您在上一節中使用的相同 Ubuntu 14.04 電腦上完成下列步驟，以將 Edison 閘道加入至 Edison 映像。

1. 將 IoT 中樞 SDK 儲存機制的 **dmpreview** 分支複製到主資料夾︰
   
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-sdks -b dmpreview --recursive
    ```
2. 建立下列資料夾以包含您新的配方︰
   
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files
    ```
3. 將 **iotdm-edison-sample.bb** 檔案從 **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** 資料夾複製到 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample** 資料夾。
4. 編輯 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/iotdm-edison-sample.bb** 檔案，將 `-Duse_http:BOOL=OFF` 取代為 `-Duse_http:BOOL=ON`。
5. 將 **iotdm\_edison\_sample.service** 檔案從 **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** 資料夾複製到 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files** 資料夾。
6. 編輯 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** 檔案，為您的新配方新增一個項目。在檔案結尾處加入下列這一行︰
   
    ```
    IMAGE_INSTALL += "iotdm-edison-sample"
    ```
7. 因為閘道 SDK 和裝置管理用戶端共用某些程式庫，所以您需要編輯 **~/edison-src/out/linux64/poky/meta/classes/sstate.bbclass** 檔案。在此檔案結尾處加入下列這一行。請務必將 `<your user>` 取代為您目前的使用者名稱：
   
    ```
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/lib/libaziotsharedutil.a"
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/include/azureiot"
    ```
8. 編輯 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-connectivity/wpa\_supplicant/wpa-supplicant/wpa\_supplicant.conf-sane** 檔案並在檔案結尾處加入下列幾行，將 WiFi 設定成在 Edison 面板上自動啟動。請務必將 `<your wifi ssid>` 和 `<your wifi password>` 取代為您的 WiFi 網路的正確值︰
   
    ```
    network={
      ssid="<your wifi ssid>"
      key_mgmt=WPA-PSK
      pairwise=CCMP TKIP
      group=CCMP TKIP WEP104 WEP40
      eap=TTLS PEAP TLS
      psk="<your wifi password>"
    }
    ```
9. 您現在可以建立 Edison 面板的映像，其中包含閘道 SDK 和裝置管理用戶端。**Bitbake** 命令的執行速度會比先前的速度快很多，因為它只需要建立新配方並將其加入至映像︰
   
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake edison-image
    ```
10. 執行下列命令來完成建置：
    
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

您需要用於刷新 Edison 面板的檔案目前位於 **~/edison-src/out/linux64/build/toFlash/** 資料夾中。

### 使用自訂映像刷新您的 Intel Edison
您現在可以使用包含 IoT 中樞裝置管理用戶端和閘道器軟體的自訂映像，刷新您的 Intel Edison。

從 Ubuntu 電腦上您用來建立自訂映像的 **toFlash** 資料夾，將檔案複製到透過 USB 纜線連接至 Edison 面板的電腦。

如果您使用 Windows 電腦透過 USB 纜線連接到 Edison應該執行 **flashall.bat** 指令碼來刷新您的 Edison。如果您使用 Linux 電腦透過 USB 纜線連接到 Edison，應該執行 **flashall.bat** 指令碼來刷新您的 Edison。

刷新程序完成時，請使用來自主機電腦的[序列連線][lnk-serial-connection]連接到 Edison，並 **root** 的身分登入。您應該確認 Edison 面板現在是否已連到 WiFi 網路。

### 執行範例
您必須在 Edison 面板上設定裝置管理用戶端，當作 **GW-device** 裝置連接到您的 IoT 中樞。使用文字編輯器 (例如 **vi** 或 **nano**)，在 Edison 的 /home/root 資料夾中建立名為 **.cs** 的檔案。此檔案只能包含 **GW-device** 的連接字串。如果您先前並未記下此連接字串，可以使用[裝置總管或 iothub-explorer][lnk-explorer-tools] 工具，從 IoT 中樞裝置登錄中擷取此裝置連接字串。

當您建立 **.cs** 檔案後，請使用下列命令重新啟動 Edison 面板︰

```
reboot -h now
```

當 Edison 重新開機時，請檢查裝置管理和閘道服務是否以 [正常] 狀態啟動︰

```
[  OK  ] Started Daemon to receive the wpa_supplicant event.
         Starting PulseAudio Sound System...
[  OK  ] Started WPA supplicant service.
[  OK  ] Started Azure IoT DM as a service..
[  OK  ] Started Azure Iot Gateway as a service..
         Stopping Daemon to receive the wpa_supplicant event...
[  OK  ] Stopped Daemon to receive the wpa_supplicant event.

```

若要對 IoT 閘道服務的任何問題進行疑難排解，請在 Edison 上查看 **/deviceCloudUploadGatewaylog.log**檔案中的項目。您也可以從此服務使用下列命令來檢視任何輸出：

```
systemctl status simulated_device_cloud_upload.service
```

若要對 IoT 裝置管理服務的任何問題進行疑難排解，請使用下列命令來檢視任何輸出︰

```
systemctl status iotdm_edison_sample.service
```

### 啟動韌體更新作業
IoT 裝置管理服務所要求的 Edison 韌體更新通常會下載 zip 檔案，其中包含來自 URL 的韌體。若要簡化本教學課程，您可手動將 zip 檔案複製到 Edison 面板，然後在要求更新時使用 **file://** URL，而不是 **http://** URL。

同樣地，為了簡化本教學課程，韌體更新會重新套用相同的韌體映像，而不是使用全新的映像。您可以看到此映像套用到 Edison 面板。

建立名為 **edison.zip** 的 zip 檔案，其中包含 Ubuntu 電腦上您用來建立自訂映像的 **toFlash** 資料夾中的所有檔案和子資料夾。確定來自 **toFlash** 資料夾的檔案位在 zip 檔案的根目錄中。使用 SCP (或如果您使用 Putty 則為 PSCP) 等工具將 **edison.zip** 檔案複製到 Edison 面板上的檔案 /home/root 資料夾。

若要提交韌體更新作業並監視其進度，使用 Node.js [裝置管理範例 UI][lnk-dm-sample-ui]。您可以在 Windows 或 Linux 上執行此範例中，且它需要 [Node.js][lnk-nodejs] 6.1.0 或更新版本。若要在桌上型電腦上擷取、建置和執行裝置管理範例 UI，請遵循下列步驟︰

1. 開啟 [命令提示字元]。
2. 輸入 `node --version`，確認您已 Node.js 6.1.0 或更新版本。
3. 執行下列命令，複製 Azure IoT 裝置管理 UI GitHub 儲存機制︰
   
    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
4. 在 Azure IoT 裝置管理 UI 儲存機制複製版本的根資料夾中，執行下列命令來擷取相依封裝︰
   
    ```
    npm install
    ```
5. 在完成 npm install 命令時，執行下列命令來建置程式碼︰
   
    ```
    npm run build
    ```
6. 使用文字編輯器，開啟所複製資料夾根目錄中的 user-config.json 檔案。將「&lt;在此輸入您的連接字串&gt;」文字取代為您的 IoT 中樞連接字串。您可以在 Azure [入口網站][lnk-azure-portal]中尋找此連接字串。
7. 在命令提示字元中執行下列命令，啟動裝置管理 UX 應用程式︰
   
    ```
    npm run start
    ```
8. 當命令提示字元報告「服務已啟動」時，開啟網頁瀏覽器，並瀏覽至位於下列 URL 的裝置管理應用程式，以檢視您的裝置︰<http://127.0.0.1:3003>。
   
    ![裝置管理 UI][img-dm-ui]
9. 選取 [GW-device] 裝置，在 [裝置作業] 下拉式清單中選取 [軔體更新]，然後按一下 [啟動]。
10. 在 [封裝 URI] 欄位中，輸入 **file:///home/root/edison.zip** 以使用您先前複製到 Edison 面板的映像檔案。依序按一下 [提交]****、[是]****，然後按一下 [工作歷程記錄]**** 連結，以查看執行中的新父系和子作業︰
    
    ![作業歷程記錄連結][img-history-link]
11. 幾分鐘後，在連線到 Edison 面板的序列終端機中，您會看到 Edison 重新開機並套用韌體變更︰
    
    ```
    reading ota_update.scr
    14747 bytes read in 17 ms (846.7 KiB/s)
    ## Executing script at 00100000
    === OTA update script ===
    Validating Ota package
    part find mmc 0 label:update u_part_num;
    ota drive is mmc 0:9
    
    Validating edison_ifwi-dbg-00-dfu.bin hash for boot0 and boot1 partitions
    
    fatload mmc 0:9 0x6400000 edison_ifwi-dbg-00-dfu.bin;
    reading edison_ifwi-dbg-00-dfu.bin
    ...
    ```
12. 當 Edison 完成重新開機時，請在裝置管理範例 UI 中重新整理頁面，以查看這兩項韌體更新作業的作業狀態現在是 [已完成]︰
    
    ![作業狀態完成][img-job-status]

您現在已完成本教學課程，其中示範如何在 Intel Edison 面板上使用 IoT 中樞閘道軟體和裝置管理用戶端。在本教學課程中，您︰

* 已建立自訂 Intel Edison 映像，其中包含的 IoT 中樞裝置管理用戶端和閘道軟體已設定成在 Edison 面板開機時啟動。
* 已設定 Edison 面板和裝置管理用戶端要連接到您的 IoT 中樞。
* 已從範例 UI 啟動裝置管理作業，該作業會導致 Edison 面板重新開機並套用新的韌體映像。

## 後續步驟
若要深入了解使用 IoT 中樞的裝置管理和範例 UI，請參閱 [Azure IoT 中樞裝置管理概觀][lnk-device-management]一文。

如果您想要更進一步了解閘道 SDK 並實驗一些程式碼範例，請造訪 [Azure IoT Gateway SDK][lnk-gateway-sdk]。

若要進一步探索 IoT 中樞的功能，請參閱︰

* [設計您的解決方案][lnk-design]
* [開發人員指南][lnk-devguide]
* [使用範例 UI 探索裝置管理][lnk-dmui]
* [使用 Azure 入口網站管理 IoT 中樞][lnk-portal]

<!-- Links and images -->

[img-dm-ui]: media/iot-hub-gateway-sdk-device-management/image1.png
[img-history-link]: media/iot-hub-gateway-sdk-device-management/image2.png
[img-job-status]: media/iot-hub-gateway-sdk-device-management/image3.png
[img-architecture]: media/iot-hub-gateway-sdk-device-management/architecture.png

[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-device-management]: iot-hub-device-management-overview.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-serial-connection]: https://software.intel.com/setting-up-serial-terminal-intel-edison-board
[lnk-inteledison-bsp]: http://www.intel.com/content/dam/support/us/en/documents/edison/sb/edisonbsp_ug_331188007.pdf
[lnk-hackgnar]: http://www.hackgnar.com/2016/01/manually-building-yocto-images-for.html
[lnk-shawnhymel]: http://shawnhymel.com/724/creating-a-custom-linux-kernel-for-the-edison-yocto-2-1/
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-nodejs]: https://nodejs.org/
[lnk-azure-portal]: https://portal.azure.com
[lnk-dm-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-physical]: iot-hub-gateway-sdk-physical-device.md
[lnk-gateway-scenario]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0928_2016-->