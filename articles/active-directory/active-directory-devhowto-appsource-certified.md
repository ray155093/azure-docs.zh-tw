---
title: 如何讓 AppSource 取得 Azure Active Directory (AD) 認證 | Microsoft Docs
description: 有關如何讓應用程式 AppSource 取得 Azure Active Directory 認證的詳細資料。
services: active-directory
documentationcenter: ''
author: skwan
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2016
ms.author: skwan;bryanla

---
# <a name="how-to-get-appsource-certified-for-azure-active-directory-(ad)"></a>如何讓 AppSource 取得 Azure Active Directory (AD) 認證
若要得到 Azure AD 的 AppSource 認證，您的應用程式必須使用 OpenID Connect、OAuth 2.0 或 SAML 2.0 通訊協定來實作與 Azure AD 搭配運作的多租用戶登入模式。 

如果您不熟悉 Azure AD 登入或多租用戶應用程式開發：

1. 從閱讀 Azure AD 驗證案例中的[瀏覽器到 Web 應用程式案例][AAD-Auth-Scenarios-Browser-To-WebApp]著手。 
2. 接者，參閱 Azure AD [Web 應用程式快速入門指南][AAD-QuickStart-Web-Apps]，這些指南示範如何實作登入，並且包含程式碼範例。 
3. 若要了解如何實作與 Azure AD 搭配運作的多租用戶登入模式，請參閱[如何使用多租用戶應用程式模式讓任何 Azure Active Directory (AD) 使用者登入][AAD-Howto-Multitenant-Overview]

## <a name="related-content"></a>相關內容
若要了解如何建置支援 Azure AD 登入的應用程式，或是要取得說明和支援，請參閱 [Azure AD 開發人員指南][AAD-Dev-Guide]。

請使用下方的 Disqus 註解區段來提供意見反應，並協助我們改善及設計我們的內容。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->













<!--HONumber=Oct16_HO2-->


