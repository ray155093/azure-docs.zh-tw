---
title: 在 Azure 應用程式中使用內部部署 Active Directory 進行驗證 | Microsoft Docs
description: 了解 Azure App Service 中可使用內部部署 Active Directory 來進行驗證的企業營運應用程式的不同選項
services: app-service
documentationcenter: ''
author: cephalin
manager: wpickett
editor: jimbe

ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin

---
# 在 Azure 應用程式中使用內部部署 Active Directory 進行驗證
本文說明如何在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中使用內部部署 Active Directory (AD) 進行驗證。雖然 Azure 應用程式裝載於雲端，但仍有方式可安全地驗證內部部署 AD 使用者。

## 透過 Azure Active Directory 進行驗證
Azure Active Directory 租用戶的目錄可與內部部署 AD 進行同步處理。這個方法可讓 AD 使用者從網際網路存取應用程式，並使用其內部部署認證進行驗證。此外，Azure App Service 還提供[適用於此方法的周全解決方案](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。只要按幾下按鈕，您就可以使用 Azure 應用程式的目錄同步租用戶進行驗證。此方法具有下列優點：

* 應用程式中不需要任何驗證程式碼。可讓 App Service 為您進行驗證，把省下的時間花在提供應用程式的功能。
* [Azure AD 圖形 API](http://msdn.microsoft.com/library/azure/hh974476.aspx) 可從 Azure 應用程式存取目錄資料。
* 為 [Azure Active Directory 支援的所有應用程式](/marketplace/active-directory/) (包括 Office 365、Dynamics CRM Online、Microsoft InTune 及數千種非 Microsoft 的雲端應用程式) 提供 SSO。
* Azure Active Directory 支援角色型存取控制。您可以使用 [Authorize(Roles="X")] 模式，而且只需要對程式碼進行最少量的變更。

若要了解如何撰寫使用 Azure Active Directory 進行驗證的企業營運 Azure 應用程式，請參閱[使用 Azure Active Directory 驗證建立企業營運 Azure 應用程式](web-sites-dotnet-lob-application-azure-ad.md)。

## 透過內部部署 STS 進行驗證
如果您有內部部署的安全權杖服務 (STS) (例如 Active Directory Federation Services (AD FS))，您可以使用該服務同盟 Azure 應用程式的驗證。當公司原則禁止 AD 資料儲存在 Azure 時，這是最合適的方法。不過，請注意下列事項︰

* STS 拓撲必須在內部部署，需要成本和管理費用。
* 只有 AD FS 系統管理員可設定[信賴憑證者信任和宣告規則](http://technet.microsoft.com/library/dd807108.aspx)，這可能會限制開發人員的選項。但另一方面，這可以依每一應用程式來管理及自訂[宣告](http://technet.microsoft.com/library/ee913571.aspx)。
* 想要存取內部部署 AD 資料就必須有可以穿越公司防火牆的個別解決方案。

若要了解如何撰寫使用內部部署 STS 進行驗證的企業營運 Azure 應用程式，請參閱[使用 AD FS 驗證建立企業營運 Azure 應用程式](web-sites-dotnet-lob-application-adfs.md)。

<!---HONumber=AcomDC_0831_2016-->