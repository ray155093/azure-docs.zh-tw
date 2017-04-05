---
title: "Azure IoT 中樞感應器資料的即時資料視覺效果 – Web Apps | Microsoft Docs"
description: "使用 Azure Web Apps 來視覺化收集自感應器並傳送至 Azure IoT 中樞的溫度和溼度資料。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "即時資料視覺效果, 即時資料視覺效果, 感應器資料視覺效果"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 788b3a6ce98c6668a8efc41f66c7e39b8aeda2db
ms.lasthandoff: 03/30/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>使用 Azure Web Apps 將來自 Azure IoT 中樞的即時感應器資料視覺化

![感應器、IoT 裝置、IoT 中樞與 Azure Web 應用程式 (web app) 之間的連接](media/iot-hub-live-data-visualization-in-web-apps/1_sensor-iot-device-azure-iot-hub-web-app-connection.png)

> [!NOTE]
> 開始本教學課程之前，請確定您已完成[將 ESP8266 連接到 Azure IoT 中樞](/iot-hub-arduino-huzzah-esp8266-get-started.md)。 在[將 ESP8266 連接到 Azure IoT 中樞](/iot-hub-arduino-huzzah-esp8266-get-started.md)，您會設定 IoT 裝置和 IoT 中樞，並部署要在裝置上執行的範例應用程式。 應用程式會將所收集的感應器資料傳送到您的 IoT 中樞。

## <a name="what-you-learn"></a>您學到什麼

在本課程中，您會了解如何執行 Azure Web 應用程式 (web app) 上所裝載的 Web 應用程式 (web application)，以將 Azure IoT 中樞收到的即時感應器資料視覺化。 如果您想嘗試使用 Power BI 將 IoT 中樞的資料視覺化，請參閱[使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>您要做什麼

- 在 Azure 入口網站中建立 Azure Web 應用程式 (web app)。
- 新增取用者群組，讓您的 IoT 中樞準備好進行資料存取。
- 設定 Web 應用程式 (web app) 來讀取 IoT 中樞的感應器資料。
- 上傳要供 Web 應用程式 (web app) 裝載的 Web 應用程式 (web application)。
- 開啟 Web 應用程式 (web app)，以查看 IoT 中樞的即時溫度和溼度資料。

## <a name="what-you-need"></a>您需要什麼

- 完成[將 ESP8266 連接到 Azure IoT 中樞](iot-hub-arduino-huzzah-esp8266-get-started.md)教學課程，其中涵蓋下列需求：
  - 有效的 Azure 訂用帳戶。
  - 位於您訂用帳戶中的 Azure IoT 中樞。
  - 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。
- Git。 ([下載 Git](https://www.git-scm.com/downloads))。

## <a name="create-an-azure-web-app"></a>建立 Azure Web 應用程式 (web app)

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，按一下 [新增] > [Web + 行動] >  [Web 應用程式]。
1. 輸入唯一的作業名稱、驗證訂用帳戶、指定資源群組和位置，選取 [釘選到儀表板]，然後按一下 [建立]。

   我們建議您選取資源群組所在的相同位置。 這有助於提升處理速度並減少資料傳輸成本。

   ![建立 Azure Web 應用程式 (web app)](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

應用程式會使用取用者群組從 Azure IoT 中樞提取資料。 在此課程中，您可以建立 Web 應用程式 (web app) 所要使用的取用者群組，以便從 IoT 中樞讀取資料。

若要將取用者群組新增至 IoT 中樞，請遵循下列步驟：

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，開啟 IoT 中樞。
1. 按一下左窗格的 [端點]，選取中間窗格的 [事件]，在右窗格的 [取用者群組] 之下輸入名稱，然後按一下 [儲存]。

   ![在 Azure IoT 中樞建立取用者群組](media/iot-hub-live-data-visualization-in-web-apps/3_add-consumer-group-iot-hub-azure.png)

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>設定 Web 應用程式 (web app) 來讀取 IoT 中樞的資料

1. 開啟您剛才佈建的 Web 應用程式 (web app)。
1. 按一下 [應用程式設定]，然後在 [應用程式設定] 底下新增下列索引鍵/值組：

   | 索引鍵                                   | 值                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | 取自 IoT 中樞總管                               |
   | Azure.IoT.IoTHub.DeviceId             | 取自 IoT 中樞總管                               |
   | Azure.IoT.IoTHub.ConsumerGroup        | 您新增至 IoT 中樞之取用者群組的名稱  |

   ![使用索引鍵值組將設定新增至 Azure Web 應用程式 (web app)](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>上傳要供 Web 應用程式 (web app) 裝載的 Web 應用程式 (web application)

我們已在 GitHub 上啟用 Web 應用程式 (web application)，以顯示 IoT 中樞的即時感應器資料。 您只需要設定 Web 應用程式 (web app) 來使用 Git 儲存機制、從 GitHub 下載 Web 應用程式 (web application)，並將它上傳至 Azure 以供 Web 應用程式 (web app) 裝載。

1. 在 Web 應用程式 (web app) 中，按一下 [部署選項] > [選擇來源] > [本機 Git 儲存機制]。

   ![設定 Azure Web 應用程式 (web app) 部署以使用本機 git 儲存機制](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. 按一下 [設定連線]，建立要用來連線到 Azure 中 Git 儲存機制的使用者名稱和密碼，然後按一下 [確定]。

   ![為 Web 應用程式 (web app) 設定 Azure 中 git 儲存機制的使用者名稱和密碼](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. 按一下 [確定] 以完成設定。
1. 按一下 [概觀] 並記下 [Git 複製 url] 的值。

   ![取得 Azure Web 應用程式 (web app) 的 git 複製 URL](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. 開啟本機電腦上的命令或終端機視窗。
1. 從 GitHub 下載 Web 應用程式 (web application)，並將它上傳到 Azure 以供 Web 應用程式 (web app) 裝載。 若要這樣做，請執行下列命令：

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > \<Git clone URL\> 是在 Web 應用程式 (web app) 的 [概觀] 頁面上找到的 Git 儲存機制 URL。

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>開啟 Web 應用程式 (web app)，以查看 IoT 中樞的即時溫度和溼度資料

在 Web 應用程式 (web app) 的 [概觀] 頁面上，按一下 URL 以開啟 Web 應用程式。

![取得 Azure Web 應用程式 (web app) 的 URL](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

您應該會看到 IoT 中樞的即時溫度和溼度資料。

![顯示即時溫度和溼度的 Azure Web 應用程式 (web app) 頁面](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>後續步驟
您已成功使用 Azure Web 應用程式 (web app) 將 Azure IoT 中樞的即時感應器資料視覺化。

有替代方法可將 Azure IoT 中樞的資料視覺化。 請參閱[使用 Power BI 將來自 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

- [透過 iothub-explorer 管理雲端裝置傳訊](iot-hub-explorer-cloud-device-messaging.md)
- [將 IoT 中樞IoT 中樞儲存至 Azure 資料儲存體](iot-hub-store-data-in-azure-table-storage.md)

