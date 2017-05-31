---
title: "使用 Azure IoT 中樞 (.NET/Node) 排定作業 | Microsoft Docs"
description: "如何排定 Azure IoT 中樞作業在多個裝置上叫用直接方法。 您可以使用適用於 Node.js 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式以執行作業。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: juanpere
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8990464037baca23882b14dfa8dc2009b0f41371
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="schedule-and-broadcast-jobs"></a>排程及廣播作業
[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

## <a name="introduction"></a>簡介
Azure IoT 中樞是一項完全受管理的服務，可讓後端應用程式建立作業來排定和更新數百萬個裝置，並追蹤作業。  作業可用於下列動作：

* 更新所需屬性
* 更新標籤
* 叫用直接方法

就概念而言，作業會包裝上述其中一個動作，然後針對由裝置對應項 (twin) 查詢所定義的一組裝置，追蹤執行進度。  例如，後端應用程式可以在 10,000 個裝置上使用作業叫用重新啟動方法，此方法由裝置對應項查詢指定並排定在未來執行。  接著，它可以追蹤每個裝置接收和執行重新啟動方法的進度。

從下列文章深入了解這當中的每一項功能：

* 裝置對應項和屬性：[開始使用裝置對應項][lnk-get-started-twin]和[教學課程：如何使用裝置對應項屬性][lnk-twin-props]
* 直接方法：[IoT 中樞開發人員指南 - 直接方法][lnk-dev-methods]和[教學課程：使用直接方法][lnk-c2d-methods]

本教學課程說明如何：

* 建立具有直接方法的模擬裝置應用程式，此直接方法可啟用後端應用程式能夠呼叫的 **lockDoor**。
* 建立 .NET 主控台應用程式，此應用程式會使用作業在模擬裝置應用程式中呼叫 **lockDoor** 直接方法，並使用裝置作業來更新所需屬性。

在本教學課程結束時，您會有 Node.js 主控台裝置應用程式和 .NET (C#) 主控台後端應用程式：

**simDevice.js** 會使用裝置身分識別連接到您的 IoT 中樞，並接收 **lockDoor** 直接方法。

**ScheduleJob** 會使用作業在模擬裝置應用程式中呼叫直接方法，並更新裝置對應項的所需屬性。

若要完成此教學課程，您需要下列項目：

* Visual Studio 2015 或 Visual Studio 2017。
* Node.js 0.12.x 版或更新版本。 [準備您的開發環境][lnk-dev-setup]一文說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。
* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>排定用於呼叫直接方法及更新裝置對應項 (twin) 屬性的作業
在本節中，您會建立一個 .NET 主控台應用程式 (使用 C#)，此應用程式會使用直接方法在裝置上起始遠端 **lockDoor**，並更新裝置對應項的屬性。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **ScheduleJob**。

    ![新的 Visual C# Windows 傳統桌面專案][img-createapp]

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **ScheduleJob** 專案，然後按一下 [管理 NuGet 套件...]。
1. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此步驟會下載及安裝 [Azure IoT 服務 SDK][lnk-nuget-service-sdk] NuGet 套件與其相依項目，並加入對它的參考。

    ![NuGet 封裝管理員視窗][img-servicenuget]
1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;

1. 新增下列 `using` 陳述式 (如果預設陳述式中尚不存在)。

        using System.Threading.Tasks;
        
1. 將下列欄位新增到 **Program** 類別。 將預留位置替換為您在上一節中為中樞所建立的 IoT 中樞連接字串。
   
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        
1. 將下列方法加入至 **Program** 類別：
   
        public static async Task MonitorJob(string jobId)
        {
            JobResponse result;
            do
            {
                result = await jobClient.GetJobAsync(jobId);
                Console.WriteLine("Job Status : " + result.Status.ToString());
                Thread.Sleep(2000);
            } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
        }
                
1. 將下列方法加入至 **Program** 類別：

        public static async Task StartMethodJob(string jobId)
        {
            CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

            JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
                "deviceId='myDeviceId'",
                directMethod,
                DateTime.Now,
                10);

            Console.WriteLine("Started Method Job");
        }

1. 將下列方法加入至 **Program** 類別：

        public static async Task StartTwinUpdateJob(string jobId)
        {
            var twin = new Twin();
            twin.Properties.Desired["Building"] = "43";
            twin.Properties.Desired["Floor"] = "3";
            twin.ETag = "*";

            JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
                "deviceId='myDeviceId'",
                twin,
                DateTime.Now,
                10);

            Console.WriteLine("Started Twin Update Job");
        }
 

1. 最後，將下列幾行加入至 **Main** 方法：
   
        jobClient = JobClient.CreateFromConnectionString(connString);

        string methodJobId = Guid.NewGuid().ToString();

        StartMethodJob(methodJobId);
        MonitorJob(methodJobId).Wait();
        Console.WriteLine("Press ENTER to run the next job.");
        Console.ReadLine();

        string twinUpdateJobId = Guid.NewGuid().ToString();

        StartTwinUpdateJob(twinUpdateJobId);
        MonitorJob(twinUpdateJobId).Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();

1. 在 [方案總管] 中，開啟 [設定起始專案...]，並確定 **ScheduleJob** 專案的 [動作] 是 [啟動]。 建置方案。

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您會建立 Node.js 主控台應用程式，它會回應雲端所呼叫的直接方法，這會讓模擬的裝置重新啟動，並使用報告屬性來啟用裝置對應項查詢，以識別裝置以及上次重新啟動的時機。

1. 建立名為 **simDevice** 的新空白資料夾。  在命令提示字元中，使用下列命令在 **simDevice** 資料夾中建立 package.json 檔案。  接受所有預設值：
   
    ```
    npm init
    ```
1. 在 **simDevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device** 和 **azure-iot-device-mqtt** 套件：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. 使用文字編輯器，在 [simDevice] 資料夾中建立新的 **simDevice.js** 檔案。
1. 在 **simDevice.js** 檔案的開頭新增下列 'require' 陳述式：
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
1. 新增 **connectionString** 變數，並用它來建立**用戶端**執行個體。 務必以適合您設定的值來取代預留位置。
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
1. 新增下列函式以處理 **lockDoor** 方法。
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
1. 新增下列程式碼以註冊 **lockDoor** 方法的處理常式。
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
1. 儲存並關閉 **simDevice.js** 檔案。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。
> 
> 

## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。

1. 在命令提示字元中，於 **simDevice** 資料夾中執行下列命令來開始接聽重新啟動直接方法。
   
    ```
    node simDevice.js
    ```
1. 執行 C# 主控台應用程式 **ScheduleJob** - 以滑鼠右鍵按一下 **ScheduleJob** 專案，然後選取 [偵錯] 和 [開始新執行個體]。

1. 您會看到來自裝置及後端應用程式的輸出。

    ![執行應用程式以排程作業][img-schedulejobs]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已使用作業來排定裝置的直接方法，以及更新裝置對應項 (twin) 的屬性。

若要繼續開始使用「IoT 中樞」和裝置管理模式 (例如遠端無線韌體更新)，請參閱[教學課程：如何進行韌體更新][lnk-fwupdate]。

若要繼續開始使用 IoT 中樞，請參閱[開始使用 IoT Edge][lnk-gateway-SDK]。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

