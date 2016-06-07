<properties
	pageTitle="Web Apps 概觀 | Microsoft Azure"
	description="了解 Azure App Service 如何協助您開發和裝載 Web 應用程式"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/25/2016"
	ms.author="tdykstra"/>

# Web 應用程式概觀

「App Service Web Apps」是受到完整管理的計算平台，非常適合用來裝載網站和 Web 應用程式。Microsoft Azure 的這個[平台即服務](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) 產品可讓您專注於商務邏輯，而讓 Azure 負責處理用來執行及調整應用程式的基礎結構。

如需 5 分鐘影片的概觀，請參閱[與 Yochay Kiriaty 一起討論 Azure App Service Web Apps](https://azure.microsoft.com/documentation/videos/azure-app-service-web-apps-with-yochay-kiriaty/)。

## 什麼是 App Service 中的 Web 應用程式？

在 App Service 中，「Web 應用程式」是 Azure 提供用來裝載網站或 Web 應用程式的計算資源。

計算資源可能位於共用或專用的虛擬機器 (VM)，一切視您選擇的定價層而定。應用程式的程式碼會在與其他客戶隔離開來的受管理 VM 中執行。

程式碼可以使用 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 所支援的任何語言或架構，例如 ASP.NET、Node.js、Java、PHP 或 Python。您也可以在 Web 應用程式中，執行使用 [PowerShell 和其他指令碼語言](web-sites-create-web-jobs.md#acceptablefiles)的指令碼。

如需可使用 Web Apps 之典型應用程式案例的範例，請參閱 [Web 應用程式案例](https://azure.microsoft.com/documentation/scenarios/web-app/)。

## 為何要使用 Web Apps？

以下是 App Service 適用於 Web Apps 的一些主要功能︰

- **完全受管理的平台** - 自動修補 OS 和架構，內建支援備份和災害復原。 

- **使用現有技能** - 以您偏好的語言、架構和開發環境編寫程式碼。App Service 支援 .NET、Node.js、Java、PHP 和 Python。

- **快速部署** - 在幾秒鐘之內佈建新的應用程式，並對其部署程式碼。

- **持續整合** - 使用 Visual Studio Team Services、GitHub 或 BitBucket 設定[持續整合和部署](../app-service-web/app-service-continous-deployment.md)。

- **預備環境和測試環境** - 實作[分段部署](../app-service-web/web-sites-staged-publishing.md)以在與生產環境相同的生產前環境中驗證程式碼。當您準備好時，執行交換作業，以發行零停機時間的應用程式新版本。

- **在生產環境中測試** - 將分段部署帶到下一個層級，並[執行 A/B 測試](../app-service-web/app-service-web-test-in-production-get-start.md)以即時流量的可設定部分確認新的程式碼。

- **驗證和授權** - 保護應用程式，使其免於未經驗證的存取，且不必變更程式碼。內建的驗證服務會保護應用程式以供使用者、代表使用者的用戶端或服務來存取。支援的識別提供者包括：Azure Active Directory、Facebook、Twitter、Google 和 Microsoft 帳戶。如需詳細資訊，請參閱 [Azure App Service 中的驗證與授權](../app-service/app-service-authentication-overview.md)。

- **連接到任何服務** - 使用內建的[連接器](../connectors/apis-list.md)，在幾分鐘內將應用程式連接到企業系統或軟體即服務 (SaaS) 平台。有超過 50 種適用於企業系統 (例如 SAP、Siebel 和 Oracle)、常用的企業 SaaS 服務 (例如 Salesforce 和 Office 365)，以及常用的網際網路服務 (例如 Facebook、Twitter 和 Dropbox) 的連接器可供選擇。

- **全球規模** - [相應增加](../app-service/app-service-scale.md)或[相應放大](../azure-portal/insights-how-to-scale.md)以處理任何內送的客戶載入。手動選取 VM 的數目和大小，或設定以負載或排程為基礎的自動調整。Microsoft 的全球資料中心基礎結構會裝載應用程式，並可輕鬆地在多個位置複寫資料和裝載服務。

- **企業級** - App Service 的設計目的是建置和裝載安全的關鍵任務應用程式。建置安全地連線到內部部署資源的 Active Directory 整合商務應用程式，然後將它們裝載在 [ISO、SOC 和 PCI 相容的](https://www.microsoft.com/TrustCenter/)安全雲端平台。全都具有企業級 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/)。

- **Azure Marketplace** - 從不斷成長的[應用程式範本清單](https://azure.microsoft.com/marketplace/)中進行選取。使用單鍵安裝封裝 (例如 WordPress、Joomla 和 Drupal)，充分運用 OSS 應用程式社群。

- **Webjobs** - 在 App Service VM 上，[執行任何程式或指令碼](../app-service-web/web-sites-create-web-jobs.md)。以連續方式、依排程或由事件觸發來執行工作。Azure [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) 可簡化您撰寫來與其他 Azure 和協力廠商服務整合的程式碼。

- **混合式連線** - 使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虛擬網路](../app-service-web/web-sites-integrate-with-vnet.md)存取內部部署資料。

- **Visual Studio 整合** - Visual Studio 中的專用工具，可簡化建立、部署、使用、偵錯和管理 Web 應用程式、行動應用程式和 API 應用程式的工作。

此外，Web 應用程式可以利用 [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) (例如 CORS 支援) 和 [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) (例如推播通知) 所提供的功能。這三個應用程式類型 (API、Web、行動) 之間的唯一差異是它們在 Azure 入口網站中所使用的名稱和圖示。如需 App Service 中的應用程式類型詳細資訊，請參閱 [Azure App Service 概觀](../app-service/app-service-value-prop-what-is.md)。

除了 App Service 中的 Web Apps，Azure 還提供可用來裝載網站和 Web 應用程式的其他服務。大部分的情況下，Web Apps 是最佳選擇。若是微服務架構，請考慮使用 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)，如果您需要能更加充分地掌控程式碼執行所在的 VM，則請考慮使用 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。如需如何在這些 Azure 服務之間做選擇的詳細資訊，請參閱 [Azure App Service、虛擬機器、Service Fabric 及雲端服務的比較](choose-web-site-cloud-service-vm.md)。

## 開始使用

若要開始在 App Service 中的新 Web 應用程式部署範例程式碼，請遵循[在 5 分鐘內將第一個 Web 應用程式部署至 Azure 中](app-service-web-get-started.md)教學課程。您將需要免費的 Azure 帳戶。

如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；無需承諾。

<!---HONumber=AcomDC_0601_2016-->