---
title: "Azure Active Directory B2C：限制 | Microsoft Docs"
description: "Azure Active Directory B2C 的限制清單"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: bryanla
ms.assetid: 04ec3310-98bb-4bb1-856d-ddc66913b390
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 610b72f9f979f76d2d9c5180cb51ddae82a3e823
ms.lasthandoff: 03/23/2017


---
# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active Directory B2C：限制

Azure Active Directory (Azure AD) B2C 有幾項目前還未支援的特性與功能。 其中的諸多已知問題和限制將會在未來獲得解決，但如果您使用 Azure AD B2C 建置消費者導向的應用程式，則應該注意這些問題和限制。

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>建立 Azure AD B2C 租用戶期間的問題

如果您在[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)期間遇到問題，請參閱[建立 Azure AD 租用戶或 Azure AD B2C 租用戶 - 問題與解決方法](active-directory-b2c-support-create-directory.md)以取得指導方針。

請注意，當您刪除現有的 B2C 租用戶並使用相同的網域名稱加以重建時，會發生已知的問題。 您必須使用不同的網域名稱建立 B2C 租用戶。

## <a name="branding-issues-on-verification-email"></a>驗證電子郵件上的商標問題

預設的驗證電子郵件包含 Microsoft 商標。 未來我們會將它移除。 您目前可以使用 [公司商標功能](../active-directory/active-directory-add-company-branding.md)來移除它。

## <a name="branding-issues-on-local-account-sign-in-page-in-a-sign-in-policy"></a>登入原則中本機帳戶登入頁面上的商標問題

登入原則中的本機帳戶登入頁面只能使用[公司品牌功能](../active-directory/active-directory-add-company-branding.md)進行自訂，而不是經由[這裡](active-directory-b2c-reference-ui-customization.md)所述的頁面 UI 自訂功能。 此外，使用者名稱和密碼欄位沒有可用的標籤或預留位置。 我們建議的因應措施是，改用完全可自訂的「註冊或登入原則」。 如果您對於完全自訂登入原則中的本機帳戶登入頁面有興趣，請投票支持 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13062033-b2c-fully-customizable-sign-in-page) 上建議的功能。

## <a name="restrictions-on-applications"></a>應用程式的限制

Azure AD B2C 目前不支援下列類型的應用程式。 如需受支援應用程式類型的描述，請參閱 [Azure AD B2C：應用程式類型](active-directory-b2c-apps.md)。

### <a name="daemons--server-side-applications"></a>精靈 / 伺服器端應用程式

如果應用程式含有長時間執行的處理序或不需要使用者操作，也仍然需要方法來存取受保護的資源，例如 Web API。 這些應用程式可以透過 [OAuth 2.0 用戶端認證流程](active-directory-b2c-reference-protocols.md)，利用應用程式的身分識別 (而非取用者的委派身分識別) 來驗證及取得權杖。 Azure AD B2C 中還未提供此流程，因此，目前只有在進行互動式取用者登入流程之後，應用程式才能取得權杖。


### <a name="web-api-chains-on-behalf-of"></a>Web API 鏈結 (代理者)

許多架構中都有一個 Web API 需要呼叫另一個下游 Web API，而兩者都受 Azure AD B2C 保護。 此情況常見於有 Web API 後端的原生用戶端，而後端會再呼叫 Azure AD 圖形 API 等 Microsoft 線上服務。

使用 OAuth 2.0 Jwt 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為「代理者流程」。 不過，Azure AD B2C 目前未實作代理者流程。

## <a name="restrictions-on-reply-urls"></a>回覆 URL 的限制

目前，已在 Azure AD B2C 中註冊的應用程式僅限使用一組受限的回覆 URL 值。 Web 應用程式和服務的回覆 URL 必須以配置 `https` 開頭，而且所有回覆 URL 值必須共用單一 DNS 網域。 例如，您不能註冊具有下列其中一個直接 URL 的 Web 應用程式︰

`https://login-east.contoso.com`
`https://login-west.contoso.com`

註冊系統會將現有回覆 URL 的整個 DNS 名稱與您要新增之回覆 URL 的 DNS 名稱做比較。 如果下列任一條件成立，新增 DNS 名稱的要求就會失敗︰

* 新回覆 URL 的整個 DNS 名稱與現有回覆 URL 的 DNS 名稱不相符。
* 新回覆 URL 的整個 DNS 名稱不是現有回覆 URL 的子網域。

例如，如果應用程式具有下列回覆 URL：

`https://login.contoso.com`

您可以在其中新增，就像這樣：

`https://login.contoso.com/new`

在此情況下，DNS 名稱會完全相符。 或者，您可以這樣做：

`https://new.login.contoso.com`

在此情況下，您參照的是 login.contoso.com 的 DNS 子網域。 如果您想要有一個以 login-east.contoso.com 和 login-west.contoso.com 作為回覆 URL 的應用程式，就必須依下列順序新增這些回覆 URL︰

`https://contoso.com`
`https://login-east.contoso.com`
`https://login-west.contoso.com`

您可以新增後面兩個，因為它們是第一個回覆 URL (contoso.com) 的子網域。 即將推出的版本將會移除這項限制。

若要了解如何在 Azure AD B2C 中註冊應用程式，請參閱[如何使用 Azure Active Directory B2C 來註冊您的應用程式](active-directory-b2c-app-registration.md)。

## <a name="restriction-on-libraries-and-sdks"></a>程式庫與 SDK 的限制

目前能讓 Azure AD B2C 運作的 Microsoft 支援程式庫集合很有限。 我們支援 .NET 型 Web 應用程式和服務，以及 NodeJS Web 應用程式和服務。  我們也有稱為 MSAL 的預覽 .NET 用戶端程式庫，其可在 Windows 和其他 .NET 應用程式中與 Azure AD B2C 搭配使用。

我們目前並未讓程式庫支援任何其他語言或平台，包括 iOS 與 Android。  如果您想要在上面未提到的不同平台上建置，建議您使用開放原始碼 SDK，如有需要請參閱 [OAuth 2.0 和 OpenID Connect 通訊協定參考](active-directory-b2c-reference-protocols.md) 。  Azure AD B2C 會實作 OAuth 和 OpenID Connect，因此能夠使用一般的 OAuth 或 OpenID Connect 程式庫來進行整合。

我們的 iOS 和 Android 快速入門教學課程所使用的開放原始碼程式庫已完成 Azure AD B2C 相容性測試。  您可以在 [開始使用](active-directory-b2c-overview.md#get-started) 一節找到我們所有的快速入門教學課程。

## <a name="restriction-on-protocols"></a>通訊協定的限制

Azure AD B2C 支援 OpenID Connect 和 OAuth 2.0。 不過，並非每個通訊協定的所有特性與功能都已實作。 若要進一步了解 Azure AD B2C 所支援的通訊協定功能的範圍，請參閱 [OpenID Connect 和 OAuth 2.0 通訊協定參考](active-directory-b2c-reference-protocols.md)。 不提供 SAML 和 WS-Fed 通訊協定支援。

## <a name="restriction-on-tokens"></a>權杖的限制

許多由 Azure AD B2C 所簽發的權杖都會實作為 JSON Web 權杖或 JWT。 不過，並非 JWT 中包含的所有資訊 (又稱為「宣告」) 都相當完備，不然就是有所疏漏。 "preferred_username" 宣告就是一個例子。  宣告的值、格式或意義會隨著時間而改變，但現有原則的權杖則不會受到影響，因此您可以依賴它們在生產應用程式中的值。  當值變更時，我們會提供機會讓您對每個原則設定這些變更。  若要進一步了解目前  Azure AD B2C 服務所發出的權杖，請參閱 [權杖參考](active-directory-b2c-reference-tokens.md)。

## <a name="restriction-on-nested-groups"></a>巢狀群組的限制

Azure AD B2C 租用戶中不支援巢狀群組成員資格。 我們不打算新增這項功能。

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Azure AD 圖形 API 上的差異查詢功能限制

Azure AD B2C 租用戶不支援 [Azure AD 圖形 API 上的差異查詢功能](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) 。 這是我們的長期發展規劃。

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Azure 傳統入口網站上的使用者管理問題

在 Azure 入口網站上可存取 B2C 功能。 不過，您可以使用 Azure 傳統入口網站來存取其他的租用戶功能，包括使用者管理。 目前，Azure 傳統入口網站上的使用者管理 ([使用者]  索引標籤) 有幾個已知問題：

* 以本機帳戶使用者而言 (亦即，以電子郵件地址和密碼或使用者名稱和密碼來註冊的取用者)，[使用者名稱]  欄位未對應至註冊期間使用的登入識別碼 (電子郵件地址或使用者名稱)。 這是因為 Azure 傳統入口網站上顯示的欄位，實際上是使用者主體名稱 (UPN)，而這在 B2C 案例中沒有用到。 若要檢視本機帳戶的登入識別項，請在 [Graph Explorer](https://graphexplorer.cloudapp.net/)中尋找使用者物件。 您將會遇到與社交帳戶使用者 (亦即，以 Facebook、Google+ 等註冊的取用者) 同樣的問題，但在此情況下，沒所謂的登入識別項。

    ![本機帳戶 - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)
* 以本機帳戶使用者而言，您將無法在 [設定檔]  索引標籤中編輯任何欄位和儲存變更。

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>在 Azure 傳統入口網站上的系統管理員起始密碼重設問題

如果您在 Azure 傳統入口網站上重設本機帳戶取用者的密碼 ([使用者] 索引標籤上的 [重設密碼] 命令)，該取用者下次登入時將無法變更密碼；如果您使用「註冊或登入」原則，取用者還會被鎖定不得使用您的應用程式。 解決方法是使用 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) 重設取用者密碼 (不含密碼到期)，或使用「登入」原則，而不是「註冊或登入」原則。

## <a name="issues-with-creating-a-custom-attribute"></a>建立自訂屬性時的問題

[在 Azure 入口網站上新增的自訂屬性](active-directory-b2c-reference-custom-attr.md) 不會立即在 B2C 租用戶中建立。 您必須至少在一個原則中使用自訂屬性，它才會在 B2C 租用戶中建立，也才可透過圖形 API 使用。

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>在 Azure 傳統入口網站上驗證網域的問題

您目前無法在 [Azure 傳統入口網站](https://manage.windowsazure.com/)上順利驗證網域。

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Safari 瀏覽器上的使用 MFA 原則來登入的問題

在 Safari 瀏覽器上，登入原則的要求 (在開啟 MFA 時) 偶爾會失敗，並出現 HTTP 400 (不正確的要求) 錯誤。 這是因為 Safari 的低 Cookie 大小限制所致。 此問題有許多因應措施︰

* 使用「註冊或登入原則」而不是「登入原則」。
* 減少原則中所要求的 **應用程式宣告** 數目。

## <a name="issues-with-windows-desktop-wpf-apps-using-azure-ad-b2c"></a>使用 Azure AD B2C 之 Windows 傳統型 WPF 應用程式的問題

Windows 傳統型 WPF 應用程式傳送給 Azure AD B2C 的要求有時會失敗並傳回下列錯誤訊息：「瀏覽器型驗證對話方塊無法完成。 原因: 通訊協定不明，且沒有沒有輸入相符的可插入式通訊協定。」。

這是因為 Azure AD B2C 所提供的授權碼大小所致；此大小與權杖中所要求的宣告數目相互關聯。 此問題的因應方式是縮減權杖中所要求的宣告數目，然後個別查詢「圖形 API」來取得其他宣告。

