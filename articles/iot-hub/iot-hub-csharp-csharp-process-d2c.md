<properties
	pageTitle="處理 IoT 中心裝置到雲端訊息 | Microsoft Azure"
	description="請依照此教學課程來學習處理 IoT 中心裝置到雲端訊息的有用模式。"
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/29/2016"
     ms.author="dobett"/>

# 教學課程：如何處理 IoT 中樞裝置到雲端訊息

## 簡介

Azure IoT 中樞是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。其他教學課程 ([IoT 中樞入門]和[使用 IoT 中樞傳送雲端到裝置訊息]) 說明如何使用 IoT 中樞的裝置到雲端和雲端到裝置的基本傳訊功能。

本教學課程是以 [IoT 中樞入門]中顯示的程式碼為基礎，以呈現兩種可用來處理裝置到雲端訊息的可調整模式：

- [Azure Blob 儲存體]中的可靠儲存體，用來儲存裝置到雲端的訊息。有一個很常見的案例是「冷路徑」分析，您可將遙測資料儲存於 Blob，以用來做為分析程序的輸入。這些程序是衍生自 [Azure Data Factory] 或 [HDInsight (Hadoop)] 堆疊等工具。

- 「互動式」裝置到雲端訊息的可靠處理。當裝置到雲端訊息因為應用程式後端的一組動作而立即觸發時，這類訊息會是互動式的。例如，裝置可能會傳送一則警示訊息，以觸發將票證插入 CRM 系統的作業。相較之下，「資料點」訊息只會饋送至分析引擎。例如，來自裝置且儲存來以供日後分析使用的溫度遙測就是資料點訊息。

由於 IoT 中樞會公開[事件中樞][lnk-event-hubs]相容端點以接收裝置到雲端訊息，因此本教學課程會使用 [EventProcessorHost] 執行個體。這個執行個體：

* 在 Azure Blob 儲存體中可靠地儲存「資料點」訊息。
* 將「互動式」裝置到雲端訊息轉寄到 [Azure 服務匯流排佇列]，以立即處理。

服務匯流排有助於確保能可靠地處理互動式訊息，因為它提供了各個訊息的檢查點，以及以時間範圍為基礎的重複資料刪除。

> [AZURE.NOTE] **EventProcessorHost** 執行個體是用來處理互動式訊息的唯一方式。其他選項包括 [Azure Service Fabric][lnk-service-fabric] 和 [Azure 串流分析][lnk-stream-analytics]。

在本教學課程結尾處，您將會執行三個 Windows 主控台應用程式：

* **SimulatedDevice**，這是在 [IoT 中樞入門]教學課程中建立之應用程式的已修改版本、每秒可傳送資料點裝置到雲端訊息，而且每 10 秒可傳送互動式裝置到雲端訊息。此應用程式會使用 AMQPS 通訊協定與 IoT 中樞進行通訊。
* **ProcessDeviceToCloudMessages** 會使用 [EventProcessorHost] 類別來擷取事件中樞相容端點的訊息。接著，可靠地將資料點訊息儲存於 Azure Blob 儲存體，並將互動式訊息轉送到服務匯流排佇列。
* **ProcessD2CInteractiveMessages** 可將互動式訊息從服務匯流排佇列中清除。

> [AZURE.NOTE] IoT 中樞對於許多裝置平台和語言 (包括 C、Java 和 JavaScript) 提供 SDK 支援。如需如何以實體裝置取代本教學課程中模擬的裝置，以及通常如何將裝置連接到 IoT 中樞的逐步指示，請參閱 [Azure IoT 開發人員中心]。

本教學課程可直接套用至事件中樞相容訊息的其他使用方式，例如 [HDInsight (Hadoop)] 專案。如需詳細資訊，請參閱 [Azure IoT 中樞開發人員指南 - 裝置到雲端]。

若要完成此教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015。

+ 使用中的 Azure 帳戶。<br/>如果您沒有 Azure 訂用帳戶，則只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/free/)。

您應具備 [Azure 儲存體]和 [Azure 服務匯流排]的基本知識。


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.	在 Visual Studio 的方案總管中，以滑鼠右鍵按一下您的方案，然後選取 [設定啟始專案]。選取 [多個啟始專案]，然後針對 **ProcessDeviceToCloudMessages**、**SimulatedDevice** 和 **ProcessD2CInteractiveMessages** 專案選取 [啟動] 動作。

2.	按 **F5** 啟動三個主控台應用程式。**ProcessD2CInteractiveMessages** 應用程式應處理從 **SimulatedDevice** 應用程式傳送的每則互動式訊息。

  ![三個主控台應用程式][50]

> [AZURE.NOTE] 若要查看您的 Blob 檔案中的更新，您需要將 **StoreEventProcessor** 類別中的 **MAX\_BLOCK\_SIZE** 常數降低為較小的值，例如 **1024**。這是因為達到模擬裝置所傳送之資料的區塊大小限制需要一些時間。區塊大小比較小，您就不需要等待這麼久才來查看所建立和更新的 Blob。不過，使用比較大的區塊大小可讓應用程式更有彈性。

## 後續步驟

在本教學課程中，您學到如何使用 [EventProcessorHost] 類別，可靠地處理資料點與互動式裝置到雲端訊息。

[從裝置上傳檔案]教學課程是根據本教學課程，使用類似的訊息處理邏輯來建置。其中也會說明使用雲端到裝置訊息來幫助從裝置上傳檔案的模式。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure Blob 儲存體]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Azure IoT 中樞開發人員指南 - 裝置到雲端]: iot-hub-devguide.md#d2c

[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/



[使用 IoT 中樞傳送雲端到裝置訊息]: iot-hub-csharp-csharp-c2d.md
[從裝置上傳檔案]: iot-hub-csharp-csharp-file-upload.md

[IoT 中心概觀]: iot-hub-what-is-iot-hub.md
[IoT 中心指引]: iot-hub-guidance.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[IoT 中樞入門]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/

<!---HONumber=AcomDC_0601_2016-->