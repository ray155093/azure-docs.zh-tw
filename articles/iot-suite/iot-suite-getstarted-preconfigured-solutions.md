---
title: "開始使用預先設定解決方案 | Microsoft Docs"
description: "遵循此教學課程學習如何部署 Azure IoT Suite 預先設定解決方案。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>教學課程：開始使用預先設定的解決方案
## <a name="introduction"></a>簡介
Azure IoT 套件[預先設定的解決方案][lnk-preconfigured-solutions]結合了多項 Azure IoT 服務，以提供可實作常見 IoT 商務案例的端對端解決方案。 *遠端監視* 預先設定的解決方案會連接並監視您的裝置。 您可以使用此解決方案分析裝置的資料串流，並藉由讓處理程序自動回應該資料串流來提升業務績效。

本教學課程示範如何佈建遠端監視預先設定的解決方案。 其中也逐步說明預先設定解決方案的基本功能。 您可以從隨預先設定解決方案一起部署的解決方案儀表板中，存取其中許多功能：

![遠端監視預先設定解決方案儀表板][img-dashboard]

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

> [!NOTE]
> 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>檢視解決方案儀表板
解決方案儀表板可讓您管理部署的解決方案。 例如，您可以檢視遙測、新增裝置及設定規則。

1. 當佈建完成且預先設定解決方案的圖格指示 [就緒] 時，按一下 [啟動] 以在新的索引標籤中開啟遠端監視解決方案入口網站。
   
   ![啟動預先設定的解決方案][img-launch-solution]
2. 根據預設，此解決方案入口網站會顯示解決方案儀表板 。 您可以使用左側功能表選取其他檢視。
   
   ![遠端監視預先設定解決方案儀表板][img-dashboard]

儀表板會顯示下列資訊：

* 地圖會顯示每個裝置連接到解決方案的位置。 當您第一次執行解決方案時，有四個模擬裝置。 模擬裝置會實作為 Azure WebJobs，而解決方案會使用 Bing 地圖 API 在地圖上繪製資訊。
* [遙測歷程記錄]  面板會從所選的裝置繪製近乎即時的溼度和溫度遙測，並顯示彙總資料，例如最大、最小和平均溼度。
* [警示歷程記錄]  面板會在遙測值超過臨界值時顯示最近的警示事件。 除了預先設定的解決方案所建立的範例以外，您可以定義自己的警示。
* [作業] 面板顯示已排程工作的相關資訊。 您可以在 [管理作業] 頁面排程您自己的作業。

## <a name="view-the-device-list"></a>檢視裝置清單
[裝置清單] 顯示解決方案中所有已註冊的裝置。 從裝置清單中，您可以檢視和編輯裝置中繼資料、新增或移除裝置，並在裝置上叫用方法。

1. 按一下左側功能表中的 [裝置]，以顯示此解決方案的裝置清單。
   
   ![儀表板中的裝置清單][img-devicelist]
2. 裝置清單最初會顯示佈建程序所建立的四個模擬裝置。 您可以將其他模擬裝置和實體裝置新增至解決方案。
3. 您可以按一下 [資料行編輯器]，自訂裝置清單中顯示的資訊。 資料行顯示回報屬性和標籤值，您可以新增和移除這些資料行。 您也可以重新排列及重新命名資料行︰
   
   ![儀表板中的資料行編輯器][img-columneditor]
4. 若要檢視裝置詳細資料，請按一下裝置清單中的裝置。
   
   ![儀表板中的裝置詳細資料][img-devicedetails]

[裝置詳細資料] 面板包含六個區段：

* 一組連結，可讓您自訂裝置圖示、停用裝置、新增規則、叫用方法，或傳送命令。 如需命令 (裝置對雲端的訊息) 和方法 (直接方法) 的比較，請參閱[雲端對裝置的通訊指引][lnk-c2d-guidance]。
* [裝置對應項 - 標籤] 區段可讓您編輯裝置的標籤值。 您可以在裝置清單中顯示標籤值，並使用標籤值來篩選裝置清單。
* [裝置對應項 - 所需屬性] 區段可讓您設定要傳送至裝置的屬性值。
* [裝置對應項 - 回報屬性] 區段顯示從裝置傳來的屬性值。
* [裝置屬性] 區段顯示身分識別登錄中的資訊，例如裝置識別碼和驗證金鑰。
* [最近的作業] 區段顯示最近針對這個裝置執行之任何作業的相關資訊。

## <a name="customize-the-device-icon"></a>自訂裝置圖示

您可以從 [裝置詳細資料] 面板，自訂裝置清單中顯示的裝置圖示，如下所示︰

1. 按一下鉛筆圖示，開啟裝置的 [編輯影像] 面板︰
   
   ![開啟裝置影像編輯器][img-startimageedit]
2. 上傳新的影像或使用其中一個現有的影像，然後按一下 [儲存]：
   
   ![編輯裝置影像編輯器][img-imageedit]
3. 您選取的影像現在顯示在裝置的 [圖示]資料行。

> [!NOTE]
> 影像儲存在 Blob 儲存體中。 裝置對應項中的標籤包含 Blob 儲存體中的影像連結。
> 
> 

## <a name="invoke-a-method-on-a-device"></a>在裝置上叫用方法
從 [裝置詳細資料] 面板中，您可以在裝置上叫用方法。 裝置第一次啟動時會將其支援之方法的相關資訊，傳送至解決方案。

1. 在所選裝置的 [裝置詳細資料] 面板中，按一下 [方法]：
   
   ![儀表板中的裝置方法][img-devicemethods]
2. 在方法清單中選取 [重新啟動]。
3. 按一下 [叫用方法]。
4. 您可以在方法歷程記錄中，看到方法引動過程的狀態。
   
   ![儀表板中的方法狀態][img-pingmethod]

解決方案會追蹤其叫用之每個方法的狀態。 當裝置完成方法時，您在方法歷程記錄資料表中會看到新的項目。

有些方法會在裝置上啟動非同步作業。 例如，**InitiateFirmwareUpdate** 方法會啟動非同步作業來執行更新。 裝置會使用回報屬性，報告韌體更新進行時的狀態。

## <a name="send-a-command-to-a-device"></a>傳送命令至裝置
從 [裝置詳細資料] 面板中，您可以將命令傳送至裝置。 在裝置第一次啟動時，便會傳送其支援的命令相關資訊給解決方案。

1. 在所選裝置的 [裝置詳細資料] 面板中，按一下 [命令]：
   
   ![儀表板中的裝置命令][img-devicecommands]
2. 選取命令清單中的 [PingDevice]  。
3. 按一下 [傳送命令]。
4. 您可以在命令歷程記錄中看見命令的狀態。
   
   ![儀表板中的命令狀態][img-pingcommand]

解決方案會追蹤其傳送的每個命令的狀態。 結果最初是 **處理中**。 當裝置報告它已執行命令時，結果會設定為 **成功**。

## <a name="add-a-new-simulated-device"></a>新增模擬裝置
當您部署預先設定的解決方案時，自動會佈建您在裝置清單中可見的四個範例裝置。 這些裝置是在 Azure WebJob 中執行的 *模擬裝置* 。 模擬裝置讓您可以輕鬆對預先設定的解決方案進行實驗，而不需要部署真實的實體裝置。 若要將真實裝置連接至解決方案，請參閱[將裝置連接至遠端監視預先設定解決方案][lnk-connect-rm]教學課程。

下列步驟顯示如何將模擬裝置新增至解決方案︰

1. 瀏覽回到裝置清單。
2. 若要新增裝置，請按一下左下角的 [+ 新增裝置]。
   
   ![將裝置新增到預先設定的解決方案][img-adddevice]
3. 按一下 [模擬裝置] 圖格上的 [新增]。
   
   ![在儀表板中設定新的裝置詳細資料][img-addnew]
   
   如果您選擇建立 **自訂裝置**，則除了建立新的模擬裝置，也可以新增實體裝置。 如需將實體裝置連接至解決方案的詳細資訊，請參閱[將裝置連接至 IoT 套件遠端監視預先設定解決方案][lnk-connect-rm]。
4. 選取 [自行定義裝置識別碼]，然後輸入唯一的裝置識別碼名稱，例如 **mydevice_01**。
5. 按一下 [建立] 。
   
   ![儲存新的裝置][img-definedevice]
6. 在 [新增模擬裝置] 的步驟 3 中，按一下 [完成] 以返回裝置清單。
7. 您可以在裝置清單中檢視 [執行中]  裝置。
   
    ![檢視裝置清單中的新裝置][img-runningnew]
8. 您也可以在儀表板上檢視來自新裝置的模擬遙測︰
   
    ![從新裝置檢視遙測][img-runningnew-2]

## <a name="device-properties"></a>裝置屬性
遠端監視預先設定的解決方案會使用[裝置對應項][lnk-device-twin]，以同步處理裝置和解決方案後端之間的裝置中繼資料。 裝置對應項是儲存在 IoT 中樞的 JSON 文件，文件中儲存個別裝置的屬性值。 裝置會定期將中繼資料傳送至解決方案後端，當做「回報屬性」儲存在裝置對應項中。 解決方案後端可以在裝置對應項中設定「所需屬性」，以將中繼資料更新傳送至裝置。 回報屬性會顯示裝置傳送的最新中繼資料值。 如需詳細資訊，請參閱[裝置身分識別登錄、裝置對應項和 DocumentDB][lnk-devicemetadata]。

> [!NOTE]
> 解決方案也使用 DocumentDB 資料庫，儲存命令和方法相關的裝置特定資料。
> 
> 

1. 瀏覽回到裝置清單。
2. 在 [裝置清單] 中選取您的新裝置，然後按一下 [編輯] 來編輯 [裝置對應項 - 所需屬性]：
   
   ![編輯裝置所需屬性][img-editdevice]
3. 將 [所需屬性名稱] 設為 **Latitude**，將值設為 **47.639521**。 然後按一下 [將變更儲存至裝置登錄]：
   
    ![編輯裝置所需屬性][img-editdevice2]
4. 在 [裝置詳細資料] 面板中，新的緯度值一開始會顯示為所需屬性，舊的緯度值會顯示為回報屬性︰
   
    ![檢視回報屬性][img-editdevice3]
5. 目前，預先設定解決方案中的模擬裝置只會處理 **Desired.Config.TemperatureMeanValue** 和 **Desired.Config.TelemetryInterval** 所需屬性。 實際裝置應該從 IoT 中樞讀取所有所需屬性，變更其設定，然後將新的值當做回報屬性，回報給中樞。

在 [裝置詳細資料] 面板中，就像編輯 [裝置對應項 - 所需屬性] 一樣，您也可以編輯 [裝置對應項 - 標籤]。 但是與所需屬性不同，標籤不會與裝置同步處理。 標籤只存在於 IoT 中樞的裝置對應項中。 標籤適用於裝置清單中建置自訂篩選。

## <a name="sort-the-device-list"></a>排序裝置清單

您可以按一下資料行標題旁邊來排序裝置清單。 按第一次會依遞增順序排序，按第二次會依遞減順序排序︰

![排序裝置清單][img-sortdevices]

## <a name="filter-the-device-list"></a>篩選裝置清單

在裝置清單中，您可以建立、儲存和載入篩選，以顯示連接至中樞的自訂裝置清單。 若要建立篩選：

1. 按一下裝置清單上方的編輯篩選圖示︰
   
   ![開啟篩選編輯器][img-editfiltericon]
2. 在 [篩選編輯器] 中，新增欄位、運算子和值來篩選裝置清單。 您可以新增多個子句來精簡篩選。 按一下 [篩選] 來套用篩選︰
   
   ![建立篩選][img-filtereditor]
3. 在此範例中，清單依製造商和型號篩選︰
   
   ![篩選的清單][img-filterelist]
4. 若要以自訂名稱儲存篩選，請按一下 [另存新檔] 圖示︰
   
   ![新增篩選][img-savefilter]
5. 若要重新套用您先前儲存的篩選，請按一下 [開啟儲存的篩選] 圖示︰
   
   ![開啟篩選][img-openfilter]

您可以根據裝置識別碼、裝置狀態、所需屬性、回報屬性和標籤，以建立篩選。

> [!NOTE]
> 在 [篩選編輯器] 中，您可以使用 [進階檢視]，直接編輯查詢文字。
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>加入新裝置的規則
您剛才加入的新裝置沒有規則。 在本節中，您會新增一個規則，以便在新裝置所報告的溫度超過 47 度時觸發警示。 在開始之前，請注意儀表板上新裝置的遙測歷程記錄顯示裝置溫度絕不會超出 45 度。

1. 瀏覽回到裝置清單。
2. 若要新增裝置的規則，請在 [裝置清單] 中選取您的新裝置，然後按一下 [新增規則]。
3. 建立規則，其使用**溫度**做資料欄位，以及使用 **AlarmTemp** 做為溫度超過 47 度時的輸出︰
   
    ![新增裝置規則][img-adddevicerule]
4. 若要儲存變更，請按一下 [儲存並檢視規則]。
5. 按一下新裝置之裝置詳細資料窗格中的 [命令]  。
   
   ![新增裝置規則][img-adddevicerule2]
6. 從命令清單中選取 **ChangeSetPointTemp** 並將 **SetPointTemp** 設定為 45。 然後按一下 [傳送命令] ：
   
   ![新增裝置規則][img-adddevicerule3]
7. 瀏覽回到解決方案儀表板。 一小段時間之後，您會在新裝置報告溫度超過 47 度臨界值時，在 [警示歷程記錄]  窗格中看到新的項目︰
   
   ![新增裝置規則][img-adddevicerule4]
8. 您可以在儀表板的 **規則** 頁面上檢閱和編輯所有規則：
   
    ![列出裝置規則][img-rules]
9. 您可以在儀表板的 **動作** 頁面上檢閱和編輯為了回應規則而可採取的所有動作︰
   
    ![列出裝置動作][img-actions]

> [!NOTE]
> 您可以透過[邏輯應用程式][lnk-logic-apps]定義可傳送電子郵件或簡訊的動作，以回應規則或與特定業務系統進行整合。 如需詳細資訊，請參閱[將邏輯應用程式連接至 Azure IoT 套件遠端監視預先設定解決方案][lnk-logicapptutorial]。
> 
> 

## <a name="disable-and-remove-devices"></a>停用並移除裝置
您可以停用裝置，並且在已停用之後移除：

![停用並移除裝置][img-disable]

## <a name="run-jobs"></a>執行工作
您可以排程作業，以在您的裝置上執行大量作業。 您可以為裝置清單建立作業。 這份清單可以包含您的所有裝置，也可以是您在裝置清單中使用[篩選工具](#filter-the-device-list)所建立的篩選清單。 作業可以在清單中的每個裝置上叫用方法，也可以針對裝置清單中的每個裝置更新裝置對應項。

### <a name="create-a-job-to-invoke-a-method"></a>建立作業來叫用方法

下列步驟示範如何建立作業，以在清單中的每個裝置上叫用韌體更新方法。 只有在支援該方法的裝置上，才會叫用方法︰

1. 在裝置清單上使用篩選工具，建立要接收韌體更新的裝置清單︰
   
   ![開啟篩選編輯器][img-editfiltericon]
2. 在篩選的清單上，按一下 [作業排程器]：
   
   ![開啟作業排程器][img-clickjobscheduler]
3. 在 [排程作業] 面板上，按一下 [叫用方法]。
4. 在 [叫用方法] 頁面上，輸入要叫用之方法的詳細資料，然後按一下 [排程]：
   
   ![設定方法作業][img-invokemethodjob]

**InitiateFirmwareUpdate** 方法會以非同步方式在裝置上啟動工作，並立即返回。 然後，韌體更新程序會使用回報屬性，報告正在執行的更新程序。

### <a name="create-a-job-to-edit-the-device-twin"></a>建立作業以編輯裝置對應項

下列步驟示範如何建立作業，以在清單中的每個裝置上編輯裝置對應項：

1. 在裝置清單上使用篩選工具，建立要接收裝置對應項編輯的裝置清單︰
   
   ![開啟篩選編輯器][img-editfiltericon]
2. 在篩選的清單上，按一下 [作業排程器]：
   
   ![開啟作業排程器][img-clickjobscheduler]
3. 在 [排程作業] 面板上，按一下 [編輯裝置對應項]。
4. 在 [編輯裝置對應項] 頁面上，輸入要編輯之 [所需屬性] 和 [標籤] 的詳細資料，然後按一下 [排程]：
   
   ![設定方法作業][img-edittwinjob]

### <a name="monitor-the-job"></a>監視作業
您可以在 [管理作業] 頁面上，監視您排程之作業的狀態。 [作業詳細資料]面板會顯示所選作業的相關資訊：
   
   ![檢視作業狀態][img-jobstatus]

您也可以在 [儀表板] 上檢視作業的相關資訊：
   
   ![在儀表板上檢視作業][img-jobdashboard]


## <a name="behind-the-scenes"></a>在幕後
當您部署預先設定的解決方案時，部署程序會在您選取的 Azure 訂用帳戶中建立多個資源。 您可以在 Azure [入口網站][lnk-portal]中檢視這些資源。 部署程序會建立 **資源群組** ，其名稱是以您為預先設定的解決方案選擇的名稱為基礎︰

![Azure 入口網站中的預先設定解決方案][img-portal]

選取資源群組中的資源清單中的資源，即可檢視該資源的設定。

您也可以檢視預先設定的解決方案的原始程式碼。 遠端監視預先設定的解決方案原始程式碼位於 [azure-iot-remote-monitoring][lnk-rmgithub] GitHub 儲存機制：

* **DeviceAdministration** 資料夾包含儀表板的原始程式碼。
* **Simulator** 資料夾包含模擬裝置的原始程式碼。
* **EventProcessor** 資料夾包含後端程序的原始程式碼，可用於處理內送的遙測。

完成之後，您可以從 [azureiotsuite.com][lnk-azureiotsuite] 網站上的 Azure 訂用帳戶中刪除預先設定解決方案。 這個網站可讓您輕鬆刪除在建立預先設定解決方案時已佈建的所有資源。

> [!NOTE]
> 若要確保您刪除與預先設定解決方案相關的所有項目，請在 [azureiotsuite.com][lnk-azureiotsuite] 網站上進行刪除，而不是刪除入口網站中的資源群組。
> 
> 

## <a name="next-steps"></a>後續步驟
您現已部署運作中預先設定的解決方案，您可以繼續閱讀下列文章，了解如何開始使用 IoT 套件︰

* [遠端監視預先設定解決方案逐步解說][lnk-rm-walkthrough]
* [將裝置連接至遠端監視預先設定方案][lnk-connect-rm]
* [azureiotsuite.com 網站的權限][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
