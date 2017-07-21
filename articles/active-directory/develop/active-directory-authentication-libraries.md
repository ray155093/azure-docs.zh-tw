---
title: "Azure Active Directory 驗證程式庫 | Microsoft Docs"
description: "Azure AD 驗證程式庫 (ADAL) 可讓用戶端應用程式開發人員輕鬆地向雲端或內部部署 Active Directory (AD) 驗證使用者，然後取得存取權杖來保護 API 呼叫。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: bryanla
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 5f0cecf3eb26039da33260fad40cc3eaa581e7f4
ms.contentlocale: zh-tw
ms.lasthandoff: 07/14/2017

---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 驗證程式庫
Azure AD 驗證程式庫 (ADAL) 可讓用戶端應用程式開發人員輕鬆地向雲端或內部部署 Active Directory (AD) 驗證使用者，然後取得存取權杖來保護 API 呼叫。 ADAL 有許多功能可使開發人員的驗證更容易，例如非同步支援、儲存存取權杖和更新權杖的可設定權杖快取、當存取權杖到期並且更新權杖可供使用時自動更新權杖等等。 藉由處理大部分的複雜度，ADAL 可以幫助開發人員專注於他們的應用程式中的商務邏輯，並輕鬆地保護資源而不需成為安全性方面的專家。

ADAL 可用於各種平台上。

### <a name="client-libraries"></a>用戶端程式庫

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| .NET 用戶端、Windows 市集、UWP、Xamarin iOS 和 Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [傳統型應用程式](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[參考](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| .NET 用戶端、Windows 市集、Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [傳統型應用程式](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[參考](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[單一頁面應用程式](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS、macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS 應用程式](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [參考](http://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[中央儲存機制](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android 應用程式](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java Web 應用程式](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |
### <a name="server-libraries"></a>伺服器程式庫 

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| .NET |適用於 AzureAD 的 OWIN|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 應用程式](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |適用於 OpenIDConnect 的 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Web 應用程式](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web API](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |適用於 WS-同盟的 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC Web 應用程式](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |適用於 .NET 4.5 的身分識別通訊協定延伸模組 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |適用於 .NET 4.5 的 JWT 處理常式 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>案例
以下是可以使用 ADAL 進行驗證的三種常見案例。  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>向遠端資源驗證用戶端應用程式的使用者
在此案例中，開發人員會有 WPF 應用程式之類的用戶端，需要存取由 Azure AD 所保護的遠端資源，例如 Web API。 他有 Azure 訂用帳戶、知道如何叫用下游 Web API，並知道 Web API 使用的 Azure AD 租用戶。 如此一來，他可以使用 ADAL 來協助向 Azure AD 進行驗證，完全將驗證經驗委派給 ADAL 或明確地處理使用者認證。 ADAL 使得驗證使用者變得輕鬆，只要取得存取權杖並對 Azure AD 更新權杖，然後使用該存取權杖向 Web API 提出要求。

如需示範如何向 Azure AD 進行驗證的程式碼範例，請參閱 [原生用戶端 WPF 應用程式至 Web API](https://github.com/azureadsamples/nativeclient-dotnet)。

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>向遠端資源驗證伺服器應用程式
在此案例中，開發人員會有在伺服器上執行的應用程式，需要存取由 Azure AD 所保護的遠端資源，例如 Web API。 他有 Azure 訂用帳戶、知道如何叫用下游服務，並知道 Web API 使用的 Azure AD 租用戶。 如此一來，他可以透過明確地處理應用程式的認證，使用 ADAL 來協助向 Azure AD 進行驗證。 ADAL 使得從 Azure AD 擷取權杖變得輕鬆，只需使用應用程式的用戶端認證，然後使用該權杖向 Web API 提出要求。 ADAL 也會處理存取權杖的存留期的管理，方法是快取它並視需要更新。 如需示範此案例的程式碼範例，請參閱 [主控台應用程式至 Web API](https://github.com/AzureADSamples/Daemon-DotNet)。

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>代表使用者驗證伺服器應用程式以存取遠端資源
在此案例中，開發人員會有在伺服器上執行的應用程式，需要存取由 Azure AD 所保護的遠端資源，例如 Web API。 要求也必須在 Azure AD 中代表使用者提出。 他有 Azure 訂用帳戶、知道如何叫用下游 Web API，並知道服務使用的 Azure AD 租用戶。 一旦向 Web 應用程式驗證了使用者，應用程式便可以從 Azure AD 取得使用者的授權碼。 然後，Web 應用程式可以使用 ADAL 代表使用者，利用與應用程式相關聯的授權碼和用戶端認證從 Azure AD 取得存取權杖和更新權杖。 在 Web 應用程式取得存取權杖之後，它可以呼叫 Web API，直到權杖到期。 權杖到期時，Web 應用程式可以使用 ADAL 透過先前收到的更新權杖來取得新的存取權杖。

## <a name="see-also"></a>另請參閱
[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)

[Azure Active Directory 的驗證案例](active-directory-authentication-scenarios.md)

[Azure Active Directory 程式碼範例](active-directory-code-samples.md)

