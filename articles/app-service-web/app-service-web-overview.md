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
	ms.date="05/16/2016"
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

以下是 Web Apps 的一些主要功能︰

- **熟悉且快速** - 使用現有技能，以您偏好的語言、架構和整合式開發環境 (IDE) 編寫程式碼。在幾秒鐘之內佈建新的 Web 應用程式，並對其部署程式碼。

- **企業級** - Web Apps 的設計目的是建置和裝載安全的關鍵任務應用程式。建置安全地連線到內部部署資源的 Active Directory 整合商務應用程式，然後將它們裝載在 [ISO](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001)、[SOC](https://www.microsoft.com/TrustCenter/Compliance/SOC) 和 [PCI](https://www.microsoft.com/TrustCenter/Compliance/pci) 相容的安全雲端平台。全都具有企業級 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/)。

- **全球規模** - Web Apps 可讓您快速地[向上](../app-service/app-service-scale.md)延展或[向下](../azure-portal/insights-how-to-scale.md)延展以處理任何內送的客戶載入。手動選取 VM 的數目和大小，或設定以負載或排程為基礎的自動調整。Microsoft 的全球資料中心基礎結構會裝載 Web 應用程式，並可輕鬆地在多個位置複寫資料和裝載服務。

- **Azure Marketplace** - 從不斷成長的 [Web 應用程式範本清單](https://azure.microsoft.com/marketplace/)中進行選取。使用單鍵安裝封裝 (例如 Wordpress、Joomla 和 Drupal)，充分運用 OSS 應用程式社群。

- **持續整合** - 使用 Visual Studio Team Services、GitHub 或 BitBucket 設定[持續整合和部署](app-service-continous-deployment.md)工作流程。在每個成功的程式碼簽入或整合測試中，自動建置、測試和部署 Web 應用程式。

- **預備環境和測試環境** - 實作[分段部署](web-sites-staged-publishing.md)以在與生產環境相同的生產前環境中驗證程式碼。當您準備好時，執行交換作業，以發行零停機時間的應用程式新版本。

- **在生產環境中測試** - 將分段部署帶到下一個層級，並[執行 A/B 測試](app-service-web-test-in-production-get-start.md)以即時流量的可設定部分確認新的程式碼。

- **Webjobs** - 在 Web Apps VM 上，[執行任何程式或指令碼](web-sites-create-web-jobs.md)。以連續方式、依排程或由事件觸發來執行工作。Azure [WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md) 可簡化您撰寫來與其他 Azure 服務 (例如儲存體佇列、Blob 和資料表，以及服務匯流排佇列和主題) 整合的程式碼。

- **混合式連線** - 使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和 [VNET](../app-service-web/web-sites-integrate-with-vnet.md) 存取內部部署資料。

- **Visual Studio 整合** - Visual Studio 中的專用工具，可簡化建立、部署、使用、偵錯和管理 Web 應用程式的工作。如需詳細資訊，請參閱[發表 Azure SDK 2.8.1 for .NET](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/)。

此外，Web 應用程式可以利用 [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) (例如 CORS 支援) 和 [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) (例如推播通知) 所提供的功能。反之亦然︰您可以使用 API 應用程式或行動應用程式裝載 Web 應用程式，並利用 Web Apps 功能，例如自動調整與預備部署。這三個應用程式類型 (API、Web、行動) 之間的唯一差異是它們在 Azure 入口網站中所使用的名稱和圖示。如需 App Service 中的應用程式類型詳細資訊，請參閱 [Azure App Service 概觀](../app-service/app-service-value-prop-what-is.md)。

## 開始使用

若要開始在 App Service 中的新 Web 應用程式部署範例程式碼，請遵循[在 5 分鐘內將第一個 Web 應用程式部署至 Azure 中](app-service-web-get-started.md)教學課程。您將需要免費的 Azure 帳戶。

如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；無需承諾。

<!---HONumber=AcomDC_0525_2016-->