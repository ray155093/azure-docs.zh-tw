---
title: "開始使用裝置對應項 | Microsoft Docs"
description: "本教學課程說明如何使用裝置對應項"
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 4b90b2529fa6d763ed3ce9c3b4da07afeb860f0e


---
# <a name="tutorial-get-started-with-device-twins"></a>教學課程：開始使用裝置對應項
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程結束時，您將會有 .NET 和 Node.js 主控台應用程式：

* **AddTagsAndQuery.sln**，這是應該從後端執行的 .NET 應用程式，可新增標籤並查詢裝置對應項。
* **TwinSimulatedDevice.js**，這是會模擬裝置的 Node.js 應用程式，此裝置會以稍早建立的身分識別連接到您的 IoT 中樞，並報告其連線狀況。

> [!NOTE]
> [IoT 中樞 SDK][lnk-hub-sdks] 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
> 
> 

若要完成此教學課程，您需要下列項目：

* Microsoft Visual Studio 2015。
* Node.js 0.10.x 版或更新版本。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>建立服務應用程式
在本節中，您將建立一個 Node.js 主控台應用程式，此應用程式會將位置中繼資料新增至與 **myDeviceId** 相關聯的裝置對應項。 接著，它會選取位於美國的裝置來查詢儲存在 IoT 中樞的裝置對應項，再查詢會報告行動電話連線的對應項。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **AddTagsAndQuery**。
   
    ![新的 Visual C# Windows 傳統桌面專案][img-createapp]
2. 在 [方案總管] 中，以滑鼠右鍵按一下 **AddTagsAndQuery** 專案，然後按一下 [管理 NuGet 套件]。
3. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此程序會下載及安裝 [Microsoft Azure IoT 服務 SDK][lnk-nuget-service-sdk] Nuget 套件與其相依項目，並加入對它的參考。
   
    ![Nuget 套件管理員視窗][img-servicenuget]
4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using Microsoft.Azure.Devices;
5. 將下列欄位新增到 **Program** 類別。 將預留位置的值替換為您在上一節中為 IoT 中樞所建立的連接字串。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. 將下列方法加入至 **Program** 類別：
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    **RegistryManager** 類別會公開從服務來與裝置對應項進行互動時所需的所有方法。 先前的程式碼會先初始化 **registryManager** 物件，然後擷取 **myDeviceId** 的裝置對應項，最後會以所需的位置資訊來更新其標籤。
   
    在更新後，它會執行兩個查詢︰第一個只選取位於 **Redmond43** 工廠的裝置的裝置對應項，第二個會修改查詢，只選取也透過行動電話網路來連接的裝置。
   
    請注意，先前的程式碼在建立 **查詢** 物件時，指定傳回的最大文件數。 **query** 物件包含 **HasMoreResults** 布林值屬性，可用來多次叫用 **GetNextAsTwinAsync** 方法以擷取所有結果。 有一個稱為 **GetNextAsJson** 的方法適用於不是裝置對應項的結果，例如彙總查詢的結果。
7. 最後，將下列幾行加入至 **Main** 方法：
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();
8. 執行此應用程式，如果是查詢所有位於 **Redmond43** 中的裝置，您在結果中會看到一個裝置，而如果查詢將結果限於使用行動電話網路的裝置，則您不會看到任何裝置。
   
    ![視窗中的查詢結果][img-addtagapp]

在下一節，您將建立一個裝置應用程式，以報告連線資訊並變更上一節的查詢結果。

## <a name="create-the-device-app"></a>建立裝置應用程式
在本節中，您將建立一個 Node.js 主控台應用程式，此應用程式會以 **myDeviceId** 來連接到您的中樞，然後更新其報告屬性，以包含資訊來指出目前使用行動電話網路來連線。

1. 建立稱為 **reportconnectivity** 的新空白資料夾。 在 **reportconnectivity** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。 接受所有預設值：
   
    ```
    npm init
    ```
2. 在 **reportconnectivity** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device** 和 **azure-iot-device-mqtt** 套件：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文字編輯器，在 **reportconnectivity** 資料夾中建立新的 **ReportConnectivity.js** 檔案。
4. 將下列程式碼新增至 **ReportConnectivity.js** 檔案，並以您建立 **myDeviceId** 裝置身分識別時所複製的連接字串，取代 **{device connection string}** 預留位置︰
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 先前的程式碼在初始化 **Client** 物件之後，會擷取 **myDeviceId** 的裝置對應項，並以連線資訊來更新其報告屬性。
5. 執行裝置應用程式
   
        node ReportConnectivity.js
   
    您應該會看見訊息 `twin state reported`。
6. 現在，裝置已回報其連線資訊，它應該會出現在這兩個查詢中。 執行 .NET **AddTagsAndQuery** 應用程式，以再次執行查詢。 這次，**myDeviceId** 應該會出現在這兩個查詢結果中。
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>後續步驟
在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您已從後端應用程式將裝置中繼資料新增為標籤，並撰寫模擬裝置應用程式來報告裝置對應項中的裝置連線資訊。 您也了解如何使 類似 SQL 的 IoT 中樞查詢語言來查詢此資訊。

使用下列資源來了解如何：

* 從裝置傳送遙測，請參閱[開始使用 IoT 中樞][lnk-iothub-getstarted]教學課程，
* 使用裝置對應項的所需屬性來設定裝置，請參閱[使用所需的屬性來設定裝置][lnk-twin-how-to-configure]教學課程，
* 以互動方式控制裝置 (例如，從使用者控制的應用程式開啟風扇)，請參閱[使用直接方法][lnk-methods-tutorial]教學課程。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md




<!--HONumber=Nov16_HO5-->


