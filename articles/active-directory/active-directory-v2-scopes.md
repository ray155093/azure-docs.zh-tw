---
title: Azure AD v2.0 範圍、權限和同意 | Microsoft Docs
description: Azure AD v2.0 端點中的授權說明，包括範圍、權限及同意。
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: dastrock

---
# <a name="scopes,-permissions,-&-consent-in-the-v2.0-endpoint"></a>v2.0 端點的範圍、權限和同意
與 Azure AD 整合的應用程式會遵循可讓使用者控制應用程式如何存取其資料的特定授權模型。  這個授權模型的 v2.0 實作已更新，並變更應用程式必須與 Azure AD 互動的方式。  本主題涵蓋此授權模型的基本概念，包括範圍、權限及同意。

> [!NOTE]
> v2.0 端點並非支援每個 Azure Active Directory 案例和功能。  若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

## <a name="scopes-&-permissions"></a>範圍和權限
Azure AD 會實作 [OAuth 2.0](active-directory-v2-protocols.md) 授權通訊協定，此方法可讓協力廠商應用程式代表使用者存取 Web 主控的資源。  任何與 Azure AD 整合的 Web 主控資源都會有資源識別碼或 **應用程式識別碼 URI**。  例如，Microsoft 的 Web 主控資源包括：

* Office 365 整合郵件 API： `https://outlook.office.com`
* Azure AD Graph API： `https://graph.windows.net`
* Microsoft Graph： `https://graph.microsoft.com`

這也適用於已與 Azure AD 整合的任何協力廠商資源。  這些資源也可以定義一組可用來將該資源的功能分割成較小區塊的權限。  例如，Microsoft Graph 已定義數個權限：

* 讀取使用者的行事曆
* 寫入使用者的行事曆
* 以使用者身分傳送郵件
* [+ 更多功能](https://graph.microsoft.io)

藉由定義這些權限，資源可以更細微地掌控其資料及如何對外界公開資料的方式。  協力廠商應用程式接著可以向使用者要求這些權限，而使用者必須先核准權限，app 才可以代表他們執行動作。  將資源的功能切割成較小的權限集，即可將協力廠商應用程式建置為只要求他們所需的特定權限，以便執行其職責。  它也可讓使用者完全知道應用程式將如何使用其資料，如此他們才會對應用程式的行為不受惡意攻擊影響更具信心

在 Azure AD 和 OAuth 中，這些權限也稱為 **範圍**。  您也可能會看到它們稱為 **oAuth2Permissions**。  在 Azure AD 中範圍會以字串值表示。  繼續討論 Microsoft Graph 範例，每個權限的範圍值如下：

* 讀取使用者的行事曆： `Calendar.Read`
* 寫入使用者的行事曆： `Mail.ReadWrite`
* 以使用者身分傳送郵件： `Mail.Send`

如下所述，在對 v2.0 端點的要求中指定範圍，應用程式即可要求這些權限。

## <a name="openid-connect-scopes"></a>OpenId Connect 範圍
OpenID Connect 的 v2.0 實作有一些定義妥善但不會套用至任何特定資源的範圍 - `openid`、`email`、`profile` 和 `offline_access`。

#### <a name="openid"></a>OpenId
如果應用程式使用 [OpenID Connect`openid` 執行登入，則必須要求 ](active-directory-v2-protocols.md#openid-connect-sign-in-flow) 範圍。  `openid` 範圍會在公司帳戶同意畫面上顯示為「讓您登入」權限，而在個人 Microsoft 帳戶同意畫面上顯示為「檢視您的設定檔並使用您的 Microsoft 帳戶連接到應用程式和服務」權限。  此權限可讓應用程式以 `sub` 宣告的形式接收使用者的唯一識別碼。  它也會提供使用者資訊端點的應用程式存取權。  `openid` 範圍也可用於 v2.0 權杖端點來取得 id_token，而該權杖可用來保護應用程式的不同元件之間的 HTTP 呼叫。

#### <a name="email"></a>電子郵件
`email` 範圍可以連同 `openid` 範圍和任何其他範圍一起納入。  它會以 `email` 宣告的形式提供使用者主要電子郵件地址的應用程式存取權。  如果電子郵件地址與使用者帳戶相關聯 (情況並非永遠如此)，則 `email` 宣告只會包含在權杖中。  如果使用 `email` 範圍，您的應用程式應該準備好要處理 `email` 宣告不存在於權杖中的情況。

#### <a name="profile"></a>設定檔
`profile` 範圍可以連同 `openid` 範圍和任何其他範圍一起納入。  它會提供大量使用者資訊的應用程式存取權。  這包括但不限於使用者的名字、姓氏、慣用使用者名稱、物件識別碼等等。  如需指定使用者的可用 id_token 中可用的設定檔宣告完整清單，請參閱 [v2.0 權杖參考](active-directory-v2-tokens.md)。

#### <a name="offline_access"></a>Offline_access
[`offline_access` 範圍](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) 可讓您的應用程式代表使用者存取資源的期間延長。  在公司帳戶同意畫面上，此範圍會顯示為「隨時存取您的資料」權限。  在個人 Microsoft 帳戶同意畫面上，則會顯示為「隨時存取您的資訊」權限。  當使用者核准 `offline_access` 範圍時，您的應用程式將會啟用以接收來自 v2.0 權杖端點的重新整理權杖。  重新整理權杖屬於長效權杖，可讓您的應用程式在舊的存取權杖過期時取得新的存取權杖。

如果您的應用程式未要求 `offline_access` 範圍，則不會收到 refresh_token。  這表示當您在 [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md#oauth2-authorization-code-flow)中兌換 authorization_code 時，您只會從 `/token` 端點接收 access_token。  該 access_token 會短時間維持有效 (通常是一小時)，但最後終將過期。  屆時，您的應用程式必須將使用者重新導向回到 `/authorize` 端點以擷取新的 authorization_code。  在此重新導向期間，視應用程式的類型而定，使用者或許不需要再次輸入其認證或重新同意權限。

如需如何取得及使用重新整理權杖的詳細資訊，請參閱 [v2.0 通訊協定參考](active-directory-v2-protocols.md)。

## <a name="requesting-individual-user-consent"></a>要求個別使用者同意
在 [OpenID Connect 或 OAuth 2.0](active-directory-v2-protocols.md) 授權要求中，應用程式可以使用 `scope` 查詢參數來要求它所需的權限。  例如，當使用者登入應用程式時，應用程式會傳送如下所示的要求 (包含分行符號以便讀取)：

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` 參數是應用程式所要求的範圍清單 (以空格分隔)。  將範圍值附加至資源的識別項 (應用程式識別碼 URI) 可指出每個個別範圍。  上述要求指出應用程式需要讀取使用者的行事曆，以及以使用者身分傳送郵件的權限。

在使用者輸入其認證之後，v2.0 端點會檢查是否有相符的 **使用者同意**記錄。  如果使用者未曾同意所要求權限的任何一項，v2.0 端點會要求使用者授與要求的權限。  

![公司帳戶同意螢幕擷取畫面](../media/active-directory-v2-flows/work_account_consent.png)

當使用者核准權限時，就會記錄同意，使用者在後續登入時就不需要重新同意。

## <a name="requesting-consent-for-an-entire-tenant"></a>要求整個租用戶的同意
當組織想購買應用程式的授權或訂用帳戶時，他們通常希望為其員工進行完整佈建。  在此過程中，公司系統管理員可以代表任何員工授與該應用程式的同意。  藉由授與對整個租用戶的同意，該組織的員工不會看到該應用程式的同意畫面。

若要要求租用戶中所有使用者的同意，您的應用程式可以使用 **系統管理員同意端點**，如以下所述。

## <a name="admin-restricted-scopes"></a>受系統管理員限制的範圍
Microsoft 生態系統中的某些高特權權限可以標示為 **受系統管理員限制**。  這類範圍的範例包括︰

* 讀取組織的目錄資料︰ `Directory.Read`
* 將資料寫入至組織的目錄︰ `Directory.ReadWrite`
* 讀取組織目錄中的安全性群組︰ `Groups.Read.All`

雖然消費者使用者可以授與這類資料的應用程式存取權，但組織使用者會受到限制而無法授與相同公司機密資料集的存取權。  如果您的應用程式向組織使用者要求存取其中一個權限，則使用者會收到一則錯誤訊息，指出他們未經授權同意您的應用程式權限。

如果您的應用程式需要存取組織的這些受系統管理員限制範圍，您應該也使用 **系統管理員同意端點**直接向公司系統管理員要求權限，如下所述。

當系統管理員透過系統管理員同意端點授與這些權限時，將會替租用戶中的所有使用者授與同意 (如上所述)。

## <a name="using-the-admin-consent-endpoint"></a>使用系統管理員同意端點
遵循下列步驟，您的應用程式就能夠蒐集租用戶中所有使用者的權限，包括受系統管理員限制的範圍。  若要查看實作下述步驟的程式碼範例，請參閱 [系統管理員限制範圍範例](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)。

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>在應用程式註冊入口網站中要求權限
* 在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 中瀏覽至您的應用程式，或[建立應用程式](active-directory-v2-app-registration.md) (如果尚未這麼做)。
* 找出 [Microsoft Graph 權限]  區段並新增您的應用程式所需的權限。
* 務必 [儲存]  應用程式註冊

#### <a name="recommended:-sign-the-user-into-your-app"></a>建議︰讓使用者登入您的應用程式
在建置使用系統管理員同意端點的應用程式時，應用程式通常需要一個頁面/檢視，讓系統管理員核准應用程式的權限。  此頁面可以是應用程式註冊流程的一部分、應用程式設定的一部分，或專用「連接」流程的一部分。  在許多情況下，應用程式只在使用者利用工作或學校 Microsoft 帳戶登入之後顯示此「連接」檢視是很合理的。

將使用者登入應用程式，可讓您先識別系統管理員所屬的組織，再要求他們核准所需的權限。  雖然不是絕對必要，但這麼做可協助您為您組織的使用者建立更直覺式的經驗。  若要讓使用者登入，請遵循我們的 [v2.0 通訊協定教學課程](active-directory-v2-protocols.md)。

#### <a name="request-the-permissions-from-a-directory-admin"></a>向目錄管理員要求權限
當您準備向公司的系統管理員要求權限時，您可以將使用者重新導向至 v2.0 **系統管理員同意端點**。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 參數 |  | 說明 |
| --- | --- | --- |
| tenant |必要 |您想要要求權限的目錄租用戶。  可以 guid 或易記名稱格式提供。 |
| client_id |必要 |註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 指派給應用程式的應用程式識別碼。 |
| redirect_uri |必要 |您想要傳送回應的 redirect_uri，以便您的應用程式處理。  其必須完全符合您在入口網站中註冊的其中一個 redirect_uris。 |
| state |建議使用 |同樣會隨權杖回應傳回之要求中所包含的值。  其可以是您想要之任何內容的字串。  此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |

此時，Azure AD 會強制只有租用戶管理員可以登入來完成要求。  系統會要求系統管理員核准您在註冊入口網站中針對您的應用程式要求的所有權限。 

##### <a name="successful-response"></a>成功回應
如果系統管理員針對您的應用程式核准權限，則成功的回應如下︰

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 參數 | 說明 |
| --- | --- | --- |
| tenant |將要求的權限授與應用程式的目錄租用戶 (採用 guid 格式)。 |
| state |同樣會隨權杖回應傳回之要求中所包含的值。  其可以是您想要之任何內容的字串。  此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| admin_consent |將設定為 `True`。 |

##### <a name="error-response"></a>錯誤回應
如果系統管理員未針對您的應用程式核准權限，失敗的回應如下︰

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 參數 | 說明 |
| --- | --- | --- |
| 錯誤 |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error_description |可協助開發人員識別錯誤根本原因的特定錯誤訊息。 |

從系統管理員同意端點收到成功回應後，您的應用程式便已取得它所要求的權限。  您現在可以繼續要求所需資源的權杖，如下所述。

## <a name="using-permissions"></a>使用權限
在使用者同意您的應用程式的權限之後，您的應用程式即可取得存取權杖，而這些權杖代表您的應用程式存取資源的權限。  指定的存取權杖只能用於單一資源，但其內部編碼會是您的應用程式已獲得該資源的特有權限。  若要取得存取權杖，您的應用程式可以對 v2.0 權杖端點提出要求：

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

然後，結果產生的存取權杖可使用於資源的 HTTP 要求 - 它會可靠地指出您的應用程式具有適當權限可執行指定工作的資源。  

如需 OAuth 2.0 通訊協定以及如何取得存取權杖的詳細資訊，請參閱 [v2.0 端點通訊協定參考](active-directory-v2-protocols.md)。

<!--HONumber=Oct16_HO2-->


