## 讓 Azure 儲存體帳戶與 IoT 中樞產生關聯
由於模擬裝置會在 Azure 儲存體 Blob 中上傳檔案，因此您必須擁有和 IoT 中樞關聯的 Azure 儲存體帳戶。您可以使用現有的儲存體帳戶，或是依照[關於 Azure 儲存體]中的指示建立新的帳戶。您可以依照[透過 Azure 入口網站管理 IoT 中樞]中的指示，讓 Azure 儲存體帳戶與 IoT 中樞產生關聯。

## 從模擬裝置上傳檔案
在本節中，您將修改在 [使用 IoT 中樞傳送雲端到裝置訊息] 中建立的模擬裝置應用程式，以接收來自 IoT 中樞的雲端到裝置訊息。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **SimulatedDevice** 專案，按一下 [新增]，然後按一下 [現有項目]。瀏覽至影像檔並將它包含在您的專案中。本教學課程假設影像名稱為`image.jpg`。
2. 以滑鼠右鍵按一下影像，然後按一下 [內容]。確定 [複製到輸出目錄] 是設為 [一律複製]。
   
    ![][1]
3. 在 **Program.cs** 檔的頂端，新增下列陳述式：
   
        using System.IO;
4. 將下列方法加入至 **Program** 類別：
   
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();
   
            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }
   
            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }
   
    `UploadToBlobAsync` 方法會取得要上傳之檔案的檔案名稱與資料流來源，然後處理上傳至儲存體的工作。主控台應用程式會顯示上傳檔案時使用的時間。
5. 將下列方法新增到 **Main** 方法中緊接在 `Console.ReadLine()` 行前面：
   
        SendToBlobAsync();

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。在生產環境程式碼中，您應該如 MSDN 文章[暫時性錯誤處理]所建議，實作重試原則 (例如指數型輪詢)。
> 
> 

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[關於 Azure 儲存體]: ../storage/storage-create-storage-account.md#create-a-storage-account
[透過 Azure 入口網站管理 IoT 中樞]: ../articles/iot-hub-manage-through-portal/#file-upload

<!-- Images -->
[1]: ../articles/iot-hub/media/iot-hub-csharp-csharp-file-upload/image-properties.png

<!---HONumber=AcomDC_0622_2016-->