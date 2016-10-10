<properties
	pageTitle="v2.0 端點限制 | Microsoft Azure"
	description="Azure AD v2.0 端點的限制清單。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="dastrock"/>

# 我應該使用 v2.0 端點嗎？

在建立與 Azure Active Directory 整合的應用程式時，您必須判斷 v2.0 端點和驗證通訊協定是否符合您的需求。仍然完整支援原始的 Azure AD 端點，而且在某些方面比 v2.0 的功能更豐富。不過，v2.0 端點為開發人員[帶來極大的好處](active-directory-v2-compare.md)， 可能吸引您採用新的程式設計模型。

在這個時間點，我們對於使用 v2.0 端點的建議如下︰

- 如果您想要在應用程式中支援個人 Microsoft 帳戶，您應該使用 v2.0 端點。但請務必了解本文所列的限制，尤其是特別屬於工作和學校帳戶的限制。
- 如果您的應用程式只需支援工作和學校帳戶，您應該使用[原始的 Azure AD 端點](active-directory-developers-guide.md)。

隨著時間經過，v2.0 端點會成長到排除此處所列的限制，屆時您只需要使用 v2.0 端點即可。在此同時，本文旨在協助您判斷 v2.0 端點是否適合您。我們將持續更新本文，以反映 v2.0 端點目前的狀態，請記得回來重新評估您對於 v2.0 功能的需求。

如果您有一個與 Azure AD 搭配的現有應用程式未使用 v2.0 端點，則不需要從頭開始。未來，我們設法讓您的現有 Azure AD 應用程式可以使用 v2.0 端點。

## 應用程式的限制
v2.0 端點目前不支援下列類型的應用程式。如需受支援的應用程式類型描述，請參閱[這篇文章](active-directory-v2-flows.md)。

##### 獨立的 Web API
在 v2.0 端點中，您能夠[建置使用 OAuth 2.0 保護的 Web API](active-directory-v2-flows.md#web-apis)。不過，該 Web API 只能從共用相同應用程式識別碼的應用程式接收權杖。不支援建置 Web API 供具有不同應用程式識別碼的用戶端存取。該用戶端無法要求或取得您的 Web API 的權限。

若要了解如何建置 Web API 以接受具有相同應用程式識別碼的用戶端傳回的權杖，請參閱[開始使用](active-directory-appmodel-v2-overview.md#getting-started)中的 v2.0 端點 Web API 範例。

##### Web API 代理者流程
許多架構包含需要呼叫另一個下游 Web API 的 Web API，而這兩者都受到 v2.0 端點的保護。此情況常見於有 Web API 後端的原生用戶端，而後端會再呼叫 Microsoft 線上服務或另一個支援 Azure AD 的自訂建置 Web API。

使用 OAuth 2.0 Jwt 持有人認證授與可支援此案例，亦稱為「代理者流程」。不過，v2.0 端點目前不支援「代理者」流程。若要查看此流程在正式運作的 Azure AD 服務中如何運作，請參閱 [GitHub 上的代理者程式碼範例](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)。

## 應用程式註冊的限制
目前，所有想要與 v2.0 端點整合的應用程式，都必須在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 建立新的應用程式註冊。任何現有的 Azure AD 或 Microsoft 帳戶應用程式都與 v2.0 端點不相容，除新的應用程式註冊入口網站以外，在任何入口網站中註冊的應用程式也不相容。我們打算設法將現有的應用程式當做 v2.0 應用程式來使用。但目前還沒有將應用程式移轉至 v2.0 端點的途徑。

同樣地，在新的應用程式註冊入口網站中註冊的應用程式，將無法配合原始 Azure AD 驗證端點來運作。不過，您可以使用在應用程式註冊入口網站中建立的應用程式，成功地與 Microsoft 帳戶驗證端點 `https://login.live.com` 整合。

此外，在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 建立的應用程式註冊必須注意下列幾點︰

- 不支援**首頁**屬性，也稱為**登入 URL**。沒有首頁，這些應用程式就不會顯示在 Office MyApps 面板中。
- 目前每個應用程式識別碼只允許兩個應用程式密碼。
- 應用程式註冊只能由單一開發人員帳戶檢視和管理。不能在多位開發人員之間共用。
- redirect\_uri 允許的格式有幾項限制。請參閱下一節以瞭解更多詳細資訊。

## 重新導向 URI 的限制
在新的應用程式註冊入口網站中註冊的應用程式，目前僅限一組受限的 redirect\_uri 值。Web 應用程式和服務的 redirect\_uri 必須以配置 `https` 開頭，而所有 redirect\_uri 值必須共用單一 DNS 網域。例如，不可以登錄具有具有 redirect\_uri 的 Web 應用程式︰

`https://login-east.contoso.com` `https://login-west.contoso.com`

註冊系統會比較現有 redirect\_uri 的完整 DNS 名稱與您要新增的 redirect\_uri 的 DNS 名稱。如果符合下列任一條件，則新增 DNS 名稱的要求會失敗︰

- 如果新 redirect\_uri 的完整 DNS 名稱不符合現有 redirect\_uri 的 DNS 名稱
- 如果新 redirect\_uri 的完整 DNS 名稱不是現有 redirect\_uri 的子網域

例如，如果應用程式目前有 redirect\_uri：

`https://login.contoso.com`

然後可以新增︰

`https://login.contoso.com/new`

這完全符合 DNS 名稱，或︰

`https://new.login.contoso.com`

這是 login.contoso.com 的 DNS 子網域。如果您希望應用程式以 login-east.contoso.com 和 login-west.contoso.com 做為 redirect\_uri，您必須依序新增下列 redirect\_uri︰

`https://contoso.com` `https://login-east.contoso.com` `https://login-west.contoso.com`

可以新增後面兩個，因為它們是第一個 redirect\_uri (contoso.com) 的子網域。即將推出的版本將會移除這項限制。

若要了解如何在新的應用程式註冊入口網站中註冊應用程式，請參閱[這篇文章](active-directory-v2-app-registration.md)。

## 服務和 API 的限制
v2.0 端點目前支援登入所有在新的應用程式註冊入口網站註冊的應用程式，如[支援的驗證流程](active-directory-v2-flows.md)清單所列。不過，這些應用程式將只能取得 OAuth 2.0 存取權杖，獲得非常有限的資源。v2.0 端點只會為下列項目發行 access\_token：

- 要求權杖的應用程式。如果邏輯應用程式是由數個不同的元件或層級組成，應用程式就可以為自己取得 access\_token。若要查看此案例的執行狀況，請參閱[使用者入門](active-directory-appmodel-v2-overview.md#getting-started)教學課程。
- Outlook 郵件、行事曆和連絡人 REST API 全都位於 https://outlook.office.com。若要了解如何撰寫存取這些 API 的應用程式，請參閱 [Office 使用者入門](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)教學課程。
- Microsoft Graph API。若要了解 Microsoft Graph 和可用的所有資料，請造訪 [https://graph.microsoft.io](https://graph.microsoft.io)。

目前不支援其他服務。未來將增加更多 Microsoft Online 服務，並支援您自己的自訂建置 Web API 和服務。

## 程式庫與 SDK 的限制
此時，v2.0 端點的程式庫支援相當有限。如果您想要在實際執行應用程式中使用 v2.0 端點，您有下列選項：

- 如果您要建置 Web 應用程式，您可以安心地使用我們正式運作的伺服器端中介軟體來執行登入和權杖驗證。其中包括適用於 ASP.NET 的 OWIN Open ID Connect 中介軟體和我們的 NodeJS Passport 外掛程式。[開始使用](active-directory-appmodel-v2-overview.md#getting-started)一節也提供使用中介軟體的範例程式碼。
- 至於其他平台及原生與行動應用程式，您也可以直接在應用程式程式碼中傳送和接收通訊協定訊息，以便與 v2.0 端點整合。v2.0 OpenID Connect 和 OAuth 通訊協定[有明確的說明文件](active-directory-v2-protocols.md)，可協助您執行這種整合。
- 最後，您可以使用開放原始碼 Open ID Connect 和 OAuth 程式庫來與 v2.0 端點整合。v2.0 通訊協定與許多開放原始碼通訊協定程式庫都應該相容，而不需要重大變更。這類程式庫的可用性依語言和平台而不同，[Open ID Connect](http://openid.net/connect/) 和 [OAuth 2.0](http://oauth.net/2/) 網站維護一份常用的實作清單。如需更多詳細資訊，以及已利用 v2.0 端點測試的開放原始碼用戶端程式庫和範例清單，請參閱 [Azure Active Directory (AD) v2.0 和驗證程式庫](active-directory-v2-libraries.md)。

我們也已發行 [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) 的初始預覽版本 (僅限 .NET)。歡迎您在 .NET 用戶端和伺服器應用程式中試用此程式庫，但這是預覽程式庫，所以還不會提供 GA 品質的支援。

## 通訊協定的限制
v2.0 端點僅支援 Open ID Connect 和 OAuth 2.0。不過，並非每個通訊協定的所有特性與功能都已納入到 v2.0 端點，包括：

- OpenID Connect `end_session_endpoint`，可讓應用程式結束使用者的 v2.0 端點工作階段。
- v2.0 端點所發出的 id\_tokens 只包含使用者的成對識別碼。這表示兩個不同的應用程式會針對相同使用者收到不同的識別碼。請注意，藉由查詢 Microsoft Graph `/me` 端點，您就能夠取得可用於各應用程式的使用者相關識別碼。
- 即使您向使用者取得檢視其電子郵件的權限，v2.0 端點所發出的 id\_tokens 目前並不包含使用者的 `email` 宣告。
- OpenID Connect 使用者資訊端點。目前未在 v2.0 端點上實作使用者資訊端點。不過，從 Microsoft Graph `/me` 端點可取得您可能會在此端點收到的所有使用者設定檔資料。
- 角色和群組宣告。此時，v2.0 端點不支援在 id\_tokens 中發出角色或群組宣告。

若要進一步了解 v2.0 端點支援的通訊協定功能範圍，請參閱 [OpenID Connect 與 OAuth 2.0 通訊協定參照](active-directory-v2-protocols.md)。

## 工作和學校帳戶的限制
v2.0 端點尚未支援 Microsoft 組織/企業使用者特有的一些功能。

##### 裝置型條件式存取、原生和行動應用程式以及 Microsoft Graph
V2.0 端點尚未支援行動和原生應用程式的裝置驗證，例如在 iOS 或 Android 上執行的原生應用程式。這可能會封鎖原生應用程式針對特定組織呼叫 Microsoft Graph。系統管理員在應用程式上設定裝置型條件式存取原則時，需要裝置驗證。對於 v2.0 端點，最可能的裝置型條件式存取案例是系統管理員在 Microsoft Graph 中設定資源 (例如 Outlook API) 的原則。如果系統管理員設定此原則，且原生應用程式要求 Microsoft Graph 的權杖，則要求最終會失敗，因為尚未支援裝置驗證。但是，設定裝置型原則後，便會支援要求 Microsoft Graph 權杖的 Web 應用程式。在 Web 應用程式案例中，會透過使用者的網頁瀏覽器執行裝置驗證。

身為開發人員，您最可能無法控制何時設定 Microsoft Graph 資源原則，或甚至察覺何時發生此狀況。如果您要為公司和學校使用者建置應用程式，您應該使用[原始的 Azure AD 端點](active-directory-developers-guide.md)，直到 v2.0 端點支援裝置驗證為止。如需有關裝置型條件式存取的詳細資訊，請查看[這篇文章](active-directory-conditional-access.md#device-based-conditional-access)。

##### 同盟租用戶的 Windows 整合式驗證
如果您已在 Windows 應用程式中使用 ADAL (以及原始的 Azure AD 端點)，您可能已利用所謂的 SAML 判斷提示授與。此授與可讓同盟 Azure AD 租用戶的使用者使用其內部部署 Active Directory 執行個體以無訊息模式進行驗證，而不需要輸入其認證。v2.0 端點目前不支援 SAML 判斷提示授與。

<!---HONumber=AcomDC_0928_2016-->