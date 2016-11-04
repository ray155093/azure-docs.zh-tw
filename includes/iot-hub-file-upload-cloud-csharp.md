## 接收檔案上傳通知
在本節中，您將撰寫 Windows 主控台應用程式，它會接收來自 IoT 中樞的檔案上傳通知訊息。

1. 在目前的 Visual Studio 方案中，使用 [主控台應用程式] 專案範本來建立新的 Visual C# Windows 專案。將專案命名為 **ReadFileUploadNotification**。
   
    ![Visual Studio 中的新專案][2]
2. 在 [方案總管] 中，以滑鼠右鍵按一下 **ReadFileUploadNotification** 專案，然後按一下 [管理 NuGet 封裝]。
   
    此時會顯示 [管理 NuGet 封裝] 視窗。
3. 搜尋 `Microsoft.Azure.Devices`，然後按一下 [**安裝**] 並接受使用條款。
   
    這會下載及安裝參考，並將參考加入 **ReadFileUploadNotification** 專案中的 [Azure IoT - 服務 SDK NuGet 封裝]。
4. 在 **Program.cs** 檔的頂端，新增下列陳述式：
   
        using Microsoft.Azure.Devices;
5. 將下列欄位新增到 **Program** 類別。將預留位置的值替換成[開始使用 IoT 中樞] 中的 IoT 中樞連接字串：
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. 將下列方法加入至 **Program** 類別：
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();
   
            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();
   
                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }
   
    請注意，這裡的接收模式，與用來從裝置應用程式接收雲端到裝置訊息的模式相同。
7. 最後，將下列幾行加入至 **Main** 方法：
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[Azure IoT - 服務 SDK NuGet 封裝]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[開始使用 IoT 中樞]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[2]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0622_2016-->