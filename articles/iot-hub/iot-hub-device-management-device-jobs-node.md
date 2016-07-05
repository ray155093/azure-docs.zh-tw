<properties
	pageTitle="IoT 中樞裝置管理裝置工作 | Microsoft Azure"
	description="Azure IoT 中樞裝置管理教學課程說明如何使用裝置工作來執行作業 (例如遠端韌體更新)。"
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

# 教學課程：如何使用裝置工作更新裝置韌體 (預覽)

[AZURE.INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## 簡介
Azure IoT 裝置管理可讓您使用裝置工作來與實體裝置進行互動。識別裝置對應項 (實體裝置的服務呈現) 之後，您可以使用裝置工作與其對應的實體裝置進行互動。裝置工作可協調多個裝置上的複雜程序。此程序可以包含多個步驟以及長時間執行的作業。

Azure IoT 中樞裝置管理目前提供六種類型的裝置作業 (我們會在客戶需要其他作業時新增作業)：

- **韌體更新**：更新實體裝置上的韌體 (或 OS 映像)。
- **重新啟動**：重新啟動實體裝置。
- **恢復出廠預設值**：將實體裝置的韌體 (或 OS 映像) 還原為儲存在裝置上的原廠提供備份映像。
- **組態更新**：設定實體裝置上執行的 IoT 中樞用戶端代理程式。
- **讀取裝置屬性**︰取得實體裝置上裝置屬性的最新值。
- **寫入裝置屬性**︰變更實體裝置上的裝置屬性。

如需如何使用這些作業的詳細資訊，請參閱 [API 文件][lnk-apidocs]。

您可以查詢作業記錄，了解所啟動作業的狀態。如需一些範例查詢，請參閱[我們的查詢運算式程式庫][lnk-query-samples]。

## 使用裝置工作執行韌體更新︰架構

下圖說明與單一實體裝置進行互動的韌體更新裝置工作。

![][img-architecture]

韌體更新裝置工作的步驟︰

1.  雲端架構 IoT 解決方案會啟動韌體更新裝置工作，並提供韌體封裝位置的 URI。IoT 解決方案負責將韌體封裝放在裝置可進行下載的位置。

2.  當實體裝置接收到此 URI 時，會自動從提供的 URI 開始進行下載。

3.  您在裝置上的程式碼會接收來自 IoT 中樞的要求，並從提供的位置 URI 下載韌體封裝。

4.  收到已完成下載裝置狀態訊息之後，裝置工作會指示裝置套用下載的韌體映像。

5.  裝置會在套用韌體映像之後重新開機，並重新連接到 IoT 中樞，然後通知 IoT 中樞已套用新韌體，接著裝置工作即完成。

## 執行韌體更新範例

下列範例延伸了[開始使用 Azure IoT 中樞裝置管理][lnk-get-started]教學課程的功能。從執行不同的模擬裝置開始，會啟動作業來更新這些裝置上的韌體。

### 必要條件 

執行此範例之前，您必須已完成[開始使用 Azure IoT 中樞裝置管理][lnk-get-started]中的步驟。這表示您的模擬裝置必須為執行中。如果您之前已完成程序，請立即重新啟動您的模擬裝置。

### 啟動範例

若要啟動範例，您需要執行 `jobClient_scheduleJob.js`。這會在所有模擬裝置上啟動韌體更新程序。遵循下列步驟來啟動範例：

1.  從您複製 **azure-iot-sdks** 儲存機制的根資料夾，瀏覽至 **azure-iot-sdks/node/service/samples** 目錄。  

2.  開啟 **jobClient\_scheduleJob.js**，以您的 IoT 中樞連接字串取代預留位置。

4.  執行 `node jobClient_scheduleJob.js`。

您應該會看到命令列視窗中的輸出，其中顯示有七個裝置工作追蹤六個模擬裝置的模擬韌體更新。

### 啟動裝置工作

一般而言，是在 **JobClient** 執行個體上使用多種 **schedule&lt;作業名稱&gt;** 方法來啟動裝置作業。在韌體更新範例中，是 **scheduleFirmwareUpdate** 方法。因為我們傳遞具有 6 個裝置識別碼的陣列，所以會啟動 7 個裝置工作：一個正在更新的裝置會有一個裝置工作，而且一個用來追蹤其他 6 個裝置工作的父裝置工作。

在下列片段中，會啟動韌體更新作業。此呼叫取用 **deviceId** 值的字串陣列作為參數，代表我們想要更新的裝置。

```
jobClient.scheduleFirmwareUpdate(jobId, deviceIds, packageURI, timeoutInMinutes, callback);
```

### 裝置模擬器實作詳細資料

在先前各節中，我們示範服務觀點 (如何啟動裝置工作以及查詢其狀態) 的韌體更新實作詳細資料。現在，我們將討論裝置方面上的對應實作。

Azure IoT 中樞裝置管理用戶端程式庫會處理裝置與服務之間的通訊，因此都是裝置特定邏輯的實作。這包含兩個部分：

- 實作裝置特定韌體更新程序︰這包含撰寫裝置特定邏輯來下載韌體封裝，並在下列適當的回呼中套用更新。在模擬的範例中，這是在[範例][lnk-github-firmware]中實作：

  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```

- 通知韌體更新程序的服務︰這包含修改韌體更新狀態和韌體更新結果欄位。達到此目的的方法是呼叫 **set\_firmwareupdate\_state** 和 **set\_firmwareupdate\_updateresult** API。在模擬的範例中，這是在[範例][lnk-github-firmware]中實作。

## 後續步驟

若要深入了解 Azure IoT 中樞裝置管理功能，您可以瀏覽下列教學課程︰

- Azure IoT 中樞 DM 用戶端程式庫提供使用 [Intel Edison 裝置][lnk-edison]的端對端範例。

- [如何使用裝置對應項][lnk-twin-tutorial]

- [如何使用查詢找出裝置對應項][lnk-tutorial-queries]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0622_2016-->