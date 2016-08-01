<properties
 pageTitle="遠端監視方案中的裝置資訊中繼資料 | Microsoft Azure"
 description="說明 Azure IoT 預先設定解決方案遠端監視及其架構。"
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
 ms.date="06/20/2016"
 ms.author="dobett"/>

# 遠端監視預先設定方案中的裝置資訊中繼資料

Azure IoT 套件遠端監視預先設定方案會示範用於管理裝置中繼資料的方法。本文將概述此方案為了讓您了解而採用的方法︰

- 方案會儲存哪些裝置中繼資料。
- 方案如何管理裝置中繼資料。

## Context

遠端監視預先設定方案會使用 [Azure IoT 中樞][lnk-iot-hub]，讓您的裝置將資料傳送至雲端。IoT 中樞包括[裝置身分識別登錄][lnk-identity-registry]，可控制 IoT 中樞的存取。IoT 中樞裝置身分識別登錄與儲存裝置資訊中繼資料的遠端監視方案專用「裝置登錄」彼此獨立。遠端監視方案使用 [DocumentDB][lnk-docdb] 資料庫來實作其裝置登錄，以便儲存裝置資訊中繼資料。[Microsoft Azure IoT 參考架構][lnk-ref-arch]說明典型 IoT 方案中裝置登錄的角色。

> [AZURE.NOTE] 遠端監視預先設定方案會讓裝置身分識別登錄與裝置登錄保持同步。兩者都使用相同的裝置識別碼來唯一識別連線到您的 IoT 中樞的每個裝置。

[IoT 中樞裝置管理預覽][lnk-dm-preview]會將類似於本文中所述遠端監視事先設定方案中裝置資訊管理功能的功能加入至 IoT 中樞。不過，在此階段，遠端監視方案只使用 IoT 中樞的公開上市 (GA) 功能。

## 裝置資訊中繼資料

儲存在裝置登錄 DocumentDB 資料庫中的裝置資訊中繼資料 JSON 文件具有下列結構︰

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**：裝置本身會寫入這些屬性，而且裝置是此資料的授權單位。其他範例裝置屬性包括製造商、型號及序號。
- **DeviceID**︰唯一的裝置識別碼。此值在 IoT 中樞裝置身分識別登錄中相同。
- **HubEnabledState**：IoT 中樞中裝置的狀態。這一開始設為 **null**，直到裝置第一次連接為止。在方案入口網站中，這會以「已註冊但不存在」的裝置表示。
- **CreatedTime**︰建立裝置的時間。
- **DeviceState**︰裝置所回報的狀態。
- **UpdatedTime**︰上次透過方案入口網站更新裝置的時間。
- **SystemProperties**︰方案入口網站會寫入系統屬性，而裝置並不知道這些屬性。如果方案已獲授權並連接到負責管理啟用 SIM 之裝置的服務，**ICCID** 即為系統屬性範例。
- **Commands**：裝置支援的命令清單。裝置會提供這項資訊給方案。
- **CommandHistory**︰遠端監視方案傳送給裝置的命令清單，以及這些命令的狀態。
- **IsSimulatedDevice**︰將裝置識別為模擬裝置的旗標。
- **id**︰此裝置文件的唯一 DocumentDB 識別碼。

> [AZURE.NOTE] 裝置訊息也可包含中繼資料，以描述裝置傳送給 IoT 中樞的遙測。遠端監視方案會使用此遙測中繼資料，自訂儀表板顯示[動態遙測][lnk-dynamic-telemetry]的方式。

## 生命週期

當您第一次在方案入口網站中建立裝置時，方案會在其裝置登錄中建立一個項目，如上所示。大部分的資訊一開始會虛設，而 **HubEnabledState** 會設為 **null**。此時方案也會在 IoT 中樞裝置身分識別登錄中為裝置建立一個項目，以產生裝置用來向 IoT 中樞進行驗證的金鑰。

當裝置第一次連接至方案時，它會傳送裝置資訊訊息。此裝置資訊訊息包括裝置屬性，例如裝置製造商、型號及序號。裝置資訊訊息也包含裝置支援的命令清單，其中包括任何命令參數的相關資訊。當方案收到此訊息時，它會更新裝置登錄中的裝置資訊中繼資料。

### 在方案入口網站中檢視和編輯裝置資訊

方案入口網站中的裝置清單會將下列裝置屬性顯示為資料行︰[狀態]、[裝置識別碼]、[製造商]、[型號]、[序號]、[韌體]、[平台]、[處理器] 和 [安裝的 RAM]。裝置屬性 [緯度] 和 [經度] 會支配儀表板上 Bing 地圖中的位置。

![裝置清單][img-device-list]

如果您在方案入口網站的 [裝置詳細資料] 窗格中按一下 [編輯]，您可以編輯上述所有屬性。編輯這些屬性會更新 DocumentDB 資料庫中裝置的記錄；不過，如果裝置傳送已更新的裝置資訊訊息，它會覆寫在方案入口網站中進行的所有變更。您無法在方案入口網站中編輯 **DeviceId**、**Hostname**、**HubEnabledState**、**CreatedTime**、**DeviceState** 和 **UpdatedTime** 屬性，因為只有裝置具備這些屬性的權限。

![裝置編輯][img-device-edit]

您可以使用方案入口網站，從您的方案中移除裝置。當您移除裝置時，方案會從方案裝置登錄中移除裝置資訊中繼資料，並移除 IoT 中樞裝置身分識別登錄中的裝置項目。您必須先停用裝置，才可以將它移除。

![裝置移除][img-device-remove]

## 裝置資訊訊息處理

裝置所傳送的裝置資訊訊息與遙測訊息不同，其中包含裝置屬性、裝置可以回應的命令和任何命令歷程記錄等資訊。IoT 中樞本身不知道裝置資訊訊息中內含的中繼資料，它會以處理任何裝置對雲端訊息的相同方式處理訊息。在遠端監視方案中，[Azure 串流分析][lnk-stream-analytics] (ASA) 作業會讀取來自 IoT 中樞的訊息。**DeviceInfo** 串流分析作業會篩選包含 **"ObjectType":"DeviceInfo"** 的訊息，並將這些訊息轉送至在 Web 作業中執行的 **EventProcessorHost** 主機執行個體。**EventProcessorHost** 執行個體中的邏輯會使用裝置識別碼來尋找特定裝置的 DocumentDB 記錄及更新記錄。裝置登錄記錄現在包含裝置屬性、命令和命令歷程記錄等資訊。

> [AZURE.NOTE] 裝置資訊訊息是標準的裝置對雲端訊息。方案會使用 ASA 查詢，以區分裝置資訊訊息與遙測訊息。

## 範例裝置資訊記錄

遠端監視預先設定方案會使用兩種裝置資訊記錄︰使用方案部署之模擬裝置的記錄，以及您連接至方案之自訂裝置的記錄。

### 模擬裝置

下列範例顯示模擬裝置的 JSON 裝置資訊記錄。此記錄已設定 **UpdatedTime** 的值，表示裝置已傳送 **DeviceInfo** 訊息給 IoT 中樞。此記錄包含一些常見的裝置數性、定義模擬裝置支援的六個命令，並將 **IsSimulatedDevice** 旗標設為 **1**。

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### 自訂裝置

下列範例顯示自訂裝置的 JSON 裝置資訊記錄，並將 **IsSimulatedDevice** 旗標設為 **0**。您可以看到此自訂裝置支援兩個命令，而且方案入口網站已傳送 **SetTemperature** 命令給裝置︰

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

下圖顯示裝置傳送的 JSON **DeviceInfo** 訊息，以更新裝置資訊中繼資料︰

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## 後續步驟

若要深入了解如何從自訂裝置傳送裝置資訊訊息，請參閱[將裝置連接至遠端監視預先設定方案][lnk-connect-custom]。

若要深入了解如何自訂預先設定的方案，請參閱[自訂預先設定的方案][lnk-customize]。

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-connect-custom]: iot-suite-connecting-devices.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

<!---HONumber=AcomDC_0720_2016-->