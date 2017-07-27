---
title: "將 IoT 中樞訊息儲存至 Azure 資料儲存體 | Microsoft Docs"
description: "使用 Azure 函式應用程式將 IoT 中樞訊息儲存到 Azure 資料表儲存體。 IoT 中樞訊息包含 IoT 裝置所傳來之感應器資料一類的資訊。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 資料儲存體, iot 感應器資料存儲存體"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: c1f5d737b9718ead9c59794dae23798ef26aa42a
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017


---
# <a name="save-iot-hub-messages-that-contain-information-like-sensor-data-to-azure-table-storage"></a>將包含感應器資料等資訊的 IoT 中樞訊息儲存到 Azure 資料表儲存體

![端對端圖表](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-will-learn"></a>學習目標

您會了解如何建立 Azure 儲存體帳戶和 Azure 函式應用程式，以將 IoT 中樞訊息儲存在 Azure 資料表儲存體。

## <a name="what-you-will-do"></a>將執行的作業

- 建立 Azure 儲存體帳戶。
- 準備 IoT 中樞連線以讀取訊息。
- 建立並部署 Azure 函式應用程式。

## <a name="what-you-will-need"></a>必要元件

- 完成涵蓋下列需求的[設定裝置](iot-hub-raspberry-pi-kit-node-get-started.md)教學課程︰
  - 有效的 Azure 訂用帳戶。
  - 位於您訂用帳戶中的 Azure IoT 中樞。
  - 將訊息傳送到您 Azure IoT 中樞的執行中應用程式。

## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [新增] > [儲存體] > [儲存體帳戶]。
1. 輸入儲存體帳戶的必要資訊︰

   ![在 Azure 入口網站中建立儲存體帳戶](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   **名稱**︰儲存體帳戶的名稱。 此名稱必須是全域唯一的。

   **資源群組**︰使用 IoT 中樞所用的相同資源群組。

   **釘選至儀表板**︰核取此選項可讓您從儀表板輕鬆地存取 IoT 中樞。
1. 按一下 [建立] 。

## <a name="prepare-for-iot-hub-connection-to-read-messages"></a>準備 IoT 中樞連線以讀取訊息

IoT 中樞會公開內建的事件中樞相容端點，以便讓應用程式能夠讀取 IoT 中樞訊息。 同時，應用程式會使用取用者群組從 IoT 中樞讀取資料。 在建立 Azure 函式應用程式以從 IoT 中樞讀取資料之前，您必須︰

- 取得 IoT 中樞端點的連接字串。
- 為 IoT 中樞建立取用者群組。

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>取得 IoT 中樞端點的連接字串

1. 開啟 IoT 中樞。
1. 在 [IoT 中樞] 窗格上，按一下 [傳訊] 底下的 [端點]。
1. 在右窗格中，按一下 [內建端點] 底下的 [事件]。
1. 在 [屬性] 窗格中，將下列值記下來︰
   - 事件中樞相容端點
   - 事件中樞相容名稱

   ![在 Azure 入口網站中取得 IoT 中樞端點的連接字串](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

1. 在 [IoT 中樞] 窗格上，按一下 [設定] 底下的 [共用存取原則]。
1. 按一下 [iothubowner]。
1. 記下**主要金鑰**值。
1. 組成 IoT 中樞端點的連接字串，如下所示︰

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!Note]
   > 將 `<Event Hub-compatible endpoint>` 和 `<Primary key>` 替換為您記下的值。

### <a name="create-a-consumer-group-for-your-iot-hub"></a>為 IoT 中樞建立取用者群組

1. 開啟 IoT 中樞。
1. 在 [IoT 中樞] 窗格上，按一下 [傳訊] 底下的 [端點]。
1. 在右窗格中，按一下 [內建端點] 底下的 [事件]。
1. 在 [屬性] 窗格中，於 [取用者群組] 底下輸入名稱，並記下該名稱。
1. 按一下 [儲存] 。

## <a name="create-and-deploy-an-azure-function-app"></a>建立並部署 Azure 函式應用程式

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [新增] > [計算] > [函式應用程式]。
1. 輸入函式應用程式的必要資訊。

   ![在 Azure 入口網站中建立函式應用程式](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   **應用程式名稱**︰函式應用程式的名稱。 此名稱必須是全域唯一的。

   **資源群組**︰使用 IoT 中樞所用的相同資源群組。

   **儲存體帳戶**︰您建立的儲存體帳戶。

   **釘選至儀表板**︰核取此選項可讓您從儀表板輕鬆地存取函式應用程式。
1. 按一下 [建立] 。
1. 在函式應用程式建立之後將其開啟。
1. 在函式應用程式中建立新的函式。
   1. 按一下 [新函式]。
   1. 選取 [JavaScript] 作為 [語言]，選取 [資料處理] 作為 [案例]。
   1. 按一下 [建立此函式]，然後按一下 [新增 Function]。
   1. 選取 [JavaScript] 作為 [語言]，選取 [資料處理] 作為 [情節]。
   1. 按一下 **EventHubTrigger-JavaScript** 範本。
   1. 輸入範本的必要資訊。

      **替您的函式命名**：函式的名稱。

      **事件中樞名稱**：您記下的事件中樞相容名稱。

      **事件中樞連線**：按一下 [新增] 以新增您所組成之 IoT 中樞端點的連接字串。
   1. 按一下 [建立] 。
1. 設定函式的輸出。
   1. 按一下 [整合] > [新增輸出] > [Azure 資料表儲存體] > [選取]。

      ![在 Azure 入口網站中將資料表儲存體新增至函式應用程式](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)
   1. 輸入必要資訊。

      **資料表參數名稱**︰使用 `outputTable` 作為名稱，會用於 Azure Functions 的程式碼中。
      
      **資料表名稱**︰使用 `deviceData` 作為名稱。

      **儲存體帳戶連線**︰按一下 [新增] 並選取或輸入儲存體帳戶。 如果看不到儲存體帳戶，請參閱[儲存體帳戶需求](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)
      
   1. 按一下 [儲存] 。
1. 在 [觸發程序] 底下，按一下 [Azure 事件中樞 (myEventHubTrigger)]。
1. 在 [事件中樞取用者群組] 底下，輸入您所建立之取用者群組的名稱，然後按一下 [儲存]。
1. 按一下 [開發]，然後按一下 [檢視檔案]。
1. 使用下列程式碼來取代 `index.js` 中的程式碼，然後按一下 [儲存]。

   ```javascript
   'use strict';

   // This function is triggered each time a message is revieved in the IoTHub.
   // The message payload is persisted in an Azure Storage Table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

現在，您已建立函式應用程式。 它會將 IoT 中樞收到的訊息儲存在您的 Azure 資料表儲存體中。

> [!Note]
> 您可以使用 [執行] 按鈕來測試函式應用程式。 當您按一下 [執行]，系統就會將測試訊息傳送到 IoT 中樞。 訊息送達時應該會觸發函式應用程式來啟動，然後將訊息儲存至您的 Azure 資料表儲存體。 [記錄] 窗格會記錄處理程序的詳細資料。

## <a name="verify-your-message-in-your-table-storage"></a>確認訊息位於資料表儲存體中

1. 在裝置上執行應用程式範例以將訊息傳送至 IoT 中樞。
1. [下載並安裝 Microsoft Azure 儲存體總管](http://storageexplorer.com/)。
1. 開啟 Microsoft Azure 儲存體總管，按一下 新增 Azure 帳戶 > 登入，然後登入您的 Azure 帳戶。
1. 按一下您的 Azure 訂用帳戶 > [儲存體帳戶] > 您的儲存體帳戶 > [資料表] > [deviceData]。

   您應該會看到從裝置傳送到 IoT 中樞的訊息記錄在 `deviceData` 資料表中。

## <a name="next-steps"></a>後續步驟

您已成功建立 Azure 儲存體帳戶和 Azure 函式應用程式，以將您的 IoT 中樞收到的訊息儲存在 Azure 資料表儲存體中。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

