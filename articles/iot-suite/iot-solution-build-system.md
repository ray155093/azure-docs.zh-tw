---
title: "MyDriving Azure IoT 範例 - 建置 | Microsoft Docs"
description: "建置應用程式，以完整示範如何使用 Microsoft Azure 建立 IoT 系統的架構，包括串流分析、機器學習服務和事件中樞。"
services: 
documentationcenter: .net
suite: 
author: harikmenon
manager: douge
ms.assetid: c2fcd6ee-3bbe-43d1-a066-dce52cc3a53d
ms.service: multiple
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: dotnet
ms.topic: article
ms.date: 06/30/2017
ms.author: harikm
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: c4b19cc76ca11f606ca8af6b0f3277b5aa46ac5a
ms.contentlocale: zh-tw
ms.lasthandoff: 07/08/2017


---
# <a name="build-and-deploy-the-mydriving-solution-to-your-environment"></a>建置 MyDriving 方案並部署至您的環境
MyDriving 是物聯網 (IoT) 方案，它會收集您的行車資料、使用機器學習服務處理資料，再將資料呈現在您的行動電話上。 此後端是由 Microsoft Azure 提供的各種服務所組成。 用戶端可以是 Android、iOS 或 Windows 10 手機。

我們建立 MyDriving 方案的目的，是為了協助您快速開始建立自己的 IoT 系統。 從 [GitHub 上的 MyDriving 儲存機制](https://github.com/Azure-Samples/MyDriving)，您可以取得 Azure Resource Manager 指令碼，將後端架構部署到您自己的 Azure 帳戶。 此後，您可以重新設定不同的服務，修改查詢以符合您自己的資料等等。 您可以在 MyDriving 儲存機制中找到這些指令碼，以及適用於行動應用程式、Azure App Service API 專案等的程式碼。

如果您尚未試用過此應用程式，請參閱 [快速入門指南](iot-solution-get-started.md)。

[MyDriving 參考指南](http://aka.ms/mydrivingdocs)詳細說明此架構。 簡而言之，我們會設定幾個組件來建立類似的專案：

* **用戶端應用程式** 會在 Android、iOS 和 Windows 10 手機上執行。 我們使用 Xamarin 平台以共用儲存在 GitHub 上 `src/MobileApp`下的大部分程式碼。 此應用程式實際上會執行兩個不同的功能︰
  * 從車載診斷系統 (OBD) 裝置及其本身的位置服務轉送遙測至系統的雲端後端。
  * 這是可讓使用者查詢其行車記錄的使用者介面。
* **雲端服務** 可即時內嵌並處理車程資料。 建立這項服務的主要工作包括選擇、參數化及連接到各種 Azure 服務。 其中一部分需要指令碼來篩選及處理內送資料。 我們使用 Azure Resource Manager 範本來設定所有組件。
* **行動服務應用程式** 是裝置應用程式之使用者介面組件背後的 Web 服務。 其主要作業是查詢儲存在資料庫中處理過的資料。 其程式碼位於 GitHub 上的 `src/MobileAppService`下。
* **Visual Studio 和 Xamarin** 是我們的開發環境。 Xamarin 既是 Visual Studio 的元件，也是獨立整合式開發環境 (IDE)，可用來建置跨平台裝置程式碼。 若要建置 iOS 程式碼，必須在 OSX 電腦上執行 Xamarin 的執行個體。 必要時，也可以當做從 Visual Studio 管理的代理程式來執行。
* **單元測試** 是在 Xamarin Test Cloud 中執行。
* **GitHub** 是我們儲存所有程式碼、指令碼和範本的儲存機制。
* **Visual Studio Team Services** 是可用來管理 Web 服務和裝置應用程式之連續建置和測試的雲端服務。
* **HockeyApp** 可用來散發各種裝置程式碼版本。 還會收集當機和使用狀況報告以及使用者意見反應。
* **Visual Studio Application Insights** 可監視 Web 服務。

以下示範如何設定上述所有項目。 

> [!NOTE] 
> 您可以省略下列許多步驟。
>
>

## <a name="sign-up-for-accounts"></a>註冊帳戶
* [Visual Studio Dev Essentials](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx)。 這個免費程式可讓您輕鬆地存取許多開發人員工具和服務，包括 Visual Studio、Visual Studio Team Services 和 Azure。 它會提供給您 12 個月每月美金 $25 元的 Azure 點數。 它也包括 Pluralsight 訓練和 Xamarin University 的訂用帳戶。 您也可以另外註冊 [Azure](https://azure.com) 和 [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx) 的免費層，但這不會提供 Azure 點數。
* [HockeyApp](https://rink.hockeyapp.net/) (選擇性)，用於管理行動應用程式的測試散發及收集遙測。
* [Xamarin](https://xamarin.com/) (必要項)，用於在 [Xamarin Test Cloud](https://xamarin.com/test-cloud) 上建置行動應用程式，以及執行偵錯回合與測試。
* [GitHub](https://github.com/Azure-Samples/MyDriving/) (選擇性)，可為您自己的程式碼建立免費的公用儲存機制 (私用儲存機制需要付費)。 此外，您可以在 Visual Studio Team Services 中，針對私用儲存機制使用基本方案。
* [Power BI](https://powerbi.microsoft.com/) (選擇性)，可在整個系統內建立豐富的資料視覺效果。

> [!NOTE]
> 您不需要 GitHub 帳戶，即可存取 [GitHub MyDriving 儲存機制](https://github.com/Azure-Samples/MyDriving)中的 MyDriving 程式碼。
> 
> 

## <a name="install-development-tools"></a>安裝開發工具
以下設定適用於開發完整解決方案︰iOS、Android 和 Windows 10 Mobile 跨平台應用程式。

如果您不在 Azure 後端執行，也可以在 Mac 或 Windows 上使用 Xamarin Studio 開發行動應用程式。

有 [此設定的詳細說明](https://msdn.microsoft.com/library/mt613162.aspx)。

### <a name="windows-development-machine"></a>Windows 開發電腦
Windows 上的中心工具是 Visual Studio，可與 Android 和 Windows 版 MyDriving 應用程式、App Service API 專案及微服務擴充功能搭配使用。

Xamarin、Git、模擬器及其他實用元件已全部與 Visual Studio 整合。

安裝：

* [Visual Studio 和 Xamarin](https://www.visualstudio.com/products/visual-studio-community-vs) (任何版本 - Community 版免費)。
* [適用於通用 Windows 平台的 SQLite](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)。 必須具備才能建置 Windows 10 Mobile 程式碼。
* [Azure SDK for Visual Studio](https://www.visualstudio.com/vs/azure-tools/)。 提供您可在 Azure 中執行應用程式的 SDK，以及用於管理 Azure 的命令列工具。
* [Azure Service Fabric SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric)。 必須具備才能建置 [微服務](../service-fabric/service-fabric-get-started.md) 擴充功能。

請確定您有適當的 Visual Studio 延伸模組。 確認在 [工具] 下會看到 **Android、iOS、Xamarin…**。 如果看不到，請開啟 Visual Studio，搜尋 Xamarin，然後遵循提示加以安裝。 此外，請檢查是否已安裝 **Git for Windows**。 如果未安裝，請在 Visual Studio 中進行搜尋，然後遵循提示加以安裝。 

### <a name="mac-development-machine"></a>Mac 開發電腦
如果您要開發 iOS，則需要 Mac (Yosemite 或更新版本)。 雖然我們在 Windows 上使用 Visual Studio 和 Xamarin 來開發及管理所有程式碼，但是 Xamarin 會使用 Mac 上安裝的代理程式，以便建置及簽署 iOS 程式碼。

![在 Windows 上開發及在 Mac 上建置](./media/iot-solution-build-system/image1.png)

(您也可以在 Mac 上，直接使用 Xamarin Studio 來開發跨平台應用程式。)

如果您不想要包含 iOS 作為目標平台，則不需要 Mac。

安裝：

* [適用於 iOS 的 Xamarin Studio](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/)。 您也可以在執行 Windows 虛擬機器的 Mac 上，設定 Visual Studio 和 Xamarin。 請參閱 MSDN 上的 [針對 Mac 使用者的設定、安裝和驗證](https://msdn.microsoft.com/library/mt488770.aspx) 。
* [Azure 開發工具](https://azure.microsoft.com/downloads/) (選擇性)。

啟用 Mac 上的遠端登入。 開啟 [系統偏好設定] > [共用]，然後選取 [遠端登入]。

當您在 Windows 上使用 Visual Studio 開啟 iOS 專案時，Xamarin 外掛程式會提示您輸入 Mac 的識別碼。

## <a name="fetch-the-github-repository"></a>擷取 GitHub 儲存機制
使用 GitHub、Visual Studio 或其他 Git 用戶端中的 **[下載 ZIP]** 按鈕，[擷取 GitHub MyDriving 儲存機制](https://github.com/Azure-Samples/MyDriving) 的本機複本。

將檔案解壓縮至具有簡短路徑名稱的資料夾，例如 C:\\code。

或者，如果您要保持最新狀態或貢獻程式碼，請如下所示複製儲存機制︰

**git clone https://github.com/Azure-Samples/MyDriving.git**

## <a name="get-a-bing-maps-api-key"></a>取得 Bing 地圖 API 金鑰
[註冊 Bing 地圖 API 金鑰](https://msdn.microsoft.com/library/ff428642.aspx)。

您必須在 `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`中的第 22 行處加以更換。

## <a name="build-the-demo-app"></a>建置示範應用程式
在 Visual Studio 中開啟這些方案：

* src\MobileApps\MyDriving.sln
* src\MobileAppService\MyDrivingService.sln
* src\Extensions\ServiceFabric\VINLookUpApplication\VINLookUpApplication.sln

您會收到提示，要求您︰

* 信任一些潛在不受信任的專案。 如果您要繼續進行，請選擇加以開啟。
* 如果您在全新的 Windows 10 電腦上工作，請設定開發人員模式。
* 提供您的 Xamarin 認證。
* 連接到 Xamarin Mac。 如果您沒有 Mac，請以滑鼠右鍵按一下 Visual Studio 中的 iOS 專案，然後選取 [卸載專案] 。

重建方案。

如果無法順利建置，請對我們找到的突發事件試用解決方案︰

* *VINLookupApplication 專案未載入*：請確定已安裝 [Azure SDK for Visual Studio](https://www.visualstudio.com/vs/azure-tools/)。
* *Service Fabric 專案未建置*︰請先建置介面專案，再確定已安裝 Service Fabric SDK。
* Android 應用程式未建置︰
  
  * 開啟 [工具] > [Android] > [Android SDK Manager]，然後確定已安裝 Android 6 (API 23)/SDK 平台。
  * 刪除此目錄，然後重新建置︰<br/>
    `%LocalAppData%\Xamarin\zips`

## <a name="get-to-know-the-code"></a>了解程式碼
在方案中，您會找到︰

* Azure 擴充功能：Service Fabric。
* Azure HDInsight：用於處理 Azure 中行程資料的指令碼。
* Mobile Apps：裝置應用程式。
* MobileAppsService/MyDrivingService：Web 後端。
* Power BI：Power BI 報表定義。
* Scripts：
  
  * Resource Manager：用以建置 Azure 資源的範本
  * PowerShell：用以執行 ARM 範本的指令碼。
  * Azure SQL Database：資料庫偵錯。
* SQL Database：CreateTables：結構描述定義。
* Azure 串流分析：轉換傳入資料流的查詢。

## <a name="run-the-apps-in-development-mode"></a>在開發模式中執行應用程式
根據您所使用的裝置，採取動作以執行應用程式︰

* 後端：將 MyDrivingService 設定為啟始專案，然後按 F5 鍵執行後端 Web 服務。 這會開啟 API 清單的瀏覽器檢視。
* 行動用戶端︰ [行動應用程式是以 Xamarin 開發](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/)。
  
  * Android：如需詳細資訊，請參閱 [以 Xamarin 對 Android 進行偵錯](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/)。
  * iOS：如需詳細資訊，請參閱 [在 iOS 中偵錯](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/)。
  * Windows Phone：如需詳細資訊，請參閱 [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/)。

## <a name="upload-the-mobile-app-to-hockeyapp"></a>將行動應用程式上傳至 HockeyApp
HockeyApp 可管理 Android、iOS 或 Windows 應用程式散發以測試使用者，並在有新版本時通知使用者。 它也會收集實用的當機報告、含螢幕擷取畫面的使用者意見反應及使用計量。

[開始上傳](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) 您的組建應用程式。 然後從您的開發電腦登入 [HockeyApp](https://rink.hockeyapp.net)。 在開發人員儀表板上，按一下 [新增應用程式] ，然後將建置的檔案拖曳至視窗。 (之後，您可以將組建服務自動化來執行這項作業)。

現在，移至您的應用程式儀表板。

![應用程式儀表板上的概觀索引標籤](./media/iot-solution-build-system/image2.png)

針對執行應用程式的每個平台重複此程序。 然後，您可以執行下列工作：

* 使用儀表板中的 [應用程式識別碼](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) ，從您的應用程式傳送當機資料和意見反應。 在 MyDriving 中，更新 src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs 中的識別碼。
* [邀請測試使用者](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers)。 取得 URL，以招募測試人員使用者。 這些使用者將能夠註冊加入您的小組、下載應用程式並傳送意見反應給您。
* 如果您偏好更開放的 Beta 版，請將散發設定為公用。 按一下 [管理應用程式] > [散發] > [下載 = 公用]。 現在，所有人都可以下載您的應用程式並傳送意見反應給您，而且當您公佈新版本時，他們將會看到通知。 您可能也會從這些使用者收到一些當機報告。
  
   ![儀表板上的小組](./media/iot-solution-build-system/image3.png)
* [將當機報告連結至 Visual Studio Team Services](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs)。 按一下 [管理應用程式] > [Visual Studio Team Services]。 HockeyApp 可在有當機報告或收到意見反應時，自動在 Team Services 中建立工作項目。

如需詳細資訊，請參閱 [HockeyApp 網站](https://hockeyapp.net)。

## <a name="test-the-mobile-app-on-xamarin-test-cloud"></a>在 Xamarin Test Cloud 上測試行動應用程式
[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) 可將雲端中實際裝置上的 UI 測試自動化。 使用 NUnit 架構，您可以撰寫透過使用者介面執行應用程式的測試。

若要使用 Xamarin，請將以 NuGet 封裝形式提供的 [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) SDK 併入您的應用程式。 您可以在示範應用程式中找到此 SDK，當您使用 Xamarin 範本建立新的測試專案時，也會包含此 SDK。

![介面上哪裡可以找到跨平台 SDK](./media/iot-solution-build-system/image4.png)

儲存機制中的應用程式隨附測試專案範例。 請在 [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService) 中的 [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/ 下尋找。

如果您使用 Visual Studio Team Services 組建，則可以輕鬆地撰寫 Xamarin UI 單元測試，並將其當做組建的一部分來執行。

## <a name="deploy-azure-services"></a>部署 Azure 服務
若要執行 Azure 服務和 Team Services 組建服務的自動部署，請參閱 **scripts/README.md**中的詳細指示。

Microsoft Azure 提供許多不同的服務，可用來建置雲端應用程式。 雖然您可以個別使用許多服務 (例如 App Service/Web Apps)，但這些服務與整合式系統 (例如在 MyDriving 中使用的整合式系統) 互連時的效果最佳。

您可以手動建立 Azure 服務並與其互連，但使用 Azure Resource Manager 範本的速度和可靠性均較高。 [Resource Manager](../azure-resource-manager/resource-group-overview.md) 可自動化方案的資源部署，並互相連接這些資源。

您可以在 GitHub 儲存機制中的 [scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM)下，找到 MyDriving 系統的範本。 此範本可讓您詳盡且精簡地檢視架構中不同服務的互連方式。 我們在 [MyDriving 參考指南](http://aka.ms/mydrivingdocs)中將詳細說明，不過光是閱讀範本本身，就能了解許多資訊。

> [!NOTE]
> 大多數 Azure 服務都有取決於定價層的相關聯成本。 如果您不熟悉 Azure，您可以 [免費試用](https://azure.microsoft.com/free/)。 不過，如果您不打算使用 MyDriving 系統中的特定元件，請務必移除以避免產生成本。 本文後面的「估計作業成本」一節提供一般服務費用的摘要。
> 
> 

### <a name="edit-the-template"></a>編輯範本
若要自訂您的部署，或許是為了移除不必要的元件或新增其他元件，請先複製 scenario\_complete.params.json 和 scenario\_complete.json 以進行變更。

您可以使用 scenario\_complete.params.json 檔案來覆寫各種預設值，例如下表中所描述的服務 SKU 或儲存體複寫類型。 預設值會選取最低成本選項。

| **參數** | **說明** | **預設值** |
| --- | --- | --- |
| IoT 中樞 SKU |Azure IoT 中樞服務層 |F1 |
| 儲存體帳戶類型 |儲存體複寫類型 |標準 LRS |
| SQL 服務目標 |並行存取插槽耗用量 |DW100 |
| 主控方案 SKU |App Service 的服務方案 |F1 |

在 scenario\_complete.json 中：

* 搜尋 "baseName" 並變更為您所偏好的名稱。
* 搜尋 "Create"。 這些區段會各自建立一個資源。
* 將 sqlServerAdminLogin 和 sqlServerAdminPassword 設定為適當的值。
* 刪除建立資源的區段之前，請搜尋檔案中的其他位置是否有其名稱，以檢查是否有相依項。 請注意，建立服務的每個區段都會包含列出其相依性的 *dependsOn* 區段。

以下是這個範本所設定的內容。 [參考指南](http://aka.ms/mydrivingdocs)中有詳細資料。

| **服務** | **說明和詳細資料** |
| --- | --- |
| 儲存體帳戶 |此範本會建立三個帳戶︰ |
| -   從串流分析接收彙總遙測的 SQL Database，並可作為 Azure App Service 資料表的備份存放區，以透過 API 端點公開此資料。 | |
| -   從另一個串流分析作業累積歷程記錄資料，以供 HDInsight 處理的 Blob 儲存體。 | |
| -   接收經過 HDInsight 處理的結果以搭配 Power BI 使用的 SQL Database。 | |
| Azure IoT 中心 |建立每個已連接裝置的雙向連接。 在 MyDriving 方案中，行動應用程式會作為欄位閘道器，以傳送資料至 Azure IoT 中樞。 Azure IoT 中樞接著可作為串流分析的輸入。 |
| Azure 事件中心 |串流分析作業的輸出，可將以 Azure Service Fabric 建立的擴充功能輸出排入佇列。 |
| Azure SQL 資料倉儲 | |
| 串流分析作業 |將輸入和輸出連接到查詢，以用來彙總 App Service API、Azure Machine Learning、擴充功能和 Power BI 的即時和歷程記錄資料。 |
| 機器學習服務工作區 |包括實驗、R 程式碼和 API 服務。 |
| Azure Data Factory |排程的機器學習服務重新訓練。 |
| Service Fabric 主控方案 |適用於擴充功能。 |
| App Service (「行動應用程式」) |裝載行動應用程式 API 專案，以提供行動應用程式的端點。 API 程式碼必須從 Visual Studio 部署至 App Service。 |
| 警示規則 |如果應用程式回應表示失敗，則傳送電子郵件給您。 |
| Application Insights |用於監視 App Service 中的 API 效能。 您必須設定 Visual Studio 中的連接。 |
| Azure 金鑰保存庫 |用於儲存 Web 服務叢集憑證。 |

### <a name="run-the-template"></a>執行範本
在 **scripts/README.md**中，有可供執行範本的詳細指示。

若要在您自己的 Azure 帳戶中使用指令碼佈建上述所有服務，請執行下列其中一項︰

* 使用 PowerShell：
  
  ```
  
  cd scripts/PowerShell;
  deploy.ps1 *location* *resourceGroupName*
  ```
  
  * *location* 是 [Azure 位置](https://azure.microsoft.com/regions/)，例如 `North Europe`或 `West US`。 使用 `Get-AzureLocation` 尋找可用位置清單。
  * *resourceGroupName* 是您要提供給所有資源所屬群組的名稱。 資源使用完後，您可以刪除此群組來刪除所有資源。
* 以 Bash 執行 DeploymentScripts/Bash/deploy.sh。
* 開啟並建置 Visual Studio 方案 DeploymentScripts/VS/DeployARM.sln。

請注意，每次執行範本都會使用新名稱來建立一組新的資源。 若要刪除資源，請前往入口網站並刪除資源群組。

如果指令碼因任何原因而失敗，您可以重新執行指令碼。

此指令碼可讓您選擇在 Visual Studio Team Services 中設定連續整合。 如果您已設定 Team Services 專案，則會有 URL：https://yourAccountName.visualstudio.com。 請在要求時輸入完整的 URL。 您可以為 Team Services 專案提供新的或現有的名稱。

## <a name="set-up-build-and-test-definitions-in-visual-studio-team-services"></a>在 Visual Studio Team Services 中設定組建和測試定義
在此專案上使用 Team Services 主要是為了其建置和測試功能。 不過，它也提供絕佳的共同作業支援，例如使用看板進行工作管理、與工作和原始檔控制整合的程式碼檢閱，以及閘道組建。 它與其他工具適當地整合，例如 GitHub、Xamarin、HockeyApp，當然還包括 Visual Studio。 您可以隨時透過 Web 介面或透過 Visual Studio 存取此服務。

組建和發行定義中的步驟使用 Team Services [Marketplace](https://marketplace.visualstudio.com/VSTS)中可用的各種外掛程式服務。 除了執行命令列或複製檔案的基本公用程式之外，還有叫用 Xamarin、Android 及其他廠商的組建，並連接到 HockeyApp 的服務。

![Team Services 中的建置選項](./media/iot-solution-build-system/image5.png)

### <a name="build-definitions"></a>組建定義
每個主要目標會有組建定義。 而針對功能和迴歸測試有不同的版本。 這會提供：

* MyDriving.Services (行動應用程式的後端 Web 應用程式)
* MyDriving.Xamarin.Android
  
  * MyDriving.Xamarin.Android - 功能
  * MyDriving.Xamarin.Android - 迴歸
* MyDriving.Xamarin.iOS
  
  * MyDriving.Xamarin.iOS - 功能
  * MyDriving.Xamarin.iOS - 迴歸
* MyDriving.Xamarin.UWP
  
  * MyDriving.Xamarin.UWP - 功能
  * MyDriving.Xamarin.UWP - 迴歸

如果您要查看組態的完整詳細資料，請參閱 [MyDriving 參考指南](http://aka.ms/mydrivingdocs)的第 4.7 節＜Build and Release Configuration＞(組建和發行組態)。 它們會遵循相同的一般模式。 指令碼：

1. 還原 NuGet 封裝。 我們不會在儲存機制中保留已編譯的程式碼，因此每個組建的第一個步驟都是還原所需的 NuGet 封裝。
2. 啟動授權。 此組建會在雲端中執行，因此需要授權時 (特別是針對 Xamarin 組建服務)，則必須在目前的組建電腦上啟用授權。 之後我們會立即停用，以允許在另一部電腦上使用。
3. 使用適當的服務建置。 我們會針對行動應用程式使用 Xamarin 組建，並針對後端 Web 服務使用 Visual Studio 組建。
4. 建置測試。
5. 執行測試。 我們會在 Xamarin Test Cloud 中執行行動應用程式測試。
6. 將組建結果發佈到置放位置。

主要組建的觸發程序會設定為連續整合。 也就是每次將程式碼簽入 master 分支，都會執行組建。

![觸發程序設定為連續整合的介面](./media/iot-solution-build-system/image6.png)

### <a name="release-definitions"></a>發行定義
發行定義的設定方式大致相同。

針對 Web 服務，我們會將部署設定為 Azure Web 應用程式︰

![用於將部署設定為 Azure Web 應用程式的介面](./media/iot-solution-build-system/image7.png)

然後將發行觸發程序設定為連續部署。 也就是每次簽入後且建置成功時，都會更新 Web 應用程式。

![用於將發行觸發程序設定為連續部署的介面](./media/iot-solution-build-system/image8.png)

針對行動應用程式，我們會部署至 HockeyApp：

![用於將行動應用程式部署至 HockeyApp 的介面](./media/iot-solution-build-system/image9.png)

## <a name="explore-telemetry-by-using-application-insights"></a>使用 Application Insights 探索遙測
[Application Insights](../application-insights/app-insights-overview.md) 可收集有關 Web 服務效能和使用狀況的遙測。 Application Insights SDK 會將服務的遙測資料傳送至 Azure 中的 Application Insights 資源。

瀏覽至範本所設定的 Application Insights 資源。 您可以在此瀏覽 [行動應用程式服務專案](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService)的效能圖表。 這些圖表會顯示伺服器要求和回應時間、失敗和例外狀況計數。 此外，還有相依性回應時間 (也就是針對資料庫及機器學習服務等 REST API 進行呼叫) 的圖表。 如有任何效能問題，您便能夠查看是系統的哪個組件造成問題。

![效能圖表範例](./media/iot-solution-build-system/image11.png)

如果您有手動設定的 Web 服務，您可以輕鬆地取得相同的圖表。 在 Web 服務刀鋒視窗上，按一下 [工具] > [擴充功能] > [新增]。 選取 [Application Insights] 。

![用於選取 Application Insights 以取得圖表的介面](./media/iot-solution-build-system/image12.png)

透過使用 Application Insights SDK 檢測應用程式，即可執行功此能。

您可以在開發階段 [加入 Application Insights SDK](../application-insights/app-insights-asp-net.md) ，以加入自訂遙測 (或檢測在 Azure 外某處執行的應用程式)。 這對記錄取決於應用程式的計量會很有幫助，例如使用者的平均行程長度或總里程數。 在 Visual Studio 中，以滑鼠右鍵按一下專案，然後選取 [加入 Application Insights] 。

![用於選取 [新增 Application Insights] 以新增自訂遙測的介面](./media/iot-solution-build-system/image10.png)

如果 Application Insights 發現不尋常的失敗回應數目，就會傳送警示電子郵件。 您也可以針對各種計量 (例如回應時間) 設定自己的警示。

為了確保您的 Web 服務一律啟動且在執行中，您可以設定 [可用性測試](../application-insights/app-insights-monitor-web-app-availability.md)。 這些測試會從世界各地每隔 15 分鐘 Ping 一次您的網站。 同樣地，如果疑似發生問題，您會收到一封電子郵件。

## <a name="estimate-operational-costs"></a>估計作業成本
小規模地執行類似此處的應用程式極為便宜。 許多服務都有免費的入門層，因此開發和小規模作業的成本會很低。 此外，您自己的應用程式當然也不需要用到 MyDriving 中示範的所有功能。

以下是設定 MyDriving 之開發組態的約略成本估計。 我們也指出一些「未」  用到的替代方案。 當您估計自己的成本時，這項資訊可能很有用。

我們假設︰

* 一個不到 5 人 (加上觀察專案關係人) 的小組。
* 執行約一個月。
* 100 位使用者，每天 4 趟行程。

> [!NOTE]
> 如果您不熟悉 Azure，這裡有 [免費帳戶](https://azure.microsoft.com/free/)。
> 
> 

| **服務/元件** | **注意事項** | **每月成本** |
| --- | --- | --- |
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) 與 [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>跨平台開發環境 |Visual Studio Community。 (需要適用於 [Xamarin.Forms](https://xamarin.com/forms) 的 [Visual Studio Professional](https://www.visualstudio.com/vs-2015-product-editions)，以從單一程式碼基底設定跨平台)。 |美金 $0 元 |
| [Azure IoT 中心](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>裝置的雙向資料連接 |8,000 則訊息 + 每則訊息 0.5 KB 免費。 |美金 $0 元 |
| [串流分析](https://azure.microsoft.com/pricing/details/stream-analytics/)  <br/>   大量串流資料處理 |啟用時，每小時每個串流單位收取美金 $0.031 元。 您可以選擇所需的串流單位數目；選擇更多可相應增加。 |美金 $23 元 |
| [機器學習服務](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> 自適性回應 |每月每個基座美金 $10 元。 <br/>                                                                                                                                                                                 + 3 小時實驗 \* 美金 $1 元 / 實驗小時。 <br/>                                                                                                                                                           + 3.5 小時 API CPU \* 美金 $2 元 / 生產 CPU 小時。 <br/>                                                                                                                                                          API CPU 時間假設每天重新訓練 5 分鐘，不過這會隨輸入資料愈多而增加。                   <br/>                                                                                                                                                                     + 每天計分 2 分鐘以處理每天 400 趟行程。 |美金 $20 元 |
| [App Service](https://azure.microsoft.com/pricing/details/app-service/)  <br/> 裝載行動後端 |B1 層--生產環境 Web 應用程式。 |美金 $56 元 |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/)  <br/> 組建、單元測試和發行管理；工作管理 |私人 Agent，5 位使用者。 |美金 $0 元 |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>監視 Web 服務和網站的效能和使用狀況 |免費層。 |美金 $0 元 |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> 散發 Beta 版應用程式，以及收集意見反應、使用狀況和當機資料 |新使用者有兩個免費的應用程式。<br/> 之後每月美金 $30 元。 |美金 $0 元 |
| [Xamarin](https://store.xamarin.com/)<br/> 多部裝置之統一平台上的程式碼 |免費試用版。 <br/>之後每月美金 $25 元。 |美金 $0 元 |
| [SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  |基本層；單一資料庫模型。 |美金 $5 元 |
| [Service Fabric](https://azure.microsoft.com/pricing/details/service-fabric/) (選擇性) |執行本機叢集。 |美金 $0 元 |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> 對串流處理的靜態資料進行多元顯示及調查 |免費層：1GB，每小時 10,000 列，每天重新整理。 <br/> 針對 [更高限制](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/)、更多連接選項和共同作業，則為每月每位使用者美金 $10 元。 |美金 $0 元 |
| [儲存體](https://azure.microsoft.com/pricing/details/storage/) |L (本機備援) &lt; 100 G 美金 $0.024 元/GB。 |美金 $3 元 |
| [Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) |每個活動美金 $0.60 元 \* (8 - 5 FOC)。 |美金 $2 元 |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) <br/>  用於每日重新訓練的隨選叢集 |3 個 A3 節點，每天 1 小時每小時美金 $0.32 元 * 31 天。 |美金 $30 元 |
| [事件中樞](https://azure.microsoft.com/pricing/details/event-hubs/) |基本為每月輸送量單位美金 $11 元 + 美金 $0.028 元的輸入。 |美金 $11 元 |
| OBD 硬體鎖 | |美金 $12 元 |
| **總計** | |**美金 $157 元** |

如需詳細資訊，請參閱：

* [Azure 服務配額與限制](../azure-subscription-service-limits.md#iot-hub-limits)
* [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)

## <a name="send-us-your-feedback"></a>將您的意見反應傳給我們
由於我們建立 MyDriving 的目的，是為了協助您快速開始建立自己的 IoT 系統，因此當然想聽到您對其運作方式的意見反應！ 若發生下列情況，請告訴我們：

* 您遇到問題或挑戰。
* 沒有擴充點可更適合您的案例。
* 您發現更有效率的方式來完成特定需求。
* 您有改善 MyDriving 或這份文件的任何其他建議。

若要提供意見反應，請 [在 GitHub 上提出問題]，或在底下留下意見 (英文版)。

我們期待聽到您的意見反應！

## <a name="next-steps"></a>後續步驟
我們所建議的 [MyDriving 參考指南](http://aka.ms/mydrivingdocs)完整說明系統及其元件的設計。


