<properties
   pageTitle="Azure Active Directory v2.0 程式庫 | Microsoft Azure"
   description="針對 Azure Active Directory v2.0 端點，提供所有相容用戶端程式庫和伺服器中介軟體程式庫清單，以及相關文件庫/來源/範例連結。"
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/26/2016"
   ms.author="skwan;bryanla"/>


# Azure Active Directory (AD) v2.0 和驗證程式庫
Azure AD v2.0 端點支援業界標準 OAuth 2.0 和 OpenID Connect 1.0 通訊協定。Microsoft 和其他廠商提供的各種程式庫可以搭配 v2.0 端點使用。

在建置使用 v2.0 端點的應用程式時，建議您使用通訊協定網域專家遵循安全性開發生命週期 (SDL) 方法 ([ Microsoft 遵循的方法][Microsoft-SDL]) 所撰寫的程式庫。如果您決定手工撰寫通訊協定的支援，建議您遵循 SDL 並仔細觀察在各通訊協定的標準規格中找到的安全性考量。

## 程式庫類型
使用 v2.0 的程式庫有兩種︰

- **用戶端程式庫**︰用戶端程式庫使用於原生用戶端或伺服器，以取得存取權杖來呼叫資源，例如 Microsoft Graph。
- **伺服器中介軟體程式庫**︰Web 應用程式使用伺服器中介軟體程式庫來將使用者登入，而 Web API 則將這類程式庫用來驗證原生用戶端或其他伺服器所傳送的權杖。

## 程式庫支援
您可以在使用 v2.0 端點時選擇任何標準相容的文件庫，所以一定要知道哪裡可以尋求支援。程式庫程式碼中的問題和功能要求會送交程式庫擁有者。服務端通訊協定實作中的問題和功能要求會送交 Microsoft。

程式庫的支援類型有兩種︰

- **Microsoft 支援**：Microsoft 提供這些程式庫的修正程式。Microsoft 已進行這些程式庫的安全性開發生命週期審查評鑑。
- **相容**：Microsoft 已在基本案例中測試一組程式庫並確認其使用 v2.0 端點。Microsoft 不提供這些程式庫的修正程式，且尚未審查這些程式庫。問題和功能要求應重新導向至程式庫的開放原始碼專案。

如需使用 V2.0 端點的程式庫清單，請參閱下列各節。

## Microsoft 支援的用戶端程式庫
| 平台| 程式庫名稱| 下載 | 原始程式碼 | 範例 |
| :-: | :-: | :-: | :-: | :-: |
| .NET、Windows 市集、Xamarin | 適用於 .NET 的 Microsoft Authentication Library (MSAL) | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [適用於 .NET 的 MSAL (GitHub)][ClientLib-NET-Repo] | [Windows 桌面原生用戶端範例][ClientLib-NET-Sample] |
| Node.js | Microsoft Azure Active Directory Passport.js 外掛程式 | [Passport-Azure-AD (npm)][ClientLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] | 敬請期待 |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## Microsoft 支援的伺服器中介軟體程式庫
| 平台| 程式庫名稱| 下載 | 原始程式碼 | 範例 |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | 適用於 ASP.NET 的 OWIN OpenID Connect 中介軟體 | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Katana 專案 (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Web 應用程式範例][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | 適用於 ASP.NET 的 OWIN OAuth Bearer 中介軟體 | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Katana 專案 (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Web API 範例][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core | 適用於 .Net Core 的 OWIN OpenID Connect 中介軟體 | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [ASP.Net Security (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Web 應用程式範例][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core | 適用於 .Net Core 的 OWIN OAuth Bearer 中介軟體 | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [ASP.Net Security (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | 敬請期待 |
| Node.js | Microsoft Azure Active Directory Passport.js 外掛程式 | [Passport-Azure-AD (npm)][ServerLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] | [Web 應用程式範例][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .Net | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .Net (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## 相容的用戶端程式庫
| 平台| 名稱 | 測試的版本 | 原始程式碼 | 範例 |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0\.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [原生應用程式範例](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1\.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [原生應用程式範例](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | 1\.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | 敬請期待 |
| Python - Flask | [Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) | 0\.9.3 | [Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) | 敬請期待 |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | 敬請期待 |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## 相容的伺服器中介軟體程式庫 
敬請期待


## 相關內容
如需 Azure AD v2.0 端點的詳細資訊，請參閱 [Azure AD 應用程式模型 v2 概觀][AAD-App-Model-V2-Overview]。

請使用下列 Disqus 註解區段來提供意見反應，並協助我們改善及設計我們的內容。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]: [ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]: [ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]: [ClientLib-Android-Repo]:
[ClientLib-Android-Sample]: [ClientLib-Js-Lib]:
[ClientLib-Js-Repo]: [ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/zh-TW/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]: /
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]: /
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/zh-TW/documentation/articles/active-directory-v2-devquickstarts-node-web/

<!---HONumber=AcomDC_0928_2016-->