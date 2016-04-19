<properties
	pageTitle="適用於 C# 的 Azure IoT 中樞快速入門 | Microsoft Azure"
	description="採用 C# 的 Azure IoT 中樞快速入門教學課程。使用 Azure IoT 中樞與 C# 搭配 Microsoft Azure IoT SDK 來實作物聯網解決方案。"
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# 開始使用適用於 .NET 的 Azure IoT 中樞

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## 簡介

Azure IoT 中樞是一項完全受管理的服務，可在數百萬個物聯網 (IoT) 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。IoT 專案所面臨的其中一個最大挑戰，就是如何可靠且安全地將裝置連線至解決方案後端。若要解決這個問題，IoT 中樞：

- 提供可靠的裝置到雲端和雲端到裝置的超大規模傳訊。
- 使用每一裝置的安全性認證和存取控制來啟用安全通訊。
- 包括適用於最受歡迎的語言和平台的裝置程式庫。

本教學課程說明如何：

- 使用 Azure 入口網站來建立 IoT 中樞。
- 在您的 IoT 中樞中建立裝置身分識別。
- 建立模擬裝置，該裝置會傳送遙測到您的雲端後端，並從您的雲端後端接收命令。

在本教學課程結尾處，您將會有三個 Windows 主控台應用程式：

* **CreateDeviceIdentity**，這會建立裝置身分識別和相關聯的安全性金鑰，來連線您的模擬裝置。
* **ReadDeviceToCloudMessages**，其中顯示模擬的裝置所傳送的遙測。
* **SimulatedDevice**，這會使用先前建立的裝置身分識別連接到您的 IoT 中樞，並使用 AMQPS 通訊協定每秒傳送遙測訊息。

> [AZURE.NOTE] 文章 [IoT 中樞 SDK][lnk-hub-sdks] 提供可讓您可以用來建置兩個應用程式，以在裝置和您的方案後端上執行的各種 SDK 的相關資訊。

若要完成此教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015。

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

您現在已經建立 IoT 中樞，因此您已具有完成本教學課程的其餘部分所需的主機名稱和連接字串。

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.	在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下您的方案，然後按一下 [設定啟始專案]。選取 [多個啟始專案]，然後同時針對 **ReadDeviceToCloudMessages** 和 **SimulatedDevice** 專案選取 [啟動] 做為 [動作]。

   	![][41]

2.	按下 **F5** 來啟動這兩個執行的應用程式。來自 **SimulatedDevice** 應用程式的主控台輸出會顯示模擬的裝置傳送給您的 IoT 中樞的訊息，而來自 **ReadDeviceToCloudMessages** 應用程式的主控台輸出則會顯示您的 IoT 中樞接收的訊息。

   	![][42]

3. [Azure 入口網站][lnk-portal]中的 [使用量] 圖格會顯示傳送至中樞的訊息數目︰

    ![][43]


## 後續步驟

在本教學課程中，您在入口網站中設定了新的 IoT 中樞，然後在中樞的身分識別登錄中建立了裝置身分識別。您會將此裝置身分識別用於啟用模擬的裝置應用程式，以將裝置對雲端訊息傳送至中樞，並建立一個應用程式來顯示中樞所接收的訊息。您可以利用下列教學課程繼續探索 IoT 中樞功能和其他 IoT 案例：

- [使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d-tutorial]，示範如何將訊息傳送到裝置，並處理 IoT 中樞所產生的傳送意見反應。
- [處理裝置到雲端訊息][lnk-process-d2c-tutorial]，示範如何可靠地處理來自裝置的遙測和互動式訊息。
- [從裝置上傳檔案][lnk-upload-tutorial]說明使用雲端到裝置訊息來幫助從裝置上傳檔案的模式。

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0413_2016-->