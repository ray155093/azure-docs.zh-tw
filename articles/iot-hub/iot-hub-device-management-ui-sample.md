---
title: 使用 IoT 中樞裝置管理 UI | Microsoft Docs
description: 使用 Azure IoT 中樞裝置管理 UI 的逐步解說
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: dobett

---
# 使用範例 UI 探索 Azure IoT 中樞裝置管理
與範例裝置管理 UI 互動，有助於您鞏固 Azure IoT 中樞裝置管理[概觀][lnk-dm-overview]與[開始使用][lnk-get-started]文章中所含的概念與功能。本文章將逐步引導您了解三個主要裝置管理概念：裝置對應項、裝置查詢和裝置工作，如範例裝置管理 Web UI 中所示。

想要建置自己專屬裝置管理互動式體驗的開發人員，可以將範例 UI 程式碼基底分岔，做為自訂專案的基礎。您可以在 [Azure IoT 裝置管理 UI][lnk-dm-github] GitHub 儲存機制檢閱完整的專案程式碼，以及詳細說明其他開發者功能的讀我檔案文件。

## 必要條件
開始本教學課程之前，您應該先完成[開始使用 Azure IoT 中樞裝置管理][lnk-get-started]文章中的步驟。若您尚未完成，請返回並完成此文章中的所有步驟，然後再繼續進行。

當您完成「開始使用」教學課程後，您的測試系統內應該會有下列執行中的項目：

* 六個在主控台/終端機視窗中執行的 **iotdm\_simple\_sample** 模擬裝置，每個都有成功的 "REGISTERED" 訊息。
* 於本機 (<http://127.0.0.1:3003>) 建置且執行中的裝置管理範例 UI 應用程式。

## 預設裝置檢視
裝置管理範例 UI 的預設主畫面是 [裝置] 檢視，其中包含下列五個元件：

![][1]

1. 瀏覽按鈕：[裝置] 檢視 (已選取)、[工作歷程記錄] 檢視，和 [新增裝置] 檢視。
2. 裝置搜尋︰依裝置識別碼尋找和編輯單一裝置。
3. 裝置動作：[編輯] 動作、[刪除] 動作，和 [匯出] 動作。
4. 裝置作業：將裝置 [重新開機]、[韌體更新]，和 [恢復出廠預設值]。
5. 裝置方格篩選器︰篩選條件編輯器，用來建置和儲存自訂裝置方格檢視。
6. 裝置方格：檢視所有向您的 IoT 中樞執行個體註冊的裝置與預設屬性 ([裝置識別碼]、[狀態] 和 [標籤])。

[裝置管理概觀][lnk-dm-overview]介紹「裝置對應項」概念，它代表 Azure IoT 中樞上的實體 (或模擬) 裝置。在裝置方格中，您可以從裝置清單選取任何已註冊的裝置，來檢視和編輯該裝置的裝置對應項。

若要在您的第一個模擬裝置 (**Device11 7ce4a850**) 上輸入此詳細檢視，請選取對應的裝置資料列。接著按一下 [編輯] 按鈕 (您也可以按兩下資料列，或在 [搜尋] 方塊中輸入裝置識別碼)。

您現在正在檢視完整的裝置對應項元件表示，您可以在其中更新可寫入的屬性和執行其他裝置作業，如下：

![][2]

1. **編輯裝置對應項**︰這個選項包括裝置的 [啟用/停用] 切換。
2. **服務屬性**︰這個選項包括裝置**標籤**。
3. **裝置屬性**︰按一下以展開此區段。
4. [**重新整理**] 按鈕︰擷取最新的裝置對應項屬性和值。

若要繼續，按一下此檢視右下角的 [取消] 以回到預設裝置清單頁面。

## 使用裝置查詢以篩選裝置檢視
裝置查詢是快速尋找具有特定屬性 (例如特定標籤) 單一裝置或一組裝置的強力方式。範例 UI 使用裝置查詢，在預設裝置清單頁面填入裝置清單。範例 UI 可讓您在資料表中新增和移除服務屬性，並篩選一些這類屬性。

執行下列步驟，建立 "bacon" 服務屬性標籤的自訂篩選：

1. 按一下漏斗圖示以開啟裝置查詢編輯檢視：
   
   ![][3]
2. 按一下 [+ 新增篩選] 以展開編輯器。從屬性下拉中選取 [標籤]，並且在文字欄位中輸入 **bacon**，然後按一下 [套用]。裝置清單現在僅會顯示三個具有 "bacon" 標籤的裝置：
   
   ![][4]
3. 在查詢標題欄位 (漏斗圖示旁邊)，將查詢命名為 **Only Bacon**，然後按一下 [儲存]：
   
   ![][5]
4. 按一下漏斗圖示離開裝置查詢編輯器。

## 使用裝置工作以模擬裝置重新開機
當您了解裝置管理概觀後，裝置工作可讓您在一或多個實體裝置上協調簡單或複雜的動作。在本節中，您會在範例 UI 中建立裝置工作，在所有具有 "bacon" 標籤的模擬裝置上執行重新開機作業：

1. 從 [Only Bacon] 裝置查詢清單中，在每個裝置資料行上按一下，將它標記為要進行重新開機工作作業：
   
   ![][6]
2. 按一下裝置工作工具列中的 [重新啟動] 按鈕以建立重新開機工作。按一下 [是] 確認之後，在產生的 [裝置的工作已開始] 對話方塊中按一下 [工作歷程記錄] 超連結以瀏覽至 [裝置工作] 檢視。
   
   ![][7]

您現在已經建立了單一父工作，它會產生三個子工作，每個子工作會分別在三個 "bacon" 標籤裝置的其中一個上執行重新開機作業：

![][8]

在經過一段時間之後重新整理此畫面，以查看父工作和三個子工作的狀態是否已變更為**已完成**。新的狀態值會顯示重新開機作業已成功且已由模擬裝置確認。使用 [裝置識別碼] 資料行判斷哪項工作與哪個裝置關聯。

> [!NOTE]
> 如果子工作傳回狀態「失敗」，請檢查您的模擬裝置是否仍在測試系統中執行。如果沒有，請再次執行 simulate.bat 或 simulate.sh 指令碼，並重複前一節中的重新開機裝置工作步驟。
> 
> 

## 後續步驟
您現在已透過使用範例裝置管理 UI 體驗，完成裝置管理概念的引導式探索。如果您想要更進一步了解裝置管理 API 並實驗一些程式碼範例，請瀏覽下列開發人員教學課程和資源：

* [如何使用裝置對應項][lnk-tutorial-twin]
* [如何使用查詢找出裝置對應項][lnk-tutorial-queries]
* [如何使用裝置作業更新裝置韌體][lnk-tutorial-jobs]
* [啟用 IoT 閘道的受管理裝置][lnk-dm-gateway]
* [Azure IoT 中樞裝置管理用戶端程式庫簡介][lnk-library-c]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [設計您的解決方案][lnk-design]
* [開發人員指南][lnk-devguide]
* [使用閘道 SDK 模擬裝置][lnk-gateway]
* [使用 Azure 入口網站管理 IoT 中樞][lnk-portal]
* [徹底保護您的 IoT 解決方案][lnk-securing]

[1]: media/iot-hub-device-management-ui-sample/image1.png
[2]: media/iot-hub-device-management-ui-sample/image2.png
[3]: media/iot-hub-device-management-ui-sample/image3.png
[4]: media/iot-hub-device-management-ui-sample/image4.png
[5]: media/iot-hub-device-management-ui-sample/image5.png
[6]: media/iot-hub-device-management-ui-sample/image6.png
[7]: media/iot-hub-device-management-ui-sample/image7.png
[8]: media/iot-hub-device-management-ui-sample/image8.png

[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management/

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0831_2016-->