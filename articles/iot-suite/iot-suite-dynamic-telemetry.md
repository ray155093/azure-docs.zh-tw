<properties
	pageTitle="使用動態遙測 |Microsoft Azure"
	description="遵循本教學課程以了解如何使用動態遙測搭配遠端監視預先設定解決方案。"
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# 搭配使用動態遙測與遠端監視預先設定解決方案

## 簡介

動態遙測可讓您將傳送至遠端監視預先設定解決方案的任何遙測視覺化。使用預先設定解決方案部署的模擬裝置會傳送溫度與濕度遙測，您可以在儀表板上將這些遙測視覺化。如果您自訂現有的模擬裝置、建立新的模擬裝置，或將實體裝置連線到預先設定解決方案，您可以傳送其他的遙測值，例如外部溫度、RPM 或風速。然後，您可以在儀表板上將此額外遙測視覺化。

本教學課程使用簡單的 Node.js 模擬裝置，您可以輕鬆地修改它來實驗動態遙測。

若要完成本教學課程，您需要：

- 有效的 Azure 訂用帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。
- [Node.js][lnk-node] 0.12.x 版或更新版本。

您可以在任何作業系統上完成本教學課程，例如 Windows 或 Linux，只要您可以在其中安裝 Node.js。

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## 設定 Node.js 模擬裝置

1. 在遠端監視儀表板上，按一下 [+ 新增裝置]，然後新增自訂裝置。記下 IoT 中樞主機名稱、裝置識別碼和裝置金鑰。當您在本教學課程稍後準備 remote\_monitoring.js 裝置用戶端應用程式時，需要這些資料。

2. 請確定 Node.js 0.12.x 版或更新版本已經安裝在您的開發電腦上。在命令提示字元或殼層中執行 `node --version` 來檢查版本。如需使用套件管理員在 Linux 上安裝 Node.js 的資訊，請參閱 [Installing Node.js via package manager (透過套件管理員安裝 Node.js)][node-linux]。

3. 安裝 Node.js 之後，請將最新版本的 [azure-iot-sdks][lnk-github-repo] 儲存機制複製到您的開發電腦。一律使用 **master** 分支取得最新版本的程式庫和範例。

4. 從您的本機複本 [azure-iot-sdks][lnk-github-repo] 儲存機制，將下列兩個檔案從 node/device/samples 資料夾複製到您開發電腦的空白資料夾中：

  - packages.json
  - remote\_monitoring.js

5. 開啟 remote\_monitoring.js 檔案並尋找下列變數定義：

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. 以您裝置的連接字串取代 **[IoT 中樞裝置連接字串]**。使用您在步驟 1 記下的 IoT 中樞主機名稱、裝置識別碼，以及裝置金鑰的值。裝置連接字串使用的格式如下：

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    如果 IoT 中樞主機名稱是 **contoso** 且裝置識別碼是 **mydevice**，連接字串看起來如下：

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. 儲存檔案。在殼層或命令提示字元中，於包含這些檔案的資料夾，執行下列命令以安裝必要的套件，然後執行範例應用程式：

    ```
    npm install
    node remote_monitoring.js
    ```

## 觀察運作中的動態遙測

儀表板會顯示現有模擬裝置的溫度與濕度遙測：

![預設儀表板][image1]

如果您選取在上一節執行的 Node.js 模擬裝置，您會看到溫度、濕度與外部溫度遙測：

![新增外部溫度到儀表板][image2]

遠端監視解決方案會自動偵測其他的外部溫度遙測類型，並將它新增到儀表板的圖表上。

## 新增遙測類型

下一個步驟是將 Node.js 模擬裝置產生的遙測以新的一組值取代：

1. 停止 Node.js 模擬裝置，方法是在命令提示字元或殼層中輸入 **Ctrl+C**。

2. 在 remote\_monitoring.js 檔案中，您可以查看現有溫度、濕度和外部溫度遙測的基底資料值。加入 **rpm** 的基底資料值，如下所示：

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js 模擬裝置會使用 remote\_monitoring.js 檔案中的 **generateRandomIncrement** 函式，對基底資料值加上隨機增量。在現有的隨機設定後方加上一行程式碼，將 **rpm** 的值隨機化，如下所示：

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. 將新的 rpm 值加入到裝置送給 IoT 中樞的 JSON 承載：

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. 使用下列命令，執行 Node.js 模擬裝置：

    ```
    node remote_monitoring.js
    ```

6. 觀察儀表板圖表上所顯示的新 RPM 遙測類型：

![新增 RPM 到儀表板][image3]

> [AZURE.NOTE] 您可能需要在儀表板中的 [裝置] 頁面停用 Node.js 裝置，然後再啟用以立即查看變更。

## 自訂儀表板顯示

**Device-Info** 訊息可能包含裝置可傳送給 IoT 中樞的遙測相關中繼資料。此中繼資料可指定裝置傳送的遙測類型。修改 remote\_monitoring.js 檔案中的 **deviceMetaData** 值，以在 **Commands** 定義後方包含 **Telemetry** 定義。以下程式碼片段會顯示 **Commands** 定義 (請務必在 **Commands** 定義後方加上 `,`)：

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] 遠端監視解決方案會使用區分大小寫的配對，來比對中繼資料定義與遙測串流中的資料。

以上方程式碼片段所示方式加入 **Telemetry** 定義，並不會變更儀表板的行為。不過，中繼資料也可以包含 **DisplayName** 屬性來自訂儀表板中的顯示。更新 **Telemetry** 中繼資料定義，如下列程式碼片段所示：

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

下列螢幕擷取畫面說明這項變更如何修改儀表板上的圖表圖例：

![自訂圖表圖例][image4]

> [AZURE.NOTE] 您可能需要在儀表板的 [裝置] 頁面上停用 Node.js 裝置，接著再次啟用，以立即查看變更。

## 篩選遙測類型

根據預設，儀表板上的圖表會顯示遙測串流中的每個資料數列。您可以使用 **Device-Info** 中繼資料，來隱藏圖表上特定遙測類型的顯示。

若要讓圖表只顯示溫度和濕度遙測，請從 **Device-Info** **Telemetry** 中繼資料省略 **ExternalTemperature**，如下所示：

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Outdoor Temperature (戶外溫度)** 不會再顯示在圖表上：

![篩選儀表板上的遙測][image5]

這項變更只會影響圖表顯示。**ExternalTemperature** 資料值仍會儲存，並可供任何後端處理使用。

> [AZURE.NOTE] 您可能需要在儀表板的 [裝置] 頁面上停用 Node.js 裝置，接著再次啟用，以立即查看變更。

## 處理錯誤

針對要在圖表上顯示的資料串流，其在 **Device-Info** 中繼資料中的 **Type** 必須符合遙測值的資料類型。例如，如果中繼資料指定濕度資料的 **Type** 必須為 **int**，而在遙測串流中找到 **double**，則濕度遙測將不會顯示在圖表上。不過，**濕度**的值仍會儲存，並可供任何後端處理使用。

## 後續步驟

既然您已了解如何使用動態的遙測，您可以進一步了解預先設定的解決方案如何使用裝置資訊︰[遠端監視預先設定方案中的裝置資訊中繼資料][lnk-devinfo]。

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks

<!---HONumber=AcomDC_0831_2016-->