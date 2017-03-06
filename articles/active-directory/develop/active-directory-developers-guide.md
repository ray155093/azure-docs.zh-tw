---
title: "Azure Active Directory 開發人員指南 | Microsoft Docs"
description: "本文提供 Azure Active Directory 開發人員導向資源的完整指南。"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 8da480acc04922a3636821c5303f6380236c5ea3
ms.openlocfilehash: 1bff76371ea1ca3ee68c9f04ea24a42f95bb7da7
ms.lasthandoff: 02/17/2017


---
# <a name="azure-active-directory-developers-guide"></a>Azure Active Directory 開發人員指南
## <a name="overview"></a>Overview
Azure Active Directory (AD) 是身分識別管理即服務 (IDMaaS) 平台，提供有效的方法，協助開發人員將身分識別管理整合到他們的應用程式中。 下列文章提供實作的概觀和 Azure AD 的重要功能。 我們建議您依序閱讀，如果您尚未準備好開始進行，請跳到 [開始使用](#getting-started) 。

1. [Azure AD 整合的優點](active-directory-how-to-integrate.md)：探索為什麼與 Azure AD 整合是提供安全登入和授權的最佳解決方案。
2. [Azure AD 驗證案例](active-directory-authentication-scenarios.md)：利用 Azure AD 的簡易驗證來提供登入功能給您的應用程式。
3. [整合應用程式與 Azure AD](active-directory-integrating-applications.md)：了解如何從 Azure AD 加入、更新和移除應用程式、以及關於整合應用程式商標指導方針的資訊。
4. [Azure AD 圖形 API](active-directory-graph-api.md)：使用 Azure AD 圖形 API 以程式設計方式透過 REST API 端點存取 Azure AD。 Azure AD Graph API 也是可透過 [Microsoft Graph](https://graph.microsoft.io/)存取。 Microsoft Graph 會提供統一的 API，可透過單一 REST API 端點以單一的存取權杖，存取多個 Microsoft 雲端服務 API。
5. [Azure AD 驗證程式庫](active-directory-authentication-libraries.md)：利用適用於 .NET、JavaScript、Objective-C、Android 及其他項目的 Azure AD 驗證程式庫，輕鬆地驗證使用者的身分來取得存取權杖。

## <a name="getting-started"></a>開始使用
這些教學課程適用於多種平台，可協助您快速開始使用 Azure Active Directory 進行開發。 但您必須先 [取得 Azure Active Directory 租用戶](active-directory-howto-tenant.md)。

### <a name="mobile-and-pc-application-quick-start-guides"></a>行動裝置與電腦應用程式快速入門指南
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | 
|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows</br>通用](active-directory-devquickstarts-windowsstore.md) |

|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|
|[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[直接整合</br>OAuth 2.0](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Web 應用程式快速入門指南
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) |
|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[直接整合</br>OpenID Connect](active-directory-protocols-openid-connect-code.md) |

| [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|
|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[直接整合</br>OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Web API 快速入門指南
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>查詢目錄快速入門指南
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [Graph API](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>作法
這些文章描述如何使用 Azure Active Directory 執行特定工作︰

* [取得 Azure AD 租用戶](active-directory-howto-tenant.md)
* [使用多租用戶應用程式模式登入任何 Azure AD 使用者](active-directory-devhowto-multi-tenant-overview.md)
* [使用憑證而非密碼來驗證應用程式身分識別](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* 在 [Android](active-directory-sso-android.md) 和 [iOS](active-directory-sso-ios.md) 裝置上使用 ADAL 啟用跨應用程式的 SSO
* [讓應用程式 AppSource 取得 Azure AD 認證](active-directory-devhowto-appsource-certified.md)
* [在 Azure AD 應用程式庫中列出您的應用程式](active-directory-app-gallery-listing.md)
* [提交 Office 365 的 Web 應用程式到賣方儀表板](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [使用 Azure 入口網站向 Azure Active Directory 註冊應用程式](../active-directory-app-registration.md)
* [了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)
* [了解用戶端應用程式中登入和取得應用程式按鈕的商標指導方針](active-directory-branding-guidelines.md)
* [預覽：如何建置可同時透過個人和公司/學校帳戶登入使用者的應用程式](active-directory-appmodel-v2-overview.md)
* [預覽：如何建置註冊與登入取用者的應用程式](../../active-directory-b2c/active-directory-b2c-overview.md)
* [使用 PowerShell 在 Azure AD 中設定權杖存留期](../active-directory-configurable-token-lifetimes.md)。 請參閱[原則作業](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)和[原則實體](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)以取得透過 Azure AD Graph API 進行設定的詳細資料。

## <a name="reference"></a>參考
這些文章提供 REST 和驗證程式庫 API、通訊協定、錯誤、程式碼範例和端點的基礎參考。  

### <a name="support"></a>支援
* [標記的問題](http://stackoverflow.com/questions/tagged/azure-active-directory)：如要尋找 Azure Active Directory 對於 Stack Overflow 的解決方案，請搜尋 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 及 [adal](http://stackoverflow.com/questions/tagged/adal) 標記。
* 如需應用程式開發和整合相關的一些常用詞彙的定義，請參閱 [Azure AD 開發人員詞彙](active-directory-dev-glossary.md) 。

### <a name="code"></a>代碼
* [Azure Active Directory 開放原始碼程式庫](http://github.com/AzureAD)：要尋找程式庫原始碼最簡單的方法，就是使用我們的[程式庫清單](active-directory-authentication-libraries.md)。
* [Azure Active Directory 範例](https://github.com/azure-samples?query=active-directory)：要瀏覽範例清單最簡單的方法，就是使用[程式碼範例索引](active-directory-code-samples.md)。
* [適用於 .NET 的 Active Directory Authentication Library (ADAL)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - 提供[最新主要版本](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)和[先前主要版本](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)。

### <a name="graph-api"></a>Graph API
* [Graph API 參考](https://msdn.microsoft.com/library/azure/hh974476.aspx)：Azure Active Directory 圖形 API 的 REST 參考。 [請檢視互動式的圖形 API 參考體驗](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。
* [圖形 API 權限範圍](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)：用以控制應用程式對租用戶中目錄資料存取權的 OAuth 2.0 權限範圍。

### <a name="authentication-and-authorization-protocols"></a>驗證和授權通訊協定
* [在 Azure AD 中簽署金鑰變換](active-directory-signing-key-rollover.md)：了解 Azure AD 的簽署金鑰變換模式，以及如何針對最常見的應用程式案例更新金鑰。
* [OAuth 2.0 通訊協定：使用授權碼授與](active-directory-protocols-oauth-code.md)：您可以使用 OAuth 2.0 通訊協定的授權碼授與，來授權存取您 Azure Active Directory 租用戶中的 Web 應用程式及 Web API。
* [OAuth 2.0 通訊協定︰了解隱含授權](active-directory-dev-understanding-oauth2-implicit-grant.md)︰深入了解隱含授權授與，以及它是否適合您的應用程式。
* [OAuth 2.0 通訊協定：使用認證進行服務對服務呼叫](active-directory-protocols-oauth-service-to-service.md)：OAuth 2.0 用戶端認證可允許 Web 服務 (機密用戶端) 在呼叫另一個 Web 服務時使用它自己的認證來進行驗證，而不必模擬使用者。 在此案例中，用戶端通常是中介層 Web 服務、精靈服務或網站。
* [OpenID Connect 1.0 通訊協定：登入和驗證](active-directory-protocols-openid-connect-code.md)：OpenID Connect 1.0 通訊協定會擴充 OAuth 2.0，來做為驗證通訊協定。 用戶端應用程式可以接收 id_token 以管理登入程序，或增加授權碼流程以接收 id_token 和授權碼。
* [SAML 2.0 通訊協定參考](active-directory-saml-protocol-reference.md)：SAML 2.0 通訊協定可讓應用程式能為使用者提供單一登入體驗。
* [WS-同盟 1.2 通訊協定](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)：根據 Web 服務同盟 1.2 版規格，Azure Active Directory 支援 WS-同盟 1.2。 如需同盟中繼資料文件的詳細資訊，請參閱 [同盟中繼資料](active-directory-federation-metadata.md)。
* [支援的權杖與宣告類型](active-directory-token-and-claims.md)：您可以使用此指南來了解及評估 SAML 2.0 與 JSON Web Token (JWT) 權杖中的宣告。

## <a name="videos"></a>影片
### <a name="build"></a>建置
這些概觀簡報有關於使用 Azure Active Directory 功能開發應用程式，適用於直接在工程團隊工作的講者。 簡報涵蓋一些基本主題，包括 IDMaaS、驗證、身分識別同盟和單一登入。

* [Microsoft 身分識別︰美國現狀和未來展望](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory：新式應用程式的「身分識別管理即服務」](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [使用 Azure Active Directory 開發新式 Web 應用程式](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [使用 Azure Active Directory 開發新式原生應用程式](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) 是每星期五推出的一對一訪談影片系列，每集 10 至 15 分鐘，由專家就各種 Azure 的主題發表意見。  請使用頁面上的 [服務篩選] 功能，查看所有 Azure Active Directory 影片。

* [Azure 身分識別 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Azure 身分識別 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Azure 身分識別 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>社交
* [Active Directory 團隊部落格](http://blogs.technet.com/b/ad/)：提供您 Azure Active Directory 領域的最新發展。
* [Azure Active Directory Graph 團隊部落格](https://blogs.msdn.microsoft.com/aadgraphteam)︰提供您圖形 API 專屬的 Azure Active Directory 資訊。
* [雲端識別](http://www.cloudidentity.net)：某位重要的 Azure Active Directory PM 對於將身分識別管理服務的看法。  
* [Twitter 上的 Azure Active Directory](https://twitter.com/azuread)：140 個字元以內的 Azure Active Directory 公告。

## <a name="windows-server-on-premises-development"></a>Windows Server 內部部署開發
如需使用 Windows Server 和 Active Directory Federation Services (ADFS) 開發的指引，請參閱︰

* [開發人員的 AD FS 案例](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers)︰提供 AD FS 元件的概觀及其運作方式，並詳述支援的驗證/授權案例。
* [AD FS 逐步解說](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development)︰逐步解說文章的清單，提供實作相關驗證/授權流程的逐步指示。

