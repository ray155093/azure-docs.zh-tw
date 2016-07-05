<properties
	pageTitle="使用 IoT 中心從裝置上傳檔案 | Microsoft Azure"
	description="請遵循本教學課程以了解如何從使用 Azure IoT 中心搭配 C# 的裝置上傳檔案。"
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# 教學課程：如何使用 IoT 中樞將檔案從裝置上傳到雲端

## 簡介

Azure IoT 中樞是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。先前的教學課程 ([開始使用 IoT 中樞]和[使用 IoT 中樞傳送雲端到裝置訊息]) 說明了 IoT 中樞基本的裝置到雲端和雲端到裝置訊息功能，而[處理裝置到雲端訊息]教學課程會討論如何在 Azure Blob 儲存體中可靠地儲存裝置到雲端訊息的方式。不過，在某些情況下，您無法輕易地將裝置傳送的資料對應到 IoT 中樞接受且相對較小的裝置到雲端訊息。範例涵蓋包含圖片、影片、高頻率震動資料範例，或是包含部分前置處理資料形式的大型檔案。這些檔案通常會使用工具 (例如 [Azure Data Factory] 或 [Hadoop] 堆疊) 在雲端中進行批次處理。若偏好從裝置上傳檔案來傳送事件，您仍然可以使用 IoT 中樞安全性與可靠性功能。

本教學課程是以[使用 IoT 中樞傳送雲端到裝置訊息]教學課程中的程式碼來建置，示範如何使用 IoT 中樞的檔案上傳功能。其中示範如何透過 Azure Blob URI 安全地提供裝置來上傳檔案，以及如何使用 IoT 中樞檔案上傳通知來觸發應用程式後端的檔案處理。

在本教學課程結尾，您會執行兩個 Windows 主控台應用程式：

* **SimulatedDevice**，此為[使用 IoT 中樞傳送雲端到裝置訊息]教學課程中建立的應用程式修改版本，可以使用 IoT 中樞提供的 SAS URI，將檔案上傳到儲存體。
* **ReadFileUploadNotification**，它會接收來自 IoT 中樞的檔案上傳通知。

> [AZURE.NOTE] IoT 中樞透過 Azure IoT 裝置 SDK 來支援許多裝置平台和語言 (包括 C、Java 及 Javascript)。如需如何將您的裝置連接到本教學課程中所示的程式碼 (通常是連接到 Azure IoT 中樞) 的逐步指示，請參閱 [Azure IoT 開發人員中心]。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank")。

[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1. 在 Visual Studio 中，在您的方案上按一下滑鼠右鍵，然後選取 [設定啟始專案]。選取 [多個啟始專案]，然後針對 **ReadFileUploadNotification** 和 **SimulatedDevice** 選取 [啟動]。

2. 按 **F5**。這兩個應用程式應該都會啟動。您應該會在其中一個主控台應用程式中看到上傳已完成，以及另一個主控台應用程式所收到的上傳通知訊息。您可以使用 [Azure 入口網站]或 Visual Studio 伺服器總管，在您的儲存體帳戶中檢查上傳的檔案是否存在。

  ![][50]


## 後續步驟

在本教學課程中，您已學到如何運用 IoT 中樞的檔案上傳功能來簡化從裝置上傳檔案。您可以利用下列教學課程繼續探索 IoT 中心功能和案例：

- [處理裝置到雲端訊息]，示範如何可靠地處理來自裝置的遙測和互動式訊息。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md

[Azure 入口網站]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[使用 IoT 中樞傳送雲端到裝置訊息]: iot-hub-csharp-csharp-c2d.md
[處理裝置到雲端訊息]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[IoT 中心概觀]: iot-hub-what-is-iot-hub.md
[IoT 中心指引]: iot-hub-guidance.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[開始使用 IoT 中樞]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0622_2016-->