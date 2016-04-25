<properties
   pageTitle="在 mbed 上使用 C 連接裝置 | Microsoft Azure"
   description="描述如何在 mbed 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="dobett"/>


# 將裝置連接至 IoT 套件遠端監視預先設定方案

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## 在 mbed 上建置並執行範例 C 的解決方案

下列指示描述將[啟用 mbed 的 Freescale FRDM-K64F][lnk-mbed-home] 裝置連接至遠端監視解決方案的步驟。

### 將裝置連接到網路和桌上型電腦

1. 使用乙太網路纜線將 mbed 裝置連接到您的網路。這是必要的步驟，因為範例應用程式需要透過網際網路存取。

2. 請參閱 [mbed 使用者入門][lnk-mbed-getstarted]將您的 mbed 裝置連接至桌上型電腦。

3. 如果桌上型電腦執行的是 Windows，請參閱 [PC 組態][lnk-mbed-pcconnect]設定序列埠存取 mbed 裝置。

### 建立 mbed 專案並匯入範例程式碼

1. 在您的 Web 瀏覽器中，移至 mbed.org [開發人員網站](https://developer.mbed.org/)。如果您還沒有註冊，您會看到建立新帳戶的選項 (它是免費的)。否則，請使用您的帳戶認證登入。然後按一下頁面右上角的 [編譯器]。如此應該會帶您前往工作區管理介面。

2. 請確定您使用的硬體平台出現在視窗的右上角，或按一下右手邊的圖示來選取您的硬體平台。

3. 在主功能表上按一下 [匯入]。然後按一下 [按一下這裡] 從 mbed 地球標誌旁的 URL 連結匯入。

    ![][6]

4. 在快顯視窗中，輸入範例程式碼的連結 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/，然後按一下 [匯入]。

    ![][7]

5. 您可以在匯入這個專案的 mbed 編譯器視窗中看到匯入的各種程式庫。有些是由 Azure IoT 小組提供和維護 ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[azure\_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/))，其他則是可以在 mbed 程式庫目錄中取得的協力廠商程式庫。

    ![][8]

6. 開啟 remote\_monitoring\\remote\_monitoring.c 檔案，並在檔案中尋找下列程式碼：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. 將 [Device Id] 和 [Device Key] 以您的裝置資料取代，來使範例程式能夠連線到您的 IoT 中樞。使用 IoT 中樞主機名稱來取代 [IoTHub Name] 和 [IoTHub Suffix, i.e. azure-devices.net] 預留位置。例如，若您的 IoT 中樞主機名稱是 Contoso.azure-devices.net，contoso 就是 **hubName**，而它之後的所有項目就是 **hubSuffix**：

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### 程式碼逐步解說

如果您對程式運作的方式感到興趣，本節將會描述範例程式碼幾個關鍵的部分。如果您只想要執行程式碼，請直接跳到[建置並執行程式](#buildandrun)。

#### 定義模型

此範例使用[序列化程式][lnk-serializer]程式庫來定義指定裝置可傳送到或接收來自 IoT 中樞之訊息的模型。在此範例中，[Contoso] 命名空間會定義一個「控溫器」模型，此模型能指定 **Temperature**、**ExternalTemperature** 及 **Humidity** 等遙測資料，以及裝置識別碼、裝置屬性及裝置會做出回應的命令等中繼資料：

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

與模型定義相關的，是裝置會做出回應之 **SetTemperature** 和 **SetHumidity** 命令的定義：

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### 將模型連接到程式庫

函式 **sendMessage** 和 **IoTHubMessage** 是從裝置傳送遙測，並將來自 IoT 中樞的訊息連接到命令處理常式的重複使用程式碼。

#### remote\_monitoring\_run 函式

程式的 **main** 函式會在應用程式開始將裝置的行為做為 IoT 中樞裝置用戶端執行時，叫用 **remote\_monitoring\_run** 函式。此 **remote\_monitoring\_run** 函式大部分是以巢狀配對的函式所組成：

- **platform\_init** 和 **platform\_deinit** 能執行平台特定的初始化和關機作業。
- **serializer\_init** 和 **serializer\_deinit** 能初始化並取消初始化序列化程式程式庫。
- **IoTHubClient\_Create** 和 **IoTHubClient\_Destroy** 能使用連線到您 IoT 中樞的裝置認證建立用戶端控制代碼 (**iotHubClientHandle**)。

程式在 **remote\_monitoring\_run** 函式的主要區段中，會使用 **iotHubClientHandle** 控制代碼執行下列作業：

- 建立 Contoso 控溫器模型的執行個體，並針對這兩個命令設定訊息回呼。
- 使用序列化程式程式庫將有關裝置本身的資訊 (包括其支援的命令) 傳送給您的 IoT 中樞。當中樞接收到此訊息時，它會將儀表板中的裝置狀態從 [待決] 變更為 [執行中]。
- 啟動一個 **while** 迴圈，並於每秒將溫度、外部溫度及濕度的值傳送到 IoT 中樞。

做為參考，以下是啟動時會傳送到 IoT 中樞的範例 **DeviceInfo** 訊息︰

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

做為參考，以下是傳送到 IoT 中樞的範例 **Telemetry** 訊息︰

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

做為參考，以下是從 IoT 中樞收到的範例 **Command**︰

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### 建置並執行程式

1. 按一下 [編譯] 來建置程式。您可以安全地略過任何警告，但如果建置產生錯誤，請修正錯誤後再繼續。

2. 如果建置成功，mbed 編譯器網站會產生含有您專案名稱的 .bin 檔案，並將它下載到您的本機電腦。將 .bin 檔案複製到裝置。如果將 .bin 檔案儲存到裝置，將會導致裝置重新啟動並執行包含在 .bin 檔案中的程式。您可以按 mbed 裝置上的 [重設] 按鈕來隨時手動重新啟動程式。

3. 使用 SSH 用戶端應用程式 (例如 PuTTY) 連接至裝置。您可以查看 [Windows 裝置管理員] 來確定裝置使用的序列埠。

    ![][11]

4. 在 PuTTY 中，按一下 [序列] 連接類型。裝置通常的連線傳輸速率是 115200，因此請在 [速度] 方塊中輸入 115200。然後按一下 [開啟]。

5. 程式開始執行。連線時如果程式沒有自動啟動，您可能必須重設面板 (按 CTRL + Break 或按面板的重設按鈕)。

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer

<!---HONumber=AcomDC_0413_2016-->