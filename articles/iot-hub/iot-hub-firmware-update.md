---
title: 如何進行韌體更新 | Microsoft Docs
description: 本教學課程說明如何進行韌體更新
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere

---
# <a name="tutorial:-how-to-do-a-firmware-update-(preview)"></a>教學課程：如何進行韌體更新 (預覽)
## <a name="introduction"></a>簡介
在[開始使用裝置管理][lnk-dm-getstarted]教學課程中，您會看到如何使用[裝置 twin][lnk-devtwin] 和[雲端到裝置 (C2D) 方法][lnk-c2dmethod]基礎，遠端重新啟動裝置。 本教學課程會使用相同的 IoT 中樞基礎，並且提供指引和示範如何進行端對端模擬韌體更新。  此模式用於 Intel Edison 裝置範例的韌體更新實作。

本教學課程說明如何：

* 建立主控台應用程式，會透過您的 IoT 中樞在模擬裝置上呼叫 firmwareUpdate 直接方法。
* 建立模擬裝置，實作 firmwareUpdate 直接方法，該方法會經過多重階段的處理，等待下載韌體映像、下載韌體映像，最後套用韌體映像。  在執行每個階段時，裝置會使用 twin 報告屬性以更新進度。

在本教學課程結尾處，您會有兩個 Node.js 主控台應用程式：

**dmpatterns_fwupdate_service.js**，在模擬裝置上呼叫直接方法，顯示回應，並定期 (每 500 毫秒) 顯示更新的裝置 twin 報告屬性。

**dmpatterns_fwupdate_device.js**，這會將您的 IoT 中樞連接至稍早建立的裝置身分識別，接收 firmwareUpdate 直接方法，透過多重狀態處理執行以模擬韌體更新，包括等待映像下載、下載新的映像，最後套用映像。

若要完成此教學課程，您需要下列項目：

Node.js 0.12.x 版或更新版本， <br/>  [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。

使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。)

請依照[開始使用裝置管理](iot-hub-device-management-get-started.md)文件，以建立 IoT 中樞及取得連接字串。

[!INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您建立 Node.js 主控台應用程式，它會回應雲端所呼叫的直接方法，觸發模擬的裝置韌體更新，並使用裝置 twin 報告屬性，啟用裝置 twin 查詢以識別裝置以及上次重新啟動的時機。

1. 建立稱為 **manageddevice**的新的空資料夾。  在 **manageddevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。  接受所有預設值：
   
    ```
    npm init
    ```
2. 在 **manageddevice** 資料夾中，於命令提示字元執行下列命令以安裝**azure-iot-device@dtpreview**裝置 SDK 套件和 **azure-iot-device-mqtt@dtpreview** .套件：
   
    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```
3. 使用文字編輯器，在 **manageddevice** 資料夾中建立新的 **dmpatterns_fwupdate_device.js** 檔案。
4. 在 **dmpatterns_fwupdate_device.js** 檔案開頭新增下列 'require' 陳述式：
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. 新增 **connectionString** 變數，並用它來建立裝置用戶端。  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 新增下列函式，用來更新 twin 報告屬性
   
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
8. 新增下列函式，透過 twin 報告屬性來等待下載，以更新韌體更新狀態。  一般而言，會通知裝置可用的更新，系統管理員會定義原則讓裝置開始下載並套用更新。  這是讓該原則執行的邏輯所在位置。  為了簡單起見，我們延遲 4 秒，然後繼續下載韌體映像。 
   
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
9. 新增下列函式，透過 twin 報告屬性來下載韌體映像，以更新韌體更新狀態。  然後它會藉由模擬韌體下載，最後更新韌體更新狀態，以通知下載成功或失敗。
   
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
10. 新增下列函式，透過 twin 報告屬性來套用韌體映像，以更新韌體更新狀態。  然後它會藉由模擬套用韌體映像，最後更新韌體更新狀態，以通知套用成功或失敗。
    
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
11. 新增下列函式，處理 firmwareUpdate 方法並且初始化多重狀態韌體更新處理。
    
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
12. 最後，新增下列程式碼，連接到 IoT 中樞作為裝置， 
    
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

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端韌體更新
在本節中，您會建立 Node.js 主控台應用程式，使用直接方法在裝置上初始化遠端韌體更新，並使用裝置 twin 查詢來定期取得該裝置上作用中韌體更新的狀態。

1. 建立稱為 **triggerfwupdateondevice**的新的空資料夾。  在 **triggerfwupdateondevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。  接受所有預設值：
   
    ```
    npm init
    ```
2. 在 **triggerfwupdateondevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iothub** 裝置 SDK 套件以及 **azure-iot-device-mqtt** 套件：
   
    ```
    npm install azure-iot-hub@dtpreview --save
    ```
3. 使用文字編輯器，在 **triggerfwupdateondevice** 資料夾中建立新的 **dmpatterns_getstarted_service.js** 檔案。
4. 在 **dmpatterns_getstarted_service.js** 檔案開頭新增下列 'require' 陳述式：
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 新增下列變數宣告，並取代預留位置值︰
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. 新增下列函式來尋找並顯示 firmwareUpdate 報告屬性的值。
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. 新增下列函式來叫用 firmwareUpdate 方法，以重新啟動目標裝置︰
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. 最後，新增下列函式至程式碼，以啟動韌體更新順序，並且定期顯示 twin 報告屬性︰
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. 儲存並關閉 **dmpatterns_fwupdate_service.js** 檔案。

## <a name="run-the-applications"></a>執行應用程式
現在您已經準備好執行應用程式。

1. 在 **manageddevice** 資料夾中，於命令提示字元中執行下列命令以開始接聽重新啟動直接方法。
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. 在 **triggerfwupdateondevice** 資料夾中，於命令提示字元中執行下列命令以觸發裝置 twin 的遠端重新啟動和查詢以尋找最後的重新啟動時間。
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. 您會看到對直接方法的反應，方法是列印訊息

## <a name="next-steps"></a>後續步驟
在本教學課程中，您使用直接方法來觸發裝置上的遠端韌體更新，並且定期使用 twin 報告屬性，以了解韌體更新處理的進度。  

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs] 教學課程。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Oct16_HO2-->


