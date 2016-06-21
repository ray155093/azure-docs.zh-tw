<properties
	pageTitle="開始使用預先設定解決方案 | Microsoft Azure"
	description="遵循此教學課程學習如何部署 Azure IoT Suite 預先設定解決方案。"
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/25/2016"
     ms.author="dobett"/>

# 教學課程：開始使用預先設定的解決方案

## 簡介

Azure IoT Suite [預先設定的解決方案][lnk-preconfigured-solutions]結合了多項 Azure IoT 服務，以提供可實作常見 IoT 商務案例的端對端解決方案。

本教學課程示範如何佈建遠端監視預先設定的解決方案。其中也逐步說遠端監視預先設定的解決方案的基本功能。

若要完成本教學課程，您需要作用中的 Azure 訂用帳戶。

> [AZURE.NOTE]  如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## 檢視遠端監視解決方案儀表板

解決方案儀表板可讓您管理部署的解決方案。例如，您可以檢視遙測、新增裝置及設定規則。

1.  當佈建完成且預先設定解決方案的磚指示 [就緒] 時，按一下 [啟動] 以在新的索引標籤中開啟遠端監視解決方案入口網站。

    ![][img-launch-solution]

2.  根據預設，此解決方案入口網站會顯示解決方案儀表板。您可以使用左側功能表選取其他檢視。

    ![][img-dashboard]

儀表板會顯示下列資訊：

- 地圖會顯示每個裝置連接到解決方案的位置。當您第一次執行解決方案時，有四個模擬裝置。模擬裝置會實作為 Azure WebJobs，而解決方案會使用 Bing 地圖 API 在地圖上繪製資訊。
- [遙測歷程記錄] 面板會從所選的裝置繪製近乎即時的溼度和溫度遙測，並顯示彙總資料，例如最大、最小和平均溼度。
- [警示歷程記錄] 面板會在遙測值超過臨界值時顯示最近的警示事件。除了預先設定的解決方案所建立的範例以外，您可以定義自己的警示。

## 檢視解決方案裝置清單

裝置清單會顯示解決方案中所有已註冊的裝置。您可以檢視及編輯裝置中繼資料、新增或移除裝置，或傳送命令給裝置。

1.  按一下左側功能表中的 [裝置]，以顯示此解決方案的裝置清單。

    ![][img-devicelist]

2.  此裝置清單顯示佈建程序所建立的四個模擬裝置。

3.  按一下裝置清單中的裝置，以檢視裝置詳細資料。

    ![][img-devicedetails]

**裝置詳細資料**面板包含三個區段：

- **動作**區段會列出您可以在裝置執行的動作。如果您停用裝置，精緻不再能夠傳送遙測或接收命令。如果您停用裝置，您可以接著再次啟用它。您可以新增與裝置相關聯的規則，以便在遙測值超出臨界值時觸發警示。您也可以傳送命令至裝置。當裝置第一次連接時，它告訴解決方案可以回應的命令。
- **裝置屬性**區段會列出裝置中繼資料。此中繼資料某些來自裝置本身 (例如製造商)，而有些是由解決方案所產生 (例如建立時間)。您可以從這裡編輯裝置中繼資料。
- **驗證金鑰**區段會列出裝置可用來向解決方案進行驗證的金鑰清單。

## 傳送命令至裝置

裝置詳細資料窗格會顯示特定裝置支援的所有命令，並且可讓您傳送命令至裝置。在裝置第一次啟動時，便會傳送其支援的命令相關資訊給解決方案。

1.  按一下所選裝置之裝置詳細資料窗格中的 [命令]。

    ![][img-devicecommands]

2.  選取命令清單中的 [PingDevice]。

3.  按一下 [傳送命令]。

4.  您可以在命令歷程記錄中看見命令的狀態。

    ![][img-pingcommand]

解決方案會追蹤其傳送的每個命令的狀態。結果最初是**處理中**。當裝置報告它已執行命令時，結果會設定為**成功**。

## 新增模擬裝置

1.  瀏覽回到裝置清單。

2.  按一下左下角的 [+ 新增裝置] 來新增裝置。

    ![][img-adddevice]

3.  按一下 [模擬裝置] 磚上的 [新增]。

    ![][img-addnew]
    
    如果您選擇建立**自訂裝置**，則除了建立新的模擬裝置，也可以新增實體裝置。若要深入了解，請參閱[將裝置連接至 IoT 套件遠端監視預先設定方解決案][lnk-connecting-devices]。

4.  選取 [自行定義裝置識別碼]，然後輸入唯一的裝置識別碼名稱，例如 **mydevice\_01**。

5.  按一下 [建立]。

    ![][img-definedevice]

6. 在 [新增模擬裝置] 的步驟 3 中，按一下 [完成] 以返回裝置清單。

7. 您可以在裝置清單中檢視 [執行中] 裝置。

    ![][img-runningnew]

8. 您也可以在儀表板上檢視來自新裝置的模擬遙測︰

    ![][img-runningnew-2]

## 編輯裝置中繼資料

1.  瀏覽回到裝置清單。

2.  在 [裝置清單] 中選取您的新裝置，然後按一下 [編輯] 來編輯 [裝置屬性]：

    ![][img-editdevice]

3. 向下捲動並對緯度和經度值進行變更。然後按一下 [將變更儲存至裝置登錄]。

    ![][img-editdevice2]

4. 瀏覽回到儀表板，地圖上的裝置位置已變更︰

    ![][img-editdevice3]

## 加入新裝置的規則

您剛才加入的新裝置沒有規則。在本節中，您將新增一個規則，以便在新裝置所報告的溫度超過 47 度時觸發警示。在開始之前，請注意儀表板上新裝置的遙測歷程記錄顯示裝置溫度絕不會超出 45 度。

1.  瀏覽回到裝置清單。

2.  在 [裝置清單] 中選取您的新裝置，然後按一下 [新增規則] 以加入裝置的新規則。

3. 建立規則，其使用**溫度**做資料欄位，以及使用 **AlarmTemp** 做為溫度超過 47 度時的輸出︰

    ![][img-adddevicerule]

4. 按一下 [儲存及檢視規則] 以儲存變更。

5.  按一下新裝置之裝置詳細資料窗格中的 [命令]。

    ![][img-adddevicerule2]

6.  從命令清單中選取 **ChangeSetPointTemp** 並將 **SetPointTemp** 設定為 45。然後按一下 [傳送命令]：

    ![][img-adddevicerule3]

7.  瀏覽回到解決方案儀表板。一小段時間之後，您會在新裝置所報告的溫度超過 47 度臨界值時，在 [警示歷程記錄] 窗格中看到新的項目︰

    ![][img-adddevicerule4]

8. 您可以在儀表板的**規則**頁面上檢閱和編輯所有規則：

    ![][img-rules]

9. 您可以在儀表板的**動作**頁面上檢閱和編輯為了回應規則而可採取的所有動作︰

    ![][img-actions]

> [AZURE.NOTE] 您可以透過[邏輯應用程式][lnk-logic-apps]定義可傳送電子郵件或簡訊的動作，以回應規則或與特定業務系統進行整合。

## 在幕後

當您部署預先設定的解決方案時，部署程序會在您選取的 Azure 訂用帳戶中建立多個資源。您可以在 Azure [入口網站][lnk-portal]中檢視這些資源。部署程序會建立**資源群組**，其名稱是以您為預先設定的解決方案選擇的名稱為基礎︰

![][img-portal]

選取資源群組中的資源清單中的資源，即可檢視該資源的設定。上方螢幕擷取畫面顯示預先設定的解決方案中所用的 IoT 中樞設定。

您也可以檢視預先設定的解決方案的原始程式碼。遠端監視預先設定的解決方案原始程式碼位於 [azure-iot-remote-monitoring][lnk-rmgithub]：

- **DeviceAdministration** 資料夾包含儀表板的原始程式碼。
- **Simulator** 資料夾包含模擬裝置的原始程式碼。
- **EventProcessor** 資料夾包含後端程序的原始程式碼，可用於處理內送的遙測。

完成時，您可以在 [azureiotsuite.com][lnk-azureiotsuite] 網站上刪除您的 Azure 訂用帳戶中預先設定的解決方案 - 這可讓您輕鬆地刪除在建立預先設定的解決方案時佈建的所有資源。

> [AZURE.NOTE] 若要確保您刪除與預先設定的解決方案相關的所有項目，請從 [azureiotsuite.com][lnk-azureiotsuite] 進行刪除，而不只是刪除入口網站中的資源群組。

## 後續步驟

現在您已經建置可運作的預先設定解決方案，您可以繼續進行以下逐步解說：

-   [自訂預先設定解決方案指南][lnk-customize]
-   [預先設定的預防性維護解決方案概觀][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
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

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: iot-suite-faq.md

<!---HONumber=AcomDC_0615_2016-->