---
title: "如何讓 AppSource 取得 Azure Active Directory (AD) 認證 | Microsoft Docs"
description: "有關如何讓應用程式 AppSource 取得 Azure Active Directory 認證的詳細資料。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: e1299c1f7f8a31f7034fc0736fcd9d66153a9758
ms.openlocfilehash: 3290a375963bc3e625cbdb05b5f9686e8cfb34f6
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>如何讓 AppSource 取得 Azure Active Directory (AD) 認證
若要得到 Azure AD 的 AppSource 認證，您的應用程式必須使用 OpenID Connect 或 OAuth 2.0 通訊協定來實作與 Azure AD 搭配運作的多租用戶登入模式。  

如果您不熟悉 Azure AD 登入或多租用戶應用程式開發：

1. 從閱讀 [Azure AD 驗證案例中的瀏覽器到 Web 應用程式案例][AAD-Auth-Scenarios-Browser-To-WebApp]著手。  
2. 接者，參閱 Azure AD [Web 應用程式快速入門指南][AAD-QuickStart-Web-Apps]，這些指南示範如何實作登入，並且包含程式碼範例。

   > [!TIP]
   > 試用新 [開發人員入口網站](https://identity.microsoft.com/Docs/Web) 的預覽版本，這可協助您在短短幾分鐘內啟動並執行 Azure Active Directory！  開發人員入口網站會逐步引導您完成註冊應用程式並將 Azure AD 整合至您的程式碼的程序。  當您完成時，您會有可驗證租用戶中使用者的簡單應用程式，以及可接受權杖並執行驗證的後端。
   >
   >
3. 若要了解如何實作與 Azure AD 搭配運作的多租用戶登入模式，請參閱[如何使用多租用戶應用程式模式讓任何 Azure Active Directory (AD) 使用者登入][AAD-Howto-Multitenant-Overview]

## <a name="related-content"></a>相關內容
若要了解如何建置支援 Azure AD 登入的應用程式，或是要取得說明和支援，請參閱 [Azure AD 開發人員指南][AAD-Dev-Guide]。

請使用本文後面的 Disqus 註解區段來提供意見反應，並協助我們改善及設計我們的內容。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#guides


<!--Image references-->

