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

## 建立裝置識別

在本節中，您將建立 Windows 主控台應用程式，它會在 IoT 中樞的身分識別登錄中建立新的裝置身分識別。裝置無法連線到 IoT 中樞，除非它在裝置身分識別登錄中具有項目。如需詳細資訊，請參閱 [IoT 中心開發人員指南][lnk-devguide-identity]的**裝置識別登錄**一節。執行這個主控台應用程式時，它會產生唯一的裝置識別碼及金鑰，當裝置向 IoT 中樞傳送裝置對雲端訊息時，可以用來識別裝置本身。

1. 在 Visual Studio 中，使用**主控台應用程式**專案範本將新的 Visual C# Windows 傳統桌面專案加入至目前的方案。確定 .NET Framework 為 4.5.1 或更新版本。將專案命名為 **CreateDeviceIdentity**。

	![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **CreateDeviceIdentity** 專案，然後按一下 [管理 NuGet 封裝]。

3. 在 [NuGet 封裝管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、按一下 [安裝] 以安裝 [Microsoft.Azure.Devices] 封裝，並接受使用規定。

	![][11]

4. 這會下載及安裝 [Microsoft Azure IoT - 服務 SDK][lnk-nuget-service-sdk] NuGet 封裝與其相依項目，並加入其參考。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. 將下列欄位加入至 **Program** 類別，並將預留位置的值替換為您在上一節中為 IoT 中樞所建立的連接字串 ：

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. 將下列方法加入至 **Program** 類別：

		private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	這個方法會建立具有識別碼 **myFirstDevice** 的新裝置身分識別 (如果該裝置識別碼已經存在登錄中，程式碼就只會擷取現有的裝置資訊)。接著，應用程式會顯示該身分識別的主要金鑰。您將在此模擬裝置中使用此金鑰，連線到您的 IoT 中樞。

7. 最後，將下列幾行加入至 **Main** 方法：

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. 執行此應用程式，並記下裝置的金鑰。

    ![][12]

> [AZURE.NOTE] IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對中樞的安全存取。它會儲存裝置識別碼和金鑰，來做為安全性認證，以及啟用或停用旗標，讓您停用個別裝置的存取。如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]。

## 接收裝置到雲端的訊息

在本節中，您將建立 Windows 主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。IoT 中樞會公開與[事件中樞][lnk-event-hubs-overview]相容的端點以讓您讀取裝置到雲端訊息。為了簡單起見，本教學課程會建立的基本讀取器不適合用於高輸送量部署。[處理裝置到雲端的訊息][lnk-process-d2c-tutorial]教學課程會說明如何大規模處理裝置到雲端的訊息。[開始使用事件中樞][lnk-eventhubs-tutorial]教學課程則會提供進一步資訊，說明如何處理來自事件中樞的訊息，而且此教學課程也適用於 IoT 中樞事件中樞相容端點。

> [AZURE.NOTE] 用於讀取裝置到雲端訊息的事件中樞相容端點一律會使用 AMQPS 通訊協定。

1. 在 Visual Studio 中，使用**主控台應用程式**專案範本將新的 Visual C# Windows 傳統桌面專案加入至目前的方案。確定 .NET Framework 為 4.5.1 或更新版本。將專案命名為 **ReadDeviceToCloudMessages**。

    ![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **ReadDeviceToCloudMessages** 專案，然後按一下 [管理 NuGet 封裝]。

3. 在 [NuGet 封裝管理員] 視窗中，搜尋 **WindowsAzure.ServiceBus**，按一下 [安裝] 並接受使用規定。

    這會下載及安裝 [Azure 服務匯流排][lnk-servicebus-nuget]，並加入對它的參考和其所有相依性。此封裝可讓應用程式連接到 IoT 中樞上的事件中樞相容端點。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. 將下列欄位加入至 **Program** 類別，並將預留位置的值替換為您在*建立 IoT 中樞*一節中為 IoT 中樞所建立的連接字串：

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. 將下列方法加入至 **Program** 類別：

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    這個方法會使用 **EventHubReceiver** 執行個體接收來自所有 IoT 中樞裝置對雲端接收資料分割的訊息。請注意當您建立 **EventHubReceiver** 物件時如何傳遞 `DateTime.Now` 參數，使它只會收到它啟動後傳送的訊息。這很適合測試環境，因為如此一來您就可以看到目前的訊息集，但在生產環境中，您的程式碼應該要確定它能處理所有訊息，如需詳細資訊，請參閱[如何處理 IoT 中樞裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程。

7. 最後，將下列幾行加入至 **Main** 方法：

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## 建立模擬裝置應用程式

在本節中，您會撰寫 Windows 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中樞。

1. 在 Visual Studio 中，使用**主控台應用程式**專案範本將新的 Visual C# Windows 傳統桌面專案加入至目前的方案。確定 .NET Framework 為 4.5.1 或更新版本。將專案命名為 **SimulatedDevice**。

   	![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **SimulatedDevice** 專案，然後按一下 [管理 NuGet 封裝]。

3. 在 [NuGet 封裝管理員] 視窗中選取 [瀏覽]、搜尋 **Microsoft.Azure.Devices.Client**、按一下 [安裝] 以安裝 [Microsoft.Azure.Devices.Client] 封裝，並接受使用規定。

	這會下載及安裝 [Azure IoT - 裝置 SDK NuGet 封裝][lnk-device-nuget]與其相依項目，並加入其參考。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;


5. 在 [程式] 類別加入下列欄位，以*建立 IoT 中樞*小節中擷取的 IoT 中樞主機名稱和*建立裝置身分識別*小節中擷取的裝置金鑰取代預留位置值：

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. 將下列方法加入至 **Program** 類別：

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

	這個方法會每秒傳送新的裝置對雲端訊息。此訊息會包含 JSON 序列化物件及 deviceId 與隨機產生的數字，以模擬風向速度感應器。

7. 最後，將下列幾行加入至 **Main** 方法：

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  根據預設，**Create** 方法會建立一個使用 AMQP 通訊協定的 **DeviceClient** 執行個體來與 IoT 中樞通訊。若要使用 HTTPS 通訊協定，請使用可讓您指定通訊協定的 **Create** 方法的覆寫。若您選擇使用 HTTPS 通訊協定，您也應該將 **Microsoft.AspNet.WebApi.Client** NuGet 封裝新增至您的專案，以包含 **System.Net.Http.Formatting** 命名空間。

本教學課程會逐步引導您完成建立 IoT 中樞裝置用戶端的步驟。或者，您可以使用 [Azure IoT 中樞已連結的服務][lnk-connected-service] Visual Studio 擴充功能，將必要的程式碼新增至裝置用戶端應用程式。


> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。

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
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6

<!---HONumber=AcomDC_0511_2016-->