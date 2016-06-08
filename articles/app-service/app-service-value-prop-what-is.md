<properties 
	pageTitle="什麼是 Azure App Service | Microsoft Azure" 
	description="了解 Azure App Service 如何協助您開發、部署和管理 Web 和行動應用程式。" 
	keywords="App Service, Azure App Service, App Service 成本, 級別, 可調整, 應用程式部署, Azure 應用程式部署, paas, 平台即服務"
	services="app-service" 
	documentationCenter="" 
	authors="omarkmsft" 
	manager="dwrede" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="omark"/>

# 什麼是 Azure App Service？

「App Service」是 Microsoft Azure 的[平台即服務](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) 產品，可讓您針對任何平台或裝置建立 Web 和行動應用程式。您可以輕鬆地將應用程式整合到 SaaS 解決方案 (例如 Office 365、Dynamics CRM、Salesforce、Twilio)、輕鬆地連接內部部署應用程式 (例如 SAP、Oracle、Siebel)，以及輕鬆地自動執行商務程序，同時滿足嚴苛的安全性、可靠性和延展性需求。

App Service 包含先前以 Azure 網站和 Azure 行動服務的形式來獨立提供的 Web 和行動功能。此外，它也包含可用來自動執行商務程序及裝載雲端 API 的新功能。

## App Service 中的應用程式類型

App Service 提供下列可供執行應用程式程式碼或工作流程處理的應用程式類型。

- [**Web Apps**](../app-service-web/app-service-web-overview.md) - 用於裝載網站和 Web 應用程式。

- [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) - 用於裝載行動應用程式後端。
   
- [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) - 用於裝載雲端 API。
 
- [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - 用於將跨雲端的資料存取和使用自動化，而不需要撰寫程式碼。

做為單一的整合式服務，App Service 可讓您輕鬆地將多個應用程式類型撰寫成單一方案。

## App Service 方案和環境

[App Service 方案](azure-web-sites-web-hosting-plans-in-depth-overview.md)代表應用程式執行所在的計算資源。在較低的定價層上，應用程式會在共用虛擬機器 (VM) 上執行。在較高的定價層上，應用程式則會在專用 VM 上執行。您可以選擇幾種 VM 大小，而且不用停機即可變更定價層。如果您需要更多的延展性和網路隔離，則可在 [App Service 環境](../app-service-web/app-service-app-service-environment-intro.md)中執行應用程式。

## 為何要使用 App Service？

以下是 App Service 的一些重要功能和能力︰

- **完全受管理的平台** - 自動修補 OS 和架構，內建支援備份和災害復原。 

- **使用現有技能** - 以您偏好的語言、架構和開發環境編寫程式碼。App Service 支援 .NET、Node.js、Java、PHP 和 Python。

- **快速部署** - 在幾秒鐘之內佈建新的應用程式，並對其部署程式碼。

- **持續整合** - 使用 Visual Studio Team Services、GitHub 或 BitBucket 設定[持續整合和部署](../app-service-web/app-service-continous-deployment.md)。

- **預備環境和測試環境** - 實作[分段部署](../app-service-web/web-sites-staged-publishing.md)以在與生產環境相同的生產前環境中驗證程式碼。當您準備好時，執行交換作業，以發行零停機時間的應用程式新版本。

- **在生產環境中測試** - 將分段部署帶到下一個層級，並[執行 A/B 測試](../app-service-web/app-service-web-test-in-production-get-start.md)以即時流量的可設定部分確認新的程式碼。

- **驗證和授權** - 保護應用程式，使其免於未經驗證的存取，且不必變更程式碼。內建的驗證服務會保護應用程式以供使用者、代表使用者的用戶端或服務來存取。支援的識別提供者包括：Azure Active Directory、Facebook、Twitter、Google 和 Microsoft 帳戶。如需詳細資訊，請參閱 [Azure App Service 中的驗證與授權](app-service-authentication-overview.md)。

- **連接到任何服務** - 使用內建的[連接器](../connectors/apis-list.md)，在幾分鐘內將應用程式連接到企業系統或軟體即服務 (SaaS) 平台。有超過 50 種適用於企業系統 (例如 SAP、Siebel 和 Oracle)、常用的企業 SaaS 服務 (例如 Salesforce 和 Office 365)，以及常用的網際網路服務 (例如 Facebook、Twitter 和 Dropbox) 的連接器可供選擇。

- **全球規模** - [相應增加](../app-service/app-service-scale.md)或[相應放大](../azure-portal/insights-how-to-scale.md)以處理任何內送的客戶載入。手動選取 VM 的數目和大小，或設定以負載或排程為基礎的自動調整。Microsoft 的全球資料中心基礎結構會裝載應用程式，並可輕鬆地在多個位置複寫資料和裝載服務。

- **企業級** - App Service 的設計目的是建置和裝載安全的關鍵任務應用程式。建置安全地連線到內部部署資源的 Active Directory 整合商務應用程式，然後將它們裝載在 [ISO、SOC 和 PCI 相容的](https://www.microsoft.com/TrustCenter/)安全雲端平台。全都具有企業級 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/)。

- **Azure Marketplace** - 從不斷成長的[應用程式範本清單](https://azure.microsoft.com/marketplace/)中進行選取。使用單鍵安裝封裝 (例如 WordPress、Joomla 和 Drupal)，充分運用 OSS 應用程式社群。

- **Webjobs** - 在 App Service VM 上，[執行任何程式或指令碼](../app-service-web/web-sites-create-web-jobs.md)。以連續方式、依排程或由事件觸發來執行工作。Azure [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) 可簡化您撰寫來與其他 Azure 和協力廠商服務整合的程式碼。

- **混合式連線** - 使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虛擬網路](../app-service-web/web-sites-integrate-with-vnet.md)存取內部部署資料。

- **Visual Studio 整合** - Visual Studio 中的專用工具，可簡化建立、部署、使用、偵錯和管理 Web 應用程式、行動應用程式和 API 應用程式的工作。

## 開始使用應用程式服務

立即免費[建立暫時的 Web 應用程式、行動應用程式或邏輯應用程式](http://go.microsoft.com/fwlink/?LinkId=523751)，無需信用卡，也無需簽定合約，一切都非常簡單。

或申請[免費 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)，並試試我們的其中一個入門教學課程︰

* [Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)
* [行動應用程式](https://azure.microsoft.com/documentation/services/app-service/mobile/)
* [API 應用程式](https://azure.microsoft.com/documentation/services/app-service/api/)
* [Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/)

<!---HONumber=AcomDC_0601_2016-->