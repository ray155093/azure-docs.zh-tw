<properties
	pageTitle="搭配閘道 SDK 使用實際裝置 | Microsoft Azure"
	description="使用 Texas Instruments SensorTag 透過在 Intel Edison Compute Module 上執行的閘道將資料傳送到 IoT 中樞的 Azure IoT Hub 閘道 SDK 逐步解說"
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/31/2016"
     ms.author="cstreet"/>


# IoT 閘道 SDK (Beta) – 搭配使用真實裝置與 Linux 來傳送裝置到雲端訊息

這個[藍牙低功耗範例][lnk-ble-samplecode]逐步解說示範如何使用 [Microsoft Azure IoT 閘道 SDK][lnk-sdk]，將裝置到雲端遙測從實體裝置轉送到 IoT 中樞，以及如何將命令從 IoT 中樞路由傳送到實體裝置。

本逐步解說涵蓋下列項目：

* **架構**：有關藍牙低功耗範例的重要架構資訊。

* **建置和執行**︰建置和執行範例所需的步驟。

## 架構

本逐步解說會示範如何在執行 Linux 的 Intel Edison Compute Module 上建置和執行 IoT 閘道。閘道是使用 IoT 閘道 SDK 所建置。這個範例會使用 Texas Instruments SensorTag 藍牙低功耗 (BLE) 裝置來收集溫度資料。

當您執行閘道時，它會執行下列動作︰

- 使用藍牙低功耗 (BLE) 通訊協定連接到 SensorTag 裝置。
- 使用 AMQP 通訊協定連接到 IoT 中樞。
- 將遙測從 SensorTag 裝置轉送到 IoT 中樞。
- 將命令從 IoT 中樞路由傳送到 SensorTag 裝置。

閘道包含下列模組︰

- 「BLE 模組」，可與 BLE 裝置接合，以接收來自裝置的溫度資料，並將命令傳送到裝置。
- 「記錄器模組」，可產生訊息匯流排診斷。
- 「身分識別對應模組」，可在 BLE 裝置 MAC 位址與 Azure IoT 中樞裝置身分識別之間進行轉譯。
- 「IoT 中樞 HTTP 模組」，將遙測資料上傳到 IoT 中樞，並從 IoT 中樞接收裝置命令。
- 「BLE 印表機模組」，可解譯來自 BLE 裝置的遙測，並將格式化的資料列印到主控台以啟用疑難排解和偵錯。

### 資料透過閘道流動的方式

下列區塊圖說明遙測上傳資料流程路線︰

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

遙測的項目要採取來從 BLE 裝置流動到 IoT 中樞的步驟如下：

1. BLE 裝置會產生溫度範例，並透過藍牙將它傳送到閘道中的 BLE 模組。
2. BLE 模組會接收範例，並將其發佈到訊息匯流排以及裝置的 MAC 位址。
3. 身分識別對應模組會從訊息匯流排挑選出這個訊息，並使用內部資料表來將裝置的 MAC 位址轉譯為 IoT 中樞裝置身分識別 (裝置識別碼與裝置金鑰)。接著將新訊息發佈到包含溫度範例資料、裝置的 MAC 位址、裝置識別碼及裝置金鑰的訊息匯流排。
4. IoT 中樞 HTTP 模組會接收這個來自訊息匯流排的新訊息 (由身分識別對應模組所產生)，並將其發佈到 IoT 中樞。
5. 記錄器模組會將所有來自訊息匯流排的所有訊息記錄到磁碟檔案。

下列區塊圖說明裝置命令資料流程路線︰

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. IoT 中樞 HTTP 模組會定期輪詢 IoT 中樞以取得新的命令訊息。
2. 當 IoT 中樞 HTTP 模組接收到新的命令訊息時，會將其發佈到訊息匯流排。
3. 身分識別對應模組會從訊息匯流排挑選出命令訊息，並使用內部資料表來將 IoT 中樞裝置識別碼轉譯到裝置的 MAC 位址。接著將新訊息發佈到訊息匯流排，其中會在訊息的屬性對應中包含目標裝置的 MAC 位址。
4. BLE 模組會挑選出這個訊息，然後藉由與 BLE 裝置通訊來執行 I/O 指令。
5. 記錄器模組會將所有來自訊息匯流排的所有訊息記錄到磁碟檔案。

## 準備硬體

本教學課程假設您正在使用 [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) 裝置連接到 Intel Edison 面板。

### 設定 Edison 面板

開始之前，請務必確定您可以將 Edison 裝置連接到您的無線網路。若要設定 Edison 裝置，您必須將它連接到主機電腦。Intel 提供適用於下列作業系統的快速入門指南︰

- [Get Started with the Intel Edison Development Board on Windows 64-bit (在 Windows 64 位元上開始使用 Intel Edison 開發面板)][lnk-setup-win64]。
- [Get Started with the Intel Edison Development Board on Windows 32-bit (在 Windows 32 位元上開始使用 Intel Edison 開發面板)][lnk-setup-win32]。
- [Get Started with the Intel Edison Development Board on Mac OS X (在 Mac OS X 上開始使用 Intel Edison 開發面板)][lnk-setup-osx]。
- [Getting Started with the Intel® Edison Board on Linux (在 Linux 上開始使用 Intel® Edison 面板)][lnk-setup-linux]。

若要設定 Edison 裝置並熟悉它，您應該完成這些「開始使用」文章中的所有步驟，但最後一個步驟 (「選擇 IDE」) 除外，因為目前的教學課程不需要用到它。在 Edison 安裝程序結尾，您必須︰

- 使用最新版韌體快閃您的 Edison。
- 建立從您的主機到 Edison 的序列式連線。
- 執行 **configure\_edison** 指令碼來設定密碼，並啟用 Edison 上的 WiFi。

### 從 Edison 面板上啟用與 SensorTag 裝置的連線

執行範例之前，您需要確認 Edison 面板可連接到 SensorTag 裝置。

您必須先在 Edison 上更新 BlueZ 軟體的版本。請注意，即使您已經安裝版本 5.37，還是應該完成下列步驟，以確保安裝完成︰

1. 停止目前正在執行的藍牙精靈。
    
    ```
    systemctl stop bluetooth
    ```

2. 下載並解壓縮適用於 BlueZ 版本 5.37 的 [原始程式碼](http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz)。
    
    ```
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    tar -xvf bluez-5.37.tar.xz
    cd bluez-5.37
    ```

3. 建置和安裝 BlueZ。
    
    ```
    ./configure --disable-udev --disable-systemd --enable-experimental
    make
    make install
    ```

4. 藉由編輯檔案 **/lib/systemd/system/bluetooth.service**，來變更藍牙的 *systemd* 服務組態，以使其指向新的藍牙精靈。取代 **ExecStart** 屬性的值，讓它看起來像這樣︰
    
    ```
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

5. 重新啟動您的 Edison。

接著，您需要確認 Edison 可以連接到 SensorTag 裝置。

1. 解除封鎖 Edison 上的藍牙，並檢查版本號碼為 **5.37**。
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. 執行 **bluetoothctl** 命令。您應該會看到如下的輸出：
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

3. 現在您已處於互動式藍芽介面中。輸入 **scan on** 命令以掃描藍牙裝置。您應該會看到如下的輸出：
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

4. 按下小按鈕 (綠色 LED 應該會閃爍)，以使 SensorTag 裝置變成可探索的。Edison 應該會探索 SensorTag 裝置︰
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    在此範例中，您可以看到 SensorTag 裝置的 MAC 位址是 **A0:E6:F8:B5:F6:00**。

5. 輸入 **scan off** 命令來關閉掃描。
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

6. 輸入 **connect <MAC address>**，使用裝置的 MAC 位址連接到 SensorTag 裝置。請注意以下為簡略的範例輸出︰
    
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
    
    附註︰您可以使用 **list-attributes** 命令，再次列出裝置的 GATT 特性。

7. 您現在可以使用 **disconnect** 命令來中斷連接，然後使用 **quit** 命令來從藍牙介面結束︰
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

您現在已經準備好在 Edison 裝置上執行 BLE 閘道範例。

## 執行 BLE 閘道範例

若要在 Edison 上執行 BLE 範例，您必須完成三項工作︰

- 在您的 IoT 中樞上設定兩個範例裝置。
- 在 Edison 裝置上建置閘道 SDK。
- 在 Edison 裝置上設定和執行 BLE 範例。

在撰寫本文時，閘道 SDK 只支援在 Linux 上使用 BLE 模組的閘道。

### 在您的 IoT 中樞上設定兩個範例裝置

- [建立 IoT 中樞][lnk-create-hub] (於 Azure 訂用帳戶中) 時，您需要中樞名稱才能完成此逐步解說。如果您還沒有 Azure 訂用帳戶，則可以取得[免費帳戶][lnk-free-trial]。
- 在您的 IoT 中樞新增一個名為 **SensorTag\_01** 的裝置，並記下其識別碼和裝置金鑰。您可以使用[裝置總管或 iothub-explorer][lnk-explorer-tools] 工具，將這個裝置新增到您在上一個步驟中建立的 IoT 中樞，並擷取其金鑰。當您設定閘道時，您會將此裝置對應到 SensorTag 裝置。

### 在 Edison 裝置上建置閘道 SDK

Edsion 上的 **git** 版本不支援子模組。若要將適用於閘道 SDK 的完整來源下載到 Edison，您有兩個選項︰

- 選項 #1︰複製 Edison 上的 [Microsoft Azure IoT 閘道 SDK][lnk-sdk] 儲存機制，然後手動複製每一個子模組的儲存機制。
- 選項 2︰複製傳統型裝置 (其中的 **git** 支援子模組) 上的 [Microsoft Azure IoT 閘道 SDK][lnk-sdk] 儲存機制，然後將完整的儲存機制和子模組複製到您的 Edison。

如果您選擇選項 #2，請使用下列 **git** 命令來複製閘道 SDK 及其所有子模組︰

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

接著，您應該先將整個本機儲存機制壓縮為單一封存檔，然後再將它複製到 Edison。您可以使用公用程式 (例如 **Putty** 隨附的 **pscp**)，來將封存檔複製到 Edison。例如：

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

當您在 Edison 上擁有閘道 SDK 儲存機制的完整複本時，就可以從包含 SDK 的資料夾使用下列命令加以建置：

```
./tools/build.sh
```

### 在 Edison 裝置上設定和執行 BLE 範例

若要啟動並執行範例，您需要設定參與閘道的每個模組。這個組態是以 JSON 檔案來提供，您必須設定所有五個參與的模組。儲存機制中提供了一個名為 **gateway\_sample.json** 的範例 JSON 檔案，您可以用其做為建立您自己的組態檔的起點。這個檔案位於閘道 SDK 儲存機制本機複本的 **samples/ble\_gateway\_hl/src** 資料夾中。

下列各節說明如何編輯 BLE 範例的這個組態檔，並假設閘道 SDK 儲存機制位於Edison 裝置上的 **/home/root/azure-iot-gateway-sdk /** 資料夾中。如果儲存機制位於其他地方，您應該據以調整該路徑︰


#### 記錄器組態

假設閘道儲存機制位於資料夾 **/home/root/azure-iot-gateway-sdk /** 中，請以如下方式設定記錄器模組︰

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### BLE 模組組態

BLE 裝置的範例組態會假設 Texas Instruments SensorTag 裝置。任何可做為 GATT 週邊操作的標準 BLE 裝置應該可以運作，但您需要更新 GATT 特性識別碼和資料 (適用於寫入指示)。新增 SensorTag 裝置的 MAC 位址︰

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
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

#### IoT 中樞 HTTP 模組

新增 IoT 中樞的名稱。尾碼值通常是 **azure-devices.net**：

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothubhttp/libiothubhttp_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>"
  }
}
```

#### 身分識別對應模組組態

新增 SensorTag 裝置的 MAC 位址，以及您新增到 IoT 中樞之 **SensorTag\_01** 裝置的裝置識別碼和金鑰：

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### BLE 印表機模組組態

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

若要執行範例，您要執行 **ble\_gateway\_hl** 二進位檔，將路徑傳遞到 JSON 組態檔。如果您使用 **gateway\_sample.json** 檔案，要執行的命令看起來如下︰

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

執行範例之前，您可能需要按下 SensorTag 上的小按鈕，以使其變成可探索的。

當您執行範例時，可以使用[裝置總管或 iothub-explorer][lnk-explorer-tools] 工具，來監視閘道從 SensorTag 裝置轉送的訊息。

## 傳送雲端到裝置訊息

BLE 模組也支援從 Azure IoT 中樞傳送指示給裝置。您可以使用 [Azure IoT 中樞裝置總管](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) 或 [IoT 中樞總管] https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer)，來傳送 BLE 閘道模組傳遞給 BLE 裝置的 JSON 訊息。例如，如果您正在使用 Texas Instruments SensorTag 裝置，則可從 IoT 中樞將下列 JSON 訊息傳送到裝置。

- 重設所有的 LED 與警報器 (關閉)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- 將 I/O 設定為「遠端」

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- 開啟紅色 LED

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- 開啟綠色 LED

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- 開啟警報器

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

針對使用 HTTP 通訊協定連接到 IoT 中樞之裝置的預設行為是每隔 25 分鐘會檢查新的命令。因此，如果您傳送數個個別命令，就需要等待 25 分鐘，讓裝置接收每個命令。

> [AZURE.NOTE] 閘道也會在它每次啟動時檢查新命令，如此您就能藉由停止和啟動閘道來強制其處理命令。

## 後續步驟

如需詳細資訊，請參閱 [Azure IoT 閘道 SDK][lnk-sdk]。

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

<!---HONumber=AcomDC_0601_2016-->