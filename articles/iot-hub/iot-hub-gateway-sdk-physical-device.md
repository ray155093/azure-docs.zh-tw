---
title: "搭配 IoT 閘道 SDK 使用實體裝置 | Microsoft Docs"
description: "「Azure IoT Hub 閘道 SDK」逐步解說，其中使用 Texas Instruments SensorTag 裝置，透過在 Raspberry Pi 3 上執行的閘道，將資料傳送到「IoT 中樞」"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 9c8ab5b54644c3fa7999e7250825fba5d8532082


---
# <a name="azure-iot-gateway-sdk--send-device-to-cloud-messages-with-a-physical-device-using-linux"></a>Azure IoT 閘道 SDK – 使用 Linux 以實體裝置傳送裝置到雲端訊息
這個[藍牙低功耗範例][lnk-ble-samplecode]逐步解說示範如何使用 [Azure IoT 閘道 SDK][lnk-sdk]，將「裝置到雲端」的遙測資料從實體裝置轉送到 IoT 中樞，以及如何將命令從 IoT 中樞路由傳送到實體裝置。

本逐步解說涵蓋下列項目：

* **架構**：有關藍牙低功耗範例的重要架構資訊。
* **建置和執行**︰建置和執行範例所需的步驟。

## <a name="architecture"></a>架構
本逐步解說會示範如何在執行 Raspbian Linux 的 Raspberry Pi 3 上建置和執行 IoT 閘道。 閘道是使用 IoT 閘道 SDK 建置而成。 這個範例會使用 Texas Instruments SensorTag 藍牙低功耗 (BLE) 裝置來收集溫度資料。

當您執行閘道時，它會執行下列動作︰

* 使用藍牙低功耗 (BLE) 通訊協定連接到 SensorTag 裝置。
* 使用 HTTP 通訊協定來連接到「IoT 中樞」。
* 將遙測從 SensorTag 裝置轉送到 IoT 中樞。
* 將命令從 IoT 中樞路由傳送到 SensorTag 裝置。

閘道包含下列模組︰

* 「BLE 模組」  ，可與 BLE 裝置接合，以接收來自裝置的溫度資料，並將命令傳送到裝置。
* 「BLE 雲端至裝置模組」，可將來自雲端的 JSON 訊息轉譯為「BLE 模組」所用的 BLE 指示。
* 「記錄器模組」，可將所有閘道訊息記錄到本機檔案。
* 「身分識別對應模組」  ，可在 BLE 裝置 MAC 位址與 Azure IoT 中樞裝置身分識別之間進行轉譯。
* 「IoT 中樞模組」  ，將遙測資料上傳到 IoT 中樞，並從 IoT 中樞接收裝置命令。
* 「BLE 印表機模組」  ，可解譯來自 BLE 裝置的遙測，並將格式化的資料列印到主控台以啟用疑難排解和偵錯。

### <a name="how-data-flows-through-the-gateway"></a>資料透過閘道流動的方式
下列區塊圖說明遙測上傳資料流程路線︰

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

遙測的項目從 BLE 裝置流動到 IoT 中樞要採取的步驟如下：

1. BLE 裝置會產生溫度範例，並透過藍牙將它傳送到閘道中的 BLE 模組。
2. BLE 模組會接收範例，並將其發佈到訊息代理程式以及裝置的 MAC 位址。
3. 身分識別對應模組會挑選出這個訊息，並使用內部資料表來將裝置的 MAC 位址轉譯為 IoT 中樞裝置身分識別 (裝置識別碼與裝置金鑰)。 接著將發佈包含溫度範例資料、裝置的 MAC 位址、裝置識別碼及裝置金鑰的新訊息。
4. IoT 中樞模組會接收這個新訊息 (由身分識別對應模組所產生)，並將其發佈到 IoT 中樞。
5. 記錄器模組會將所有來自訊息代理程式的所有訊息記錄到本機檔案。

下列區塊圖說明裝置命令資料流程路線︰

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. IoT 中樞模組會定期輪詢 IoT 中樞以取得新的命令訊息。
2. 當 IoT 中樞模組接收到新的命令訊息時，會將其發佈到訊息代理程式。
3. 身分識別對應模組會挑選出命令訊息，並使用內部資料表來將 IoT 中樞裝置識別碼轉譯到裝置的 MAC 位址。 接著將發佈新訊息，其中會在訊息的屬性對應中包含目標裝置的 MAC 位址。
4. BLE 雲端至裝置模組會挑選此訊息，並將其轉譯成適當的 BLE 指示供 BLE 模組使用。 接著將發佈新訊息。
5. BLE 模組會挑選出這個訊息，然後藉由與 BLE 裝置通訊來執行 I/O 指令。
6. 記錄器模組會將所有來自訊息代理程式的所有訊息記錄到磁碟檔案。

## <a name="prepare-your-hardware"></a>準備硬體
本教學課程假設您正在使用 [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) 裝置連接到執行 Raspbian 的 Raspberry Pi 3。

### <a name="install-raspbian"></a>安裝 Raspbian
您可以使用下列任一選項在 Raspberry Pi 3 裝置上安裝 Raspbian。 

* 使用圖形化使用者介面 [NOOBS][lnk-noobs]，以安裝最新版的 Raspbian。 
* 手動[下載][lnk-raspbian]最新的 Raspbian 作業系統映像並寫入到 SD 記憶卡。 

### <a name="install-bluez-537"></a>安裝 BlueZ 5.37
BLE 模組會透過 BlueZ 堆疊與藍牙硬體通訊。 您需要 5.37 版的 BlueZ 才能讓模組正常運作。 下列指示可確保所安裝的 BlueZ 版本不會錯誤。

1. 停止目前的藍牙精靈：
   
    ```
    sudo systemctl stop bluetooth
    ```
2. 安裝 BlueZ 相依項目。 
   
    ```
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```
3. 從 bluez.org 下載 BlueZ 原始程式碼。 
   
    ```
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```
4. 將原始程式碼解壓縮。
   
    ```
    tar -xvf bluez-5.37.tar.xz
    ```
5. 將目錄變更為新建立的資料夾。
   
    ```
    cd bluez-5.37
    ```
6. 設定要建置的 BlueZ 程式碼。
   
    ```
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```
7. 建置 BlueZ。
   
    ```
    make
    ```
8. 在 BlueZ 建置完成後安裝 BlueZ。
   
    ```
    sudo make install
    ```
9. 變更藍牙的 systemd 服務組態，讓它指向`/lib/systemd/system/bluetooth.service` 檔案中的新藍牙精靈。 使用下列文字來取代「ExecStart」程式碼行： 
    
    ```
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>從 Raspberry Pi 3 裝置上啟用與 SensorTag 裝置的連線
執行範例之前，您需要確認 Raspberry Pi 3 可連接到 SensorTag 裝置。


1. 確定已安裝 `rfkill` 公用程式。
   
    ```
    sudo apt-get install rfkill
    ```
2. 將 Raspberry Pi 3 上的藍牙解除封鎖，並檢查版本號碼為 **5.37**。
   
    ```
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```
3. 啟動藍牙服務並執行 **bluetoothctl** 命令來輸入互動式藍牙殼層。 
   
    ```
    sudo systemctl start bluetooth
    bluetoothctl
    ```
4. 輸入 **power on** 命令來開啟藍牙控制器電源。 您應該會看到如下的輸出：
   
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```
5. 在仍位於互動式藍牙殼層中的狀態下，輸入 **scan on** 命令以掃描藍牙裝置。 您應該會看到如下的輸出：
   
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```
6. 按下小按鈕 (綠色 LED 應該會閃爍)，以使 SensorTag 裝置變成可探索的。 Raspberry Pi 3 應該會探索 SensorTag 裝置︰
   
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
   
    在此範例中，您可以看到 SensorTag 裝置的 MAC 位址是 **A0:E6:F8:B5:F6:00**。
7. 輸入 **scan off** 命令來關閉掃描。
   
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```
8. 透過輸入 **connect \<MAC 位址>**，使用裝置的 MAC 位址來連接到 SensorTag 裝置。 請注意以下為簡略的範例輸出︰
   
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
   
    > 請注意，您可以使用 **list-attributes** 命令，再次列出裝置的 GATT 特性。
9. 您現在可以使用 **disconnect** 命令來中斷連接，然後使用 **quit** 命令來從藍牙介面結束︰
   
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

您現在已經準備好在 Raspberry Pi 3 上執行 BLE 閘道範例。

## <a name="run-the-ble-gateway-sample"></a>執行 BLE 閘道範例
若要執行 BLE 範例，您必須完成三項工作︰

* 在您的 IoT 中樞上設定兩個範例裝置。
* 在 Raspberry Pi 3 裝置上建置「IoT 閘道 SDK」。
* 在 Raspberry Pi 3 裝置上設定和執行 BLE 範例。

在撰寫本文的當時，「IoT 閘道 SDK」只支援在 Linux 上使用 BLE 模組的閘道。

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>在您的 IoT 中樞上設定兩個範例裝置
* 於 Azure 訂用帳戶中[建立 IoT 中樞][lnk-create-hub]時，您將需要中樞名稱才能完成此逐步解說。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* 在您的 IoT 中樞新增一個名為 **SensorTag_01** 的裝置，並記下其識別碼和裝置金鑰。 您可以使用[裝置總管或 iothub-explorer][lnk-explorer-tools] 工具，將這個裝置新增到您在上一個步驟中建立的 IoT 中樞，並擷取其金鑰。 當您設定閘道時，您會將此裝置對應到 SensorTag 裝置。

### <a name="build-the-azure-iot-gateway-sdk-on-your-raspberry-pi-3"></a>在 Raspberry Pi 3 上建置 Azure IoT 閘道 SDK

安裝 Azure IoT 閘道 SDK 的相依項目。

``` 
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```
使用下列命令將「IoT 閘道 SDK」及其所有子模組複製到主目錄︰

```
cd ~
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
cd azure-iot-gateway-sdk
git submodule update --init --recursive
```

當您的 Raspberry Pi 3 上擁有「IoT 閘道 SDK」儲存機制的完整複本時，您就可以使用下列命令，從包含該 SDK 的資料夾建置它：

```
./tools/build.sh --skip-unittests --skip-e2e-tests
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>在 Raspberry Pi 3 上設定和執行 BLE 範例
若要啟動並執行範例，您需要設定參與閘道的每個模組。 這個組態是以 JSON 檔案來提供，您必須設定所有五個參與的模組。 存放庫中提供了一個名為 **gateway_sample.json** 的範例 JSON 檔案，您可以把它當作起點，開始建立自己的組態檔。 這個檔案位於「IoT 閘道 SDK」儲存機制本機複本的 **samples/ble_gateway/src** 資料夾中。

下列各節說明如何編輯 BLE 範例的這個組態檔，並假設「IoT 閘道 SDK」儲存機制位於 Raspberry Pi 3 上的 **/home/pi/azure-iot-gateway-sdk/** 資料夾中。 如果儲存機制位於其他地方，您應該據以調整該路徑︰

#### <a name="logger-configuration"></a>記錄器組態
假設閘道儲存機制位於資料夾 **/home/pi/azure-iot-gateway-sdk /**中，請以如下方式設定記錄器模組︰

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>BLE 模組組態
BLE 裝置的範例組態會假設 Texas Instruments SensorTag 裝置。 任何可作為 GATT 週邊操作的標準 BLE 裝置應該可以運作，但您需要更新 GATT 特性識別碼和資料 (適用於寫入指示)。 新增 SensorTag 裝置的 MAC 位址︰ 

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>IoT 中樞模組
新增 IoT 中樞的名稱。 尾碼值通常是 **azure-devices.net**：

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>身分識別對應模組組態
新增 SensorTag 裝置的 MAC 位址，以及您新增到 IoT 中樞之 **SensorTag_01** 裝置的裝置識別碼和金鑰：

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "AA:BB:CC:DD:EE:FF",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>BLE 印表機模組組態
```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>BLEC2D 模組組態
```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>路由組態
下列設定參數可以確保︰

* **Logger** 模組接收並記錄所有訊息。
* **SensorTag** 模組會將訊息傳送至 **mapping** 和 **BLE Printer** 模組。
* **mapping** 模組會將準備傳送至您的 IoT 中樞的訊息傳送給 **IoTHub** 模組。
* **IoTHub** 模組會將訊息傳回給 **mapping** 模組。
* **mapping** 模組會將訊息傳送給 **BLEC2D** 模組。
* **BLEC2D** 模組會將訊息傳回給 **Sensor Tag** 模組。

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

若要執行範例，請將 JSON 組態檔的路徑傳遞到 **ble_gateway** 二進位檔。 如果您使用 **gateway_sample.json** 檔案，則命令如下。 從 azure-iot-gateway-sdk 目錄執行此命令

```
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

執行範例之前，您可能需要按下 SensorTag 上的小按鈕，以使其變成可探索的項目。

當您執行範例時，可以使用[裝置總管或 iothub-explorer][lnk-explorer-tools] 工具，來監視閘道從 SensorTag 裝置轉送的訊息。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息
BLE 模組也支援從 Azure IoT 中樞傳送指示給裝置。 您可以使用 [Azure IoT 中樞裝置總管](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md)或 [IoT 中樞總管](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer)傳送 BLE 閘道模組傳要遞給 BLE 裝置的 JSON 訊息。
如果您使用 Texas Instruments SensorTag 裝置，則可以從 IoT 中樞傳送命令以開啟紅色 LED、綠色 LED 或警報器。 若要這樣做，請先依序傳送下列兩個 JSON 訊息。 然後，您可以傳送任何命令以開啟燈號或警報器。

1 重設所有的 LED 與警報器 (將其關閉)
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```
2 將 I/O 設定為「遠端」
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* 開啟紅色 LED
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* 開啟綠色 LED
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```
* 開啟警報器
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>後續步驟
如果您想要更進一步了解「IoT 閘道 SDK」並實驗一些程式碼範例，請瀏覽下列開發人員教學課程和資源：

* [Azure IoT 閘道 SDK][lnk-sdk]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [開發人員指南][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


