<properties
	pageTitle="使用 IoT 中樞傳送雲端到裝置訊息 | Microsoft Azure"
	description="請遵循本教學課程，以了解如何搭配 C# 使用 Azure IoT 中心傳送雲端到裝置的訊息。"
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
     ms.date="06/23/2016"
     ms.author="elioda"/>

# 教學課程：如何使用 IoT 中樞和 .Net 傳送雲端到裝置訊息

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## 簡介

Azure IoT 中樞是一項完全受管理的服務，有助於讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。[IoT 中心入門]教學課程會示範如何建立 IoT 中心、在其中佈建裝置識別，以及編寫模擬的裝置，以傳送裝置到雲端的訊息。

本教學課程是以[開始使用 IoT 中樞]為基礎。這會說明如何：

- 從您的應用程式雲端後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。
- 接收裝置上的雲端到裝置訊息。
- 從您的應用程式雲端後端，要求確認收到從 IoT 中樞傳送到裝置的訊息 (*意見反應*)。

您可以在 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]中，找到有關雲端到裝置訊息的詳細資訊。

在本教學課程結尾，您將會執行兩個 Windows 主控台應用程式：

* **SimulatedDevice**，[IoT 中心入門]中建立之應用程式的修改版本，可連接到您的 IoT 中心，並接收雲端到裝置的訊息。
* **SendCloudToDevice**：會透過 IoT 中樞，將雲端到裝置訊息傳送到模擬裝置，然後接收其傳遞通知。

> [AZURE.NOTE] 「IoT 中樞」透過 Azure IoT 裝置 SDK 為許多裝置平台和語言 (包括 C、Java 及 Javascript) 提供 SDK 支援。如需有關如何將您的裝置與本教學課程中的程式碼連接 (通常是連接到「Azure IoT 中樞」) 的逐步指示，請參閱 [Azure IoT 開發人員中樞]。

若要完成此教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015

+ 使用中的 Azure 帳戶。(如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。)

## 在模擬裝置上接收訊息

在本節中，您會修改在[開始使用 IoT 中樞]中建立的模擬裝置應用程式，以接收來自 IoT 中樞的雲端對裝置訊息。

1. 在 Visual Studio 的 **SimulatedDevice** 專案中，將下列方法加入 [程式] 類別。

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    `ReceiveAsync` 方法會以非同步方式，在裝置收到訊息時，傳回收到的訊息。它會在可指定的逾時期間過後傳回「null」(在本例中，使用的是預設值 1 分鐘)。當發生這種情況時，程式碼應該繼續等待新訊息。所以加上 `if (receivedMessage == null) continue` 行。

    對 `CompleteAsync()` 的呼叫會通知 IoT 中樞，說明已成功處理訊息。可以安全地從裝置佇列中移除該訊息。如果因故導致裝置應用程式無法完成訊息處理作業，「IoT 中樞」將會重新傳遞該訊息。因此，裝置應用程式中的訊息處理邏輯必須是「等冪」，如此一來，多次接收相同的訊息才會產生相同的結果。應用程式也可以暫時放棄訊息，這會使得「IoT 中樞」將訊息保留在佇列中以供未來取用。或者，應用程式可以拒絕訊息，這會將訊息從佇列中永久移除。如需有關雲端到裝置訊息生命週期的詳細資訊，請參閱 [IoT 中樞開發人員指南][IoT Hub Developer Guide - C2D]。

    > [AZURE.NOTE] 使用 HTTP/1 而不使用 AMQP 做為傳輸時，`ReceiveAsync` 方法會立即傳回。使用 HTTP/1 時，針對雲端到裝置訊息支援的模式是裝置以間歇方式連接而不常檢查訊息 (低於每 25 分鐘一次)。發出更多 HTTP/1 接收會導致「IoT 中樞」對要求進行節流。如需有關 AMQP 與 HTTP/1 支援間的差異，以及 IoT 中樞節流的詳細資訊，請參閱 [IoT 中樞開發人員指南][IoT Hub Developer Guide - C2D]。

2. 緊接在 `Console.ReadLine()` 行前面，將下列方法新增到 **Main** 方法中：

        ReceiveC2dAsync();

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在生產環境程式碼中，您應該如 MSDN 文章[暫時性錯誤處理]所建議，實作重試原則 (例如指數型輪詢)。

## 從應用程式後端傳送雲端到裝置訊息

在本節中，您會撰寫 Windows 主控台應用程式，將雲端到裝置訊息傳送至模擬裝置應用程式。

1. 在目前的 Visual Studio 方案中，使用 [主控台應用程式] 專案範本來建立新的「Visual C# 傳統型應用程式」專案。將專案命名為 **SendCloudToDevice**。

    ![Visual Studio 中的新專案][20]

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後按一下 [**管理方案的 NuGet 封裝...**]。

	這會開啟 [管理 NuGet 封裝] 視窗。

3. 搜尋 `Microsoft Azure Devices`，然後按一下 [**安裝**] 並接受使用條款。

	這會下載及安裝參考，並將其加入 [Azure IoT - 服務 SDK NuGet 封裝]。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

		using Microsoft.Azure.Devices;

5. 將下列欄位新增到 **Program** 類別。將預留位置的值替換成[開始使用 IoT 中樞]中的 IoT 中樞連接字串：

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. 將下列方法加入至 **Program** 類別：

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	這個方法會將新的雲端到裝置訊息傳送給識別碼為 `myFirstDevice` 的裝置。若您修改了[開始使用 IoT 中樞]內的方法，則需一併變更此參數。

7. 最後，將下列幾行加入至 **Main** 方法：

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. 從 Visual Studio 中，在您的方案上按一下滑鼠右鍵，然後選取 [設定啟始專案...]。選取 [多個啟始專案]，然後同時針對 **ProcessDeviceToCloudMessages**、**SimulatedDevice** 以及 **SendCloudToDevice** 選取 [啟動] 動作。

9.  按 **F5**。三個應用程式應該全部都會啟動。選取 [SendCloudToDevice] 視窗，然後按 **Enter**。您應該會看到模擬應用程式正在接收訊息。

    ![正在接收訊息的應用程式][21]

## 接收傳遞意見反應
您可以向「IoT 中樞」要求每個雲端到裝置訊息的傳遞 (或到期) 通知。如此一來，雲端後端就能輕鬆地通知重試或補償邏輯。如需有關雲端到裝置意見反應的詳細資訊，請參閱 [IoT 中樞開發人員指南][IoT Hub Developer Guide - C2D]。

在本節中，您會修改 **SendCloudToDevice** 應用程式以要求意見反應，然後從「IoT 中樞」接收意見反應。

1. 在 Visual Studio 的 **SendCloudToDevice** 專案中，將下列方法加入至 **Program** 類別。
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    請注意，這裡的接收模式，與用來從裝置應用程式接收雲端到裝置訊息的模式相同。

2. 緊接在 `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` 行後面，將下列方法新增到 **Main** 方法中：

        ReceiveFeedbackAsync();

3. 為了要求雲端到裝置訊息傳遞狀況的意見反應，您必須在 **SendCloudToDeviceMessageAsync** 方法中指定屬性。將以下行加入 `var commandMessage = new Message(...);` 行之後：

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  按 **F5** 來執行應用程式。您應該會看到三個應用程式全部都啟動。選取 [SendCloudToDevice] 視窗，然後按 **Enter**。您應該會看到模擬應用程式正在接收訊息，而幾秒之後，則會看到您的 **SendCloudToDevice** 應用程式正在接收意見反應訊息。

    ![正在接收訊息的應用程式][22]

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在生產環境程式碼中，您應該如 MSDN 文章[暫時性錯誤處理]所建議，實作重試原則 (例如指數型輪詢)。

## 後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。您可以利用下列教學課程繼續探索 IoT 中樞功能和案例：

- [處理裝置到雲端訊息]，示範如何可靠地處理來自裝置的遙測和互動式訊息。
- [從裝置上傳檔案]，說明使用雲端到裝置訊息來幫助從裝置上傳檔案的模式。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言]
* [Azure IoT 開發人員中心]

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT - 服務 SDK NuGet 封裝]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[處理裝置到雲端訊息]: iot-hub-csharp-csharp-process-d2c.md
[從裝置上傳檔案]: iot-hub-csharp-csharp-file-upload.md

[IoT 中心概觀]: iot-hub-what-is-iot-hub.md
[IoT 中心指引]: iot-hub-guidance.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[支援的裝置平台和語言]: iot-hub-supported-devices.md
[IoT 中心入門]: iot-hub-csharp-csharp-getstarted.md
[開始使用 IoT 中樞]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot
[Azure IoT 開發人員中樞]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0706_2016-->