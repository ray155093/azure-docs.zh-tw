<properties
	pageTitle="開始使用 Azure App Service 和 ASP.NET Web API 2 | Microsoft Azure"
	description="了解如何在 Visual Studio 中建立 ASP.NET Web API 2 專案，並將它部署到 Azure App Service 中的新 API 應用程式。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="tdykstra"/>

# 使用 Visual Studio 來開始使用 Azure App Service 和 ASP.NET Web API 2

## 概觀

本教學課程示範如何使用 Visual Studio 2015，將 ASP.NET Web API 2 應用程式部署至 Azure App Service 中的 [API 應用程式](app-service-api-apps-why-best-platform.md)。您將會建立 Visual Studio 專案並將它部署至 API 應用程式，如下圖所示。

![Visual Studio 建立和部署圖表](./media/app-service-api-dotnet-get-started-template/Create_App.png)

本教學課程假設您是先前沒有 Azure 使用經驗的 ASP.NET 開發人員。完成此教學課程後，您將有個簡單的 Web API 已在雲端中啟動並執行。

您將了解：

* 如何在 Visual Studio 中建立新的 Web API 2 專案時建立新的 App Service API 應用程式。
* 如何使用 Visual Studio 將 Web API 2 專案部署到 App Service API 應用程式。
* 如何使用 [Azure 入口網站](/features/azure-portal/)來監控及管理您的 API 應用程式。

本教學課程最後的[疑難排解](#troubleshooting)一節會概述發生問題時的處理方式，而[後續步驟](#next-steps)一節則提供其他會更深入探討如何使用 Azure App Service 之教學課程的連結。

## 必要條件

### Azure 帳戶

您需要有 Azure 帳戶，才能完成本教學課程。您可以：

* [免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F)。您將獲得能用來試用 Azure 付費服務的額度。即使在額度用完後，您仍可保留帳戶並使用免費的 Azure 服務和功能。
* [啟用 Visual Studio 訂閱者權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。

如果您想要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)。您可以於該處，在 App Service 中立即建立短期的入門應用程式 — 不需信用卡，不需任何承諾。

### <a name="setupdevenv"></a>Visual Studio 2015 (含 Azure SDK for .NET)

本教學課程是特別為 Visual Studio 2015 (含 [Azure SDK for .NET](../dotnet-sdk.md) 2.8.2 或更新版本) 所撰寫。[在此下載最新的 Azure SDK for Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003)。如果您沒有 Visual Studio，它會自動與 SDK 一起安裝。

如果您有 Visual Studio 2013，您可以[下載最新的 Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322)。有些畫面看起來可能與插圖不同。

>[AZURE.NOTE] 視您的電腦上有多少 SDK 相依性而定，安裝 SDK 可能需要很長的時間 (從數分鐘到半小時以上不等)。

### ASP.NET Web API 2

本教學課程是關於搭配 Azure App Service 使用 ASP.NET Web API 2；不會教導如何開發 ASP.NET Web API。如需 ASP.NET Web API 2 的簡介，請參閱 [ASP.NET](http://asp.net/) 網站上的[開始使用 ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)。

## 在 Azure App Service 中建立專案和 API 應用程式

第一個步驟是在 Visual Studio 和 Azure App Service 的 API 應用程式中建立 ASP.NET Web API 2 專案。完成時，您會將專案部署至 API 應用程式，以使 Web API 可在網際網路上提供使用。

1. 開啟 Visual Studio 2015。

2. 按一下 [檔案] > [新增] > [專案]。

3. 在 [新增專案] 對話方塊中，依序按一下 [Visual C#] > [Web] > [ASP.NET Web 應用程式] (如有需要，您可以選擇 [Visual Basic])。

3. 確定已選取 [.NET Framework 4.5.2] 來做為目標架構。

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) 會監視您 API 應用程式的可用性、效能和使用情形。在您安裝 Visual Studio 後第一次建立 Web 專案時，預設會勾選 [在專案中新增 Application Insights] 核取方塊。如果您不想嘗試 Application Insights，但系統預設已勾選，請清除該核取方塊。

4. 將應用程式命名為 **MyExample**。

5. 按一下 [確定]。

	![[新增專案] 對話方塊](./media/app-service-api-dotnet-get-started-template/GS13newprojdb.png)

5. 在 [新建 ASP.NET 專案] 對話方塊中，選取 [Azure API 應用程式] 範本。

5. 在 [新建 ASP.NET 專案] 核取方塊中的 [Microsoft Azure] 區段，確認已選取 [在雲端託管]，並在下拉式清單中選取 [App Service]。

	![[新增 ASP.NET 專案] 對話方塊](./media/app-service-api-dotnet-get-started-template/GS13newaspnetprojdb.png)

	這些設定可指示 Visual Studio 為 Web 專案建立 Azure API 應用程式。

6. 按一下 [確定]。

5. 如果您尚未登入 Azure，Visual Studio 會提示您登入。使用您用來管理 Azure 訂用帳戶之帳戶的識別碼和密碼來登入。

	當您登入時，[建立 App Service] 對話方塊會詢問您要建立什麼資源。

	![已登入 Azure](./media/app-service-api-dotnet-get-started-template/configuresitesettings.png)

3. 在 [建立 App Service] 對話方塊中，輸入 [API 應用程式名稱]，而該名稱在 azurewebsites.net 網域中必須是唯一名稱。例如，您可以將它命名為 MyExample 並於右邊加上數字，使其成為唯一的名稱，例如 MyExample810。如果預設名稱針對您所建立，則它將是唯一的，且您可以使用該名稱。

	如果有其他人使用了您輸入的名稱，您就會在右邊看到紅色驚嘆號，而不是綠色勾號，這代表您必須輸入不同的名稱。

	Web API 的基底 URL 是這個名稱加上 .azurewebsites.net (如 [Web 應用程式名稱] 文字方塊旁所示)。例如，若名稱為 `MyExample810`，則 URL 是 `myexample810.azurewebsites.net`。

6. 在 [資源群組] 方塊旁，按一下 [新增]，然後輸入 "MyExample" 或其他您偏好的名稱。

	此組合方塊可讓您選取現有的資源群組，或藉由輸入與您的訂用帳戶中任何現有的資源群組不同的名稱，以建立新的資源群組。

	資源群組是 Azure 資源的集合，例如 API 應用程式、資料庫和 VM。在本教學課程中，最好建立新的資源群組，因為這麼做即可在一個步驟中輕鬆刪除您為本教學課程建立的所有 Azure 資源。如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../resource-group-overview.md)。

4. 按一下 [App Service 方案] 下拉式清單旁邊的 [新增] 按鈕。

	![建立 App Service 對話方塊](./media/app-service-api-dotnet-get-started-template/createas.png)

	螢幕擷取畫面顯示 [Web 應用程式名稱]、[訂用帳戶] 和 [資源群組] 的範例值 -- 您的值會有所不同。

	在下列步驟中，您會為新的資源群組建立 App Service 方案。App Service 方案會指定 API 應用程式執行所在的計算資源。例如，如果您選擇免費層，則 API 應用程式會在共用 VM 上執行，若為某些付費層，它則會在專用 VM 上執行。如需詳細資訊，請參閱 [App Service 方案概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

5. 在 [設定 App Service 方案] 對話方塊中，輸入 "MyExamplePlan" 或其他您偏好的名稱。

5. 在 [位置] 下拉式清單中，選擇最接近您的位置。

	這個設定會指定應用程式將執行所在的 Azure 資料中心。您可以在本教學課程中選取任何區域，這不會造成顯著的差異。但是對於生產用的應用程式，您的伺服器應盡可能靠近存取該應用程式的用戶端，以將[延遲](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)降到最低。

5. 在 [大小] 下拉式清單中，按一下 [免費]。

	在本教學課程中，免費定價層會提供足夠的效能。

6. 在 [設定 App Service 方案] 對話方塊中，按一下 [確定]。

	![設定 App Service 對話方塊](./media/app-service-api-dotnet-get-started-template/configasp.png)

7. 在 [建立 App Service] 對話方塊中，按一下 [建立]。

	![建立 App Service 對話方塊](./media/app-service-api-dotnet-get-started-template/clickcreate.png)

	通常在不到一分鐘的時間內，Visual Studio 就會建立 Web 專案和 API 應用程式。

	[方案總管] 視窗會顯示新專案中的檔案和資料夾。

	![Solution Explorer](./media/app-service-api-dotnet-get-started-template/solutionexplorer.png)

	[Azure App Service 活動] 視窗會顯示已建立 API 應用程式。(雖然訊息可能會稱它為 Web 應用程式)。

	![在 [Azure App Service 活動] 視窗中建立的 API 應用程式](./media/app-service-api-dotnet-get-started-template/GS13sitecreated1.png)

	您可以在 Visual Studio 的 [雲端總管] 視窗中看到 API 應用程式。

	![在 [雲端總管] 中建立的 API 應用程式](./media/app-service-api-dotnet-get-started-template/siteinse.png)
	
	此視窗可讓您檢視和管理各種 Azure 資源。您在 [雲端總管] 視窗中看到的資源類型可能會和此範例中的不同。以滑鼠右鍵按一下 API 應用程式等資源，以查看其管理選項。

## 將 Visual Studio 專案部署至 Azure API 應用程式

在本節中，您會將 Web 專案部署到 API 應用程式，如圖表中的步驟 2 所示。

![Visual Studio 建立和部署圖表](./media/app-service-api-dotnet-get-started-template/Create_App.png)

1. 在 [方案總管] 中以滑鼠右鍵按一下專案，再選擇 [發行]。

	![在 Visual Studio 功能表中選擇 [發佈]](./media/app-service-api-dotnet-get-started-template/choosepublish.png)

	[發佈 Web] 精靈會在幾秒鐘後出現。此精靈會開啟某個發佈設定檔，其中包含能將專案部署到新 API 應用程式的設定。如果您想要部署到不同的 API 應用程式，可以按一下 [設定檔] 索引標籤來建立不同的設定檔。在本教學課程中，您會接受部署到您稍早建立的 API 應用程式的設定。

	發佈設定檔包含用於部署的使用者名稱和密碼。這些認證已事先為您產生，因此您不需要加以輸入或變更。密碼會加密並存放在 `Properties\PublishProfiles` 資料夾的使用者專屬隱藏檔案中。

8. 在 [發佈 Web] 精靈的 [連線] 索引標籤中，按 [下一步]。

	![在 [發佈 Web] 精靈的 [連線] 索引標籤中按 [下一步]](./media/app-service-api-dotnet-get-started-template/GS13ValidateConnection.png)

	下一個索引標籤是 [設定] 索引標籤 (如下所示)。您可以在此變更組建組態索引標籤，以部署用於[遠端偵錯](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)的偵錯組建。此索引標籤也會提供數個 [檔案發佈選項]：

	* 在目的地移除多餘的檔案
	* 在發行期間預先編譯
	* 從 App\_Data 資料夾中排除檔案

	在本教學課程中，您不需要這些。如需它們執行了哪些作業的說明，請參閱[操作說明：在 Visual Studio 中使用單鍵發佈來部署 Web 專案](https://msdn.microsoft.com/library/dd465337.aspx)。

10. 在 [設定] 索引標籤上，按 [下一步]。

	![[發佈 Web] 精靈的 [設定] 索引標籤](./media/app-service-api-dotnet-get-started-template/GS13SettingsTab.png)

	下一個索引標籤是 [預覽] (如下所示)。您可以查看即將從您的專案複製到 API 應用程式的檔案。當您將專案部署至您先前已部署至的 API 應用程式時，只會複製已變更的檔案。如果您想要查看即將複製的項目清單，可以按一下 [開始預覽] 按鈕。

11. 在 [預覽] 索引標籤上，按一下 [發佈]。

	![[發佈 Web] 精靈的 [預覽] 索引標籤](./media/app-service-api-dotnet-get-started-template/GS13previewoutput.png)

	當您按一下 [發佈] 時，Visual Studio 就會開始將檔案複製至 Azure 伺服器。這可能需要數分鐘的時間。

	[輸出] 與 [Azure App Service 活動] 視窗會顯示已採取的部署動作，並回報部署作業已順利完成。

	![報告部署成功的 Visual Studio [輸出] 視窗](./media/app-service-api-dotnet-get-started-template/PublishOutput.png)

	部署成功時，即會自動在預設瀏覽器中開啟已部署之 API 應用程式的基底 URL。瀏覽器會顯示一個頁面，內有訊息指出「已成功建立此 Web 應用程式」。

	![報告部署成功的 Visual Studio [輸出] 視窗](./media/app-service-api-dotnet-get-started-template/successfullycreated.png)

> [AZURE.TIP] 您可以啟用 [Web 單鍵發佈] 工具列來加快部署速度，方法是依序按一下 [檢視] > [工具列]，然後選取 [Web 單鍵發佈]。您可以使用工具列來選取設定檔、按一下按鈕來發佈，或按一下按鈕來開啟 [發佈 Web] 精靈。![Web 單鍵發行工具列](./media/app-service-api-dotnet-get-started-template/weboneclickpublish.png)

## 測試已部署的 Web API

1. 使用您偏好的 HTTP 用戶端工具傳送 HTTP GET 要求至 URL {您的 API 應用程式名稱}.azurewebsites.net/api/values。

	Web API 專案範本會定義 `Values` 控制器，其會以 JSON 格式傳回 GET 要求的雙字串陣列。下圖顯示 [Postman](http://www.getpostman.com/) 所傳送的要求，且其回應本文中傳回了 JSON。

	![報告部署成功的 Visual Studio [輸出] 視窗](./media/app-service-api-dotnet-get-started-template/postman.png)

2. 您現在可以變更程式碼、使用和初始部署相同的方式重新部署專案，以及在幾秒鐘的時間內看到變更在 Azure 中生效。

## <a id="portal"></a>選擇性︰在 Azure 入口網站中監控及管理 API 應用程式

[Azure 入口網站](/services/management-portal/)是您可以用來管理及監控 Azure 服務 (例如，您剛建立的 API 應用程式) 的 Web 介面。在教學課程的這一節中，您會了解可在入口網站中執行的一些作業。

1. 在瀏覽器中，移至 [https://portal.azure.com](https://portal.azure.com)，然後用您管理 Azure 帳戶的認證登入。
	
2. 按一下 [應用程式服務]，然後按一下您 API 應用程式的名稱。

	![Azure 入口網站中的應用程式服務](./media/app-service-api-dotnet-get-started-template/selinportal.png)

	[API 應用程式] 刀鋒視窗會顯示您 API 應用程式的設定和使用量統計資料概觀。(在右側開啟的視窗稱為「刀鋒視窗」。)

	![Azure 入口網站中的 [API 應用程式] 刀鋒視窗](./media/app-service-api-dotnet-get-started-template/portaldashboard.png)

	您的 API 應用程式還沒有太多流量，因此圖形中可能不會顯示任何項目。如果您對您的 Web API 再提出一些要求，然後重新整理入口網站頁面，您就會看到一些統計資料出現。

3. [設定] 刀鋒視窗會顯示更多可用來設定您 API 應用程式的選項。

	![Azure 入口網站中的 [設定] 刀鋒視窗](./media/app-service-api-dotnet-get-started-template/portalconfigure1.png)

	[API] 區段會連結至用來設定 API 中繼資料端點和 CORS 的刀鋒視窗。請參閱[後續步驟](#next-steps)一節中將會介紹這些功能的教學課程。

	請注意 [發佈] 區段中的 [部署認證] 連結。您可以在此建立用於部署的自訂使用者名稱和密碼。按一下刀鋒視窗頂端的 [儲存] 按鈕以提交變更。如果您建立新的使用者名稱和密碼，則必須在 Web 專案之 [發佈 Web] 精靈的 [連線] 索引標籤中輸入相同的值。
	
	此螢幕擷取畫面只是 [設定] 刀鋒視窗的部分檢視。此刀鋒視窗還有許多區段並未顯示出來。

這些只是入口網站的少數幾個功能而已。您也可以建立新的 API 應用程式、刪除現有的 API 應用程式、停止及重新啟動 API 應用程式，以及管理其他類型的 Azure 服務，例如資料庫和虛擬機器。

## 選擇性︰刪除 Azure API 應用程式

當您不再需要用到為了本教學課程所建立的 API 應用程式時，您可以將它刪除。

若要刪除 API 應用程式，簡單的方法是按一下 Azure 入口網站的 [API 應用程式] 刀鋒視窗頂端的 [刪除] 按鈕。更好的方法是刪除您建立來包含 API 應用程式的資源群組。在本教學課程中，資源群組只包含 API 應用程式，但資源群組通常包含相關資源的集合。比方說，當您刪除 API 應用程式時，該 API 應用程式所使用的資料庫或 Azure 儲存體帳戶可能也不再需要用到。如果您刪除資源群組，就能刪除它所包含的所有項目。若要使用 Azure 入口網站刪除資源群組，請執行下列步驟。

1. 移至 [Azure 入口網站](https://portal.azure.com)的首頁。

2. 按一下 [資源群組]。

3. 在資源群組清單中，按一下您想要刪除的資源群組。

	當 [資源群組] 刀鋒視窗出現時，其中將會有它所包含的資源清單。

4. 在 [資源群組] 刀鋒視窗中，按一下 [刪除]。

	![在 Azure 入口網站中刪除資源群組](./media/app-service-api-dotnet-get-started-template/delresgrp.png)

## 疑難排解

如果您在進行本教學課程時遇到問題，請確定您使用的是最新版 Azure SDK for .NET。若要這麼做，最簡單的方法是[下載 Azure SDK for Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003)；如果您已安裝最新版本，Web Platform Installer 會指出不需要進行安裝。

如果您位於公司網路內，並嘗試透過防火牆部署至 Azure App Service，請確定連接埠 443 和 8172 已針對 Web Deploy 開啟。如果您無法開啟這些連接埠，請參閱下面的＜後續步驟＞一節以了解其他部署選項。

在 Azure App Service 中執行 ASP.NET API 應用程式之後，建議您深入了解可簡化疑難排解步驟的 Visual Studio 功能。如需記錄和遠端偵錯等功能的相關資訊，請參閱[在 Visual Studio 中針對 Azure API 應用程式進行疑難排解](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)。

## 後續步驟

在本教學課程中，您已了解如何建立簡易的 Web API 並將其部署至 Azure App Service 中的 API 應用程式。如需可讓您更輕鬆地開發和使用 Web API 之 App Service 功能的簡介，請參閱教學課程系列，此系列的第一課是[開始使用 API Apps 和 ASP.NET](app-service-api-dotnet-get-started.md)。

<!---HONumber=AcomDC_0504_2016-->