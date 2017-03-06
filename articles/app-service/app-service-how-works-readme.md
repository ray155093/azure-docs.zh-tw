---
title: "Azure App Service 的運作方式"
description: "了解 App Service 的運作方式"
keywords: "App Service, Azure App Service, 級別, 可調整, App Service方案, App Service 成本"
services: app-service
documentationcenter: 
author: yochay
manager: erikre
editor: 
ms.assetid: ae74fc32-969e-4580-8d61-02c922f1f184
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/23/2017
ms.author: yochayk
translationtype: Human Translation
ms.sourcegitcommit: edb3325414adf876548181243ddfa2d515aeb0b8
ms.openlocfilehash: 2d830963d3d2adba71a6ca99f79eac0fc8cbfb12
ms.lasthandoff: 02/24/2017


---
# <a name="how-app-service-works"></a>App Service 的運作方式
Azure App Service 是一項雲端服務，設計用來解決現今工程師所面臨的實務問題。
App Service 著重於提供絕佳的開發人員生產力，而不需妥協於以雲端規模提供應用程式的需求。 

App Service 也會提供建立企業營運應用程式所需的功能與架構。 App Service 可讓您使用最熱門的開發語言 (包括 Java、PHP、Node.js、Python 和 Microsoft.NET 語言) 來開發應用程式。 使用 App Service，您可以：

* 建置可高度調整的 Web Apps。
* 使用一組容易使用的行動功能 (例如資料後端、使用者驗證及推播通知) 快速建置 Mobile Apps 後端。
* 使用 API Apps 實作、部署和發佈 API。
* 將商務應用程式結合到工作流程中，並使用 Logic Apps 轉換資料。

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

所有的應用程式類型均依賴可調整和彈性的 Web Apps 平台，其可讓開發人員擁有從應用程式設計到應用程式維護的最佳化完整生命週期體驗。 生命週期功能可達到下列各項：

* **快速建立應用程式**。 從頭開始，或從 Azure Marketplace 選取作業支援系統 (OSS) 套件。
* **連續部署**。 從受歡迎的原始檔控制解決方案 (例如 TFS、GitHub 和 Bitbucket) 自動部署新程式碼和從線上儲存服務 (例如 OneDrive 和 Dropbox) 同步處理內容。
* **在生產環境中測試**。 順利建立預生產環境及管理通往這些環境的流量。 需要時在雲端偵錯，並在發現問題時回復。
* **執行非同步工作和批次作業**。 在背景程序中執行程式碼或根據事件 (例如，訊息登陸在 Azure 儲存體佇列中) 及排定的時間 (CRON) 啟動程式碼。
* **調整應用程式**。 使用許多選項之一，根據流量和資源使用率自動水平及垂直調整服務。 設定您的應用程式專用的私人環境。   
* **維護應用程式**。 運用許多的偵錯和診斷功能防患於未燃並且有效解決問題，無論是即時 (例如自動修復和即時偵錯功能) 或事後 (透過分析記錄檔和記憶體傾印)。

整體而言，App Service 功能可讓開發人員專注於他們的程式碼，並能快速達到穩定且高調整性的生產狀態。 使用 API Apps 和 Logic Apps 功能，開發人員可以建置真實的企業應用程式，以將商務解決方案及內部部署之間的障礙銜接至雲端整合。 

## <a name="videos"></a>影片
* [Azure App Service 架構](https://azure.microsoft.com/documentation/videos/why-azure-web-sites-plus-architecture/)

## <a name="next-steps"></a>後續步驟

在下列其中一個主題中深入了解 App Service：

* [什麼是 Azure 應用程式服務？](app-service-value-prop-what-is.md)
  * [Web 應用程式](../app-service-web/app-service-web-overview.md)
  * [行動應用程式](../app-service-mobile/app-service-mobile-value-prop.md)
  * [API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)
* [Azure App Service 架構 (展示)](http://www.slideshare.net/maartenba/windows-azure-web-sites-things-they-dont-teach-kids-in-school-comunity-day-2013)
* [Azure App Service、雲端服務與虛擬機器之比較](../app-service-web/choose-web-site-cloud-service-vm.md)
* [了解 App Service 方案](azure-web-sites-web-hosting-plans-in-depth-overview.md)
* [App Service 環境簡介](../app-service-web/app-service-app-service-environment-intro.md)
  * [演練：建立 App Service 環境](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Azure App Service 開發堆疊支援](https://azure.microsoft.com/blog/windows-azure-websites-development-stacks-support/)




