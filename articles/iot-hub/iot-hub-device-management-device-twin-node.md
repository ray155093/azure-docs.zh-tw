<properties
	pageTitle="IoT 中樞裝置管理對應項 | Microsoft Azure"
	description="Azure IoT 中樞裝置管理教學課程說明如何使用裝置對應項。"
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# 教學課程︰如何透過 Node.js 使用裝置對應項 (預覽)

[AZURE.INCLUDE [iot-hub-device-management-twin-selector](../../includes/iot-hub-device-management-twin-selector.md)]

## 簡介

Azure IoT 中樞裝置管理引進裝置對應項 (實體裝置的服務呈現)。下圖顯示裝置對應項的不同元件。

![][img-twin]

在本教學課程中，我們著重裝置屬性。若要深入了解其他元件，請參閱 [Azure IoT 中樞裝置管理概觀][lnk-dm-overview]。

裝置屬性是描述實體裝置之預先定義的屬性字典。實體裝置是每個裝置屬性的主要存放區，而且是每個對應值的授權存放區。這些屬性的「最終一致」呈現會儲存在雲端的裝置對應項中。一致性和有效性受限於同步處理設定，如下所述。一些裝置屬性範例包含韌體版本、電池電量和製造商名稱。

## 裝置屬性同步處理

實體裝置是裝置屬性的授權來源。透過 [LWM2M][lnk-lwm2m] 所描述的「觀察/通知」模式，自動將實體裝置上選取的值同步處理到 IoT 中樞內的裝置對應項。

實體裝置連接到 IoT 中樞時，服務會初始化所選取裝置屬性上的「觀察」。然後，實體裝置會「通知」 IoT 中樞有關裝置屬性的變更。為了實作磁滯現象，**pmin** (通知之間的最短時間) 設定為 5 分鐘。這表示，針對每個屬性，實體裝置每 5 分鐘只會通知 IoT 中樞一次，即使發生變更也是一樣。為了確保有效性，**pmax** (通知之間的最長時間) 設定為 6 小時。這表示，針對每個屬性，實體裝置每 6 小時至少會通知 IoT 中樞一次，即使屬性在這段時間內未曾變更也是一樣。

實體裝置中斷連接時，會停止同步處理。裝置重新連接到服務時，會重新啟動同步處理。您一律可以檢查屬性的上次更新時間，以確保有效性。

自動觀察到的完整裝置屬性清單如下︰

![][img-observed]

## 執行裝置對應項範例

下列範例延伸了[開始使用 Azure IoT 中樞裝置管理][lnk-get-started]教學課程的功能。從執行不同的模擬裝置開始，會使用裝置對應項來讀取和變更模擬裝置上的屬性。

### 必要條件 

執行此範例之前，您必須已完成[開始使用 Azure IoT 中樞裝置管理][lnk-get-started]中的步驟。這表示您的模擬裝置必須為執行中。如果您之前已完成程序，請立即重新啟動您的模擬裝置。

### 啟動範例

若要啟動範例，您需要執行 ```jobClient_devicePropertyReadWrite.js```。這會從裝置對應項和實體裝置讀取裝置屬性。它也會變更實體裝置上的裝置屬性。遵循下列步驟來啟動範例：

1.  從您複製 **azure-iot-sdks** 儲存機制的根資料夾，瀏覽至 **azure-iot-sdks/node/service/samples** 目錄。  

2.  開啟 **jobClient\_devicePropertyReadWrite.js**，以您的 IoT 中樞連接字串取代預留位置。

2.  執行 `node jobClient_devicePropertyReadWrite.js`。

您應該會在命令列視窗中看到顯示裝置對應項使用情形的輸出。本範例將逐步引導您完成下列程序：

1.  淺層讀取：印出裝置對應項的 `BatteryLevel` 和 `Timezone` 裝置屬性。

2.  深層讀取：讀取實體裝置中的 `Timezone` 裝置屬性。

3. 淺層讀取：印出裝置對應項的 `BatteryLevel` 和 `Timezone` 裝置屬性。

4.  深層寫入：將 **Timezone** 裝置屬性寫入實體裝置中。

5.  深層讀取：讀取實體裝置中的 **Timezone** 裝置屬性，以查看它是否已經變更。

6.  淺層讀取：印出裝置對應項的 `BatteryLevel` 和 `Timezone` 裝置屬性。

### 淺層讀取

「淺層」讀取與「深層」讀取/寫入有所差異。淺層讀取會傳回 Azure IoT 中樞內所儲存裝置對應項中的所要求屬性值。這會是先前通知作業的值。您無法執行淺層寫入，因為實體裝置是裝置屬性的授權來源。淺層讀取只會讀取裝置對應項中的屬性︰

```
deviceInfo.deviceProperties.BatteryLevel.value
```

若要判斷這些值的有效性，您可以檢查上次更新時間︰

```
deviceInfo.deviceProperties.BatteryLevel.lastUpdatedTime
```

同樣地，您可以讀取只儲存在裝置對應項中的服務屬性。它們不會同步處理到裝置。

### 深層讀取

深層讀取會啟動一個裝置作業，來讀取實體裝置中的所要求屬性值。裝置作業是在 [Azure IoT 裝置管理概觀][lnk-dm-overview]中引進，並在[教學課程︰如何使用裝置作業更新裝置韌體][lnk-dm-jobs]中詳述。深層讀取會提供最新裝置屬性值，因為有效性未受限於通知間隔。此作業會將訊息傳送到實體裝置，並僅使用所指定屬性的最新值來更新裝置對應項。它並不會重新整理整個裝置對應項。

```
scheduleDevicePropertyRead(jobId, deviceIds, propertyNames, done)
```

您無法執行服務屬性或標記的深層讀取，因為它們不會同步處理到裝置。

### 深層寫入

如果您想要變更可寫入的裝置屬性，則做法是使用深層寫入，以啟動裝置作業來寫入實體裝置的值。並非所有裝置屬性都是可寫入的。如需完整清單，請參閱 [Azure IoT 中樞裝置管理用戶端程式庫簡介][lnk-dm-library]附錄 A。

此作業會將訊息傳送到實體裝置，以更新指定的屬性。作業完成時，不會立即更新裝置對應項。您必須等到下一個通知間隔。同步處理之後，您可以使用淺層讀取來查看裝置對應項中的變更。

```
scheduleDevicePropertyWrite(jobId, deviceIds, properties, done)
```

### 裝置模擬器實作詳細資料

請調查您需要在裝置端執行的動作，來實作觀察/通知模式和深層讀取/寫入。

因為裝置屬性的同步處理完全是透過 Azure IoT 中樞 DM 用戶端程式庫處理，所以您只需要定期呼叫 API 來設定裝置屬性 (此範例中的電池電量) 即可。服務進行深層讀取時，會傳回您所設定的最後一個值。服務進行深層寫入時，會呼叫這個 set 方法。在 **iotdm\_simple\_sample.c** 中，您可以看到這類範例︰

```
int level = get_batterylevel();  // call to platform specific code 
set_device_batterylevel(0, level);
```

您可以實作回呼，而不是使用 set 方法。如需此選項的其他資訊，請參閱 [Azure IoT 中樞裝置管理程式庫簡介][lnk-dm-library]。

## 後續步驟

若要深入了解 Azure IoT 中樞裝置管理功能，您可以瀏覽下列教學課程︰

- [如何使用查詢找出裝置對應項][lnk-tutorial-queries]

- [如何使用裝置作業更新裝置韌體][lnk-dm-jobs]

- 裝置管理用戶端程式庫提供使用 [Intel Edison 裝置][lnk-edison]的端對端範例。

<!-- images and links -->
[img-twin]: media/iot-hub-device-management-device-twin/image1.png
[img-observed]: media/iot-hub-device-management-device-twin/image2.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-dm-library]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample

<!---HONumber=AcomDC_0622_2016-->