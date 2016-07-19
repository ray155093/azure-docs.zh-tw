<properties 
	pageTitle="什麼是 Logic Apps？" 
	description="深入了解應用程式服務 Logic Apps" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="07/12/2016"
	ms.author="klam"/>

#什麼是 Logic Apps？

| 快速參考 |
| --------------- |
| [Logic Apps 定義語言](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Logic Apps 連接器文件](../connectors/apis-list.md) |
| [Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Logic Apps 提供簡化和實作複雜商務程序的途徑。它提供視覺化設計工具，以一系列的步驟 (也稱為工作流程) 為您的商務程序建立模型並加以自動化。您可以設計工作流程，讓它們從觸發程序啟動，然後執行每個步驟。每個步驟會叫用 API，同時安全地處理驗證和最佳作法，如檢查點和長期執行。

使用 Logic Apps 的優點包括︰

- 使用易於了解的設計工具來設計複雜程序以節省時間
- 輕鬆地實作難以在程式碼中實作的工作
- 從範本快速開始使用
- 使用 API 自訂邏輯應用程式
- 連接不同的系統
- 讓您的邏輯應用程式在市場中獲利
- 輕送啟動並依需求擴充

Logic Apps 是完全受管理的 iPaaS (整合平台即服務)，可讓開發人員不必擔心要執行額外工作，即可處理裝載、延展性、可用性和管理。

最重要的是，Logic Apps 可以與內建的[受管理連接器][managedapis]結合，協助您輕鬆地解決棘手的整合案例：

![流程應用程式設計工具](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

如先前所述，您可以使用 Logic Apps 將商務程序自動化。以下是一些範例︰
 
* 您可以自動複寫 SQL DB 中的新記錄並傳送郵件到前台。
* 自動尋找負評推文，並將它們傳送至 Slack 通道。
* 在 FTP 伺服器上建立檔案後，剖析檔案、新增記錄至 Dynamics CRM，以及在 SharePoint 清單中建立項目。

這些案例都能從視覺化設計工具加以設定，無需撰寫程式碼。立即開始[建置邏輯應用程式][create]。

## 為什麼要使用 Logic Apps？

如果您要自動執行任何商業程序 (例如尋找負評推文並張貼至內部 Slack 通道或從 SQL 將送達的新客戶記錄複寫道 CRM 系統中)，Logic Apps 可讓您輕鬆整合完全不同的資料來源 (從雲端至內部部署)。查看我們的[受管理連接器][managedapis]以獲得更多靈感，並立即[開始][create]看看您可以做些什麼。

此外，透過我們的[企業整合帳戶][biztalk]，您可以利用 [XML 訊息][xml]、[交易夥伴管理][tpm]等的功能，擴展至成熟的整合案例。

- **容易使用的設計工具** - Logic Apps 可在瀏覽器中端對端地設計。從觸發程序啟動 - 從簡單的排程，到每次有關於貴公司的推文出現時。然後，使用豐富的連接器資源庫協調任何數量的動作。

- **輕鬆撰寫 SaaS** -即使可輕鬆說明的撰寫工作，也很難在程式碼中實作。Logic Apps 可讓您輕鬆連接不同的系統。想要在 CRM 軟體中建立以 Facebook 或 Twitter 帳戶中的活動為基礎的工作嗎？ 想要將您的雲端行銷解決方案連接到內部部署計費系統嗎？ 邏輯應用程式可用最快、最可靠方式，提供這些問題的解決方案。

- **從範本快速開始使用** - 為了方便您著手使用，我們提供[範本庫][templates]，讓您可快速建立一些常見的解決方案。從進階 BizTalk 解決方案到簡單的 SaaS 連線 (甚至只為了「好玩」) - 資源庫是了解Logic Apps 強大功能的最快方式。

- **現成可用的擴充性** - 找不到您所需要的連接器嗎？ Logic Apps 的設計可與 API 應用程式搭配使用；您可以輕鬆地建立您自己的 API 應用程式，以做為自訂 API。建置您專屬的新應用程式，或在 Marketplace 中共用及銷售。

- **真正的整合能力** - 入門容易，並且可依需求擴充。Logic Apps 可輕易地讓 Microsoft 領先業界的整合解決方案 BizTalk 發揮效益，使整合專業人員得以建置其所需的解決方案。深入了解[企業整合套件](./app-service-logic-enterprise-integration-overview.md)。

## 邏輯應用程式概念

以下是構成 Logic Apps 使用性的一些重要元素。

- **工作流程** - Logic Apps 提供圖形化的方式，以一系列的步驟或工作流程的形式為您的商務程序建立模型。
- **受管理連接器** - 邏輯應用程式需要存取資料和服務。受管理連接器是為了讓協助您連接及使用資料而特別設計的。請參閱[受管理連接器][managedapis]中目前提供的連接器清單。
- **觸發程序** - 某些受管理連接器也可以做為觸發程序。觸發程序會根據特定的事件建立新的工作流程執行個體，例如在電子郵件送達或您的 Azure 儲存體帳戶有所變更時。
-  **動作** - 工作流程中觸發程序後的每個步驟登稱為動作。每個動作通常會對應至受管理連接器或自訂 API 應用程式上的作業。
- **企業整合套件** - 在更進階的整合案例中，Logic Apps 會包含來自 BizTalk 的功能。BizTalk 是 Microsoft 領先業界的整合平台。「企業整合套件」連接器可讓您輕鬆地將驗證、轉換等項目納入邏輯應用程式工作流程中。

## 開始使用  

 - 若要開始使用 Logic Apps，請遵循[建立 Logic Apps ][create]教學課程。
 - [檢視常見的範例和案例](app-service-logic-examples-and-scenarios.md)
 - [您可以使用 Logic Apps 自動化商務程序](http://channel9.msdn.com/Events/Build/2016/T694)
 - [了解如何整合您的系統與 Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
- 如需 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][appservice]。

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md

<!---HONumber=AcomDC_0713_2016-->