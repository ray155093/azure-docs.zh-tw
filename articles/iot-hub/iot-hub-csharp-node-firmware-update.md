---
title: "使用 Azure IoT 中樞進行裝置韌體更新 (.NET/Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞上的裝置管理來起始裝置韌體更新。 您可以使用適用於 Node.js 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式以觸發韌體更新。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: 5b8aaa7e7b04224fd51c264822d619866e0161af


---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>使用裝置管理來起始裝置韌體更新 (.NET/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>簡介
在[開始使用裝置管理][lnk-dm-getstarted]教學課程中，您已了解如何使用[裝置對應項][lnk-devtwin]和[直接方案][lnk-c2dmethod]基礎，從遠端重新啟動裝置。 本教學課程使用相同的 IoT 中樞基元，並示範如何進行端對端模擬韌體更新。  此模式用於 [Raspberry Pi 裝置實作範例][lnk-rpi-implementation]的韌體更新實作。

本教學課程說明如何：

* 建立 .NET 主控台應用程式，以透過您的 IoT 中樞在模擬裝置應用程式中呼叫 firmwareUpdate 直接方法。
* 建立模擬裝置應用程式以實作 firmwareUpdate 直接方法，該方法會經過多重階段的處理，等待下載韌體映像、下載韌體映像，最後套用韌體映像。  在執行每個階段時，裝置會使用報告屬性來更新進度。

在本教學課程結束時，您會有 Node.js 主控台裝置應用程式和 .NET (C#) 主控台後端應用程式：

**dmpatterns_fwupdate_service.js**：在模擬裝置應用程式中呼叫直接方法、顯示回應，並定期 (每 500 毫秒) 顯示更新的報告屬性。

**TriggerFWUpdate**會將您的 IoT 中樞連接至稍早建立的裝置身分識別，接收 firmwareUpdate 直接方法，透過多重狀態處理執行以模擬韌體更新，包括等待映像下載、下載新的映像，最後套用映像。

若要完成此教學課程，您需要下列項目：

* Microsoft Visual Studio 2015。
* Node.js 0.12.x 版或更新版本， <br/>  [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

請依照[開始使用裝置管理](iot-hub-csharp-node-device-management-get-started.md)文件，以建立 IoT 中樞及取得 IoT 中樞連接字串。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端韌體更新
在本節中，您會建立 .NET 主控台應用程式 (使用 C#)，使用直接方法在裝置上起始遠端韌體更新，並使用裝置對應項查詢來定期取得該裝置上作用中韌體更新的狀態。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **TriggerFWUpdate**。

    ![新的 Visual C# Windows 傳統桌面專案][img-createapp]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **TriggerFWUpdate** 專案，然後按一下 [管理 NuGet 套件]。
3. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 服務 SDK][lnk-nuget-service-sdk] NuGet 套件與其相依項目，並加入對它的參考。

    ![NuGet 封裝管理員視窗][img-servicenuget]
4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using Microsoft.Azure.Devices;
        
5. 將下列欄位新增到 **Program** 類別。 將多個預留位置的值替換為您在上一節中為中樞所建立的 IoT 中樞連接字串。
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. 將下列方法加入至 **Program** 類別：
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. 將下列方法加入至 **Program** 類別：

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. 最後，將下列幾行加入至 **Main** 方法：
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. 建置方案。

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您將：

* 建立 Node.js 主控台應用程式，以回應雲端所呼叫的直接方法
* 觸發模擬韌體更新
* 使用報告屬性來啟用裝置對應項查詢，以識別裝置及其上次完成韌體更新的時間

1. 建立稱為 **manageddevice**的新的空資料夾。  在 **manageddevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。  接受所有預設值：
   
    ```
    npm init
    ```
2. 在命令提示字元中，於 **manageddevice** 資料夾中執行下列命令來安裝 **azure-iot-device** 裝置 SDK 套件和 **azure-iot-device-mqtt** 套件：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文字編輯器，在 **manageddevice** 資料夾中建立新的 **dmpatterns_fwupdate_device.js** 檔案。
4. 在 **dmpatterns_fwupdate_device.js** 檔案開頭新增下列 'require' 陳述式：
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. 新增 **connectionString** 變數，並用它來建立**用戶端**執行個體。  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 新增下列函式，用來更新報告屬性
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
7. 新增下列函式，模擬韌體映像的下載和套用。
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. 新增下列函式，透過報告屬性來等待下載，以更新韌體更新狀態。  一般而言，會通知裝置可用的更新，系統管理員會定義原則讓裝置開始下載並套用更新。  這是讓該原則執行的邏輯所在位置。  為了簡單起見，我們延遲 4 秒，然後繼續下載韌體映像。 
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. 新增下列函式，透過報告屬性來下載韌體映像，以更新韌體更新狀態。  然後它會藉由模擬韌體下載，最後更新韌體更新狀態，以通知下載成功或失敗。
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. 新增下列函式，透過報告屬性來套用韌體映像，以更新韌體更新狀態。  然後它會藉由模擬套用韌體映像，最後更新韌體更新狀態，以通知套用成功或失敗。
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. 新增下列函式，處理 **firmwareUpdate** 方法並起始多階段韌體更新程序。
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. 最後，新增下列程式碼，以裝置形式連接到 IoT 中樞。 
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。
> 
> 

## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。

1. 在 **manageddevice** 資料夾中，於命令提示字元中執行下列命令以開始接聽重新啟動直接方法。
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. 執行 C# 主控台應用程式 **TriggerFWUpdate** - 以滑鼠右鍵按一下 **TriggerFWUpdate** 專案，選取 [偵錯] 和 [啟動新的執行個體]。

3. 您會在主控台中看到直接方法的裝置回應。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您使用直接方法來觸發裝置上的遠端韌體更新，並且定期使用報告屬性，以了解韌體更新處理的進度。  

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/


<!--HONumber=Dec16_HO1-->


