---
title: Azure Active Directory B2C | Microsoft Docs
description: 使用 Azure Active Directory 的 OpenID Connect 驗證通訊協定實作來建置 Web 應用程式。
services: active-directory-b2c
documentationcenter: ''
author: dstrockis
manager: mbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock

---
# <a name="azure-active-directory-b2c:-oauth-2.0-authorization-code-flow"></a>Azure Active Directory B2C：OAuth 2.0 授權碼流程
OAuth 2.0 授權碼授與可用於裝置上所安裝的應用程式中，以存取受保護的資源，例如 Web API。 只要使用 Azure Active Directory (Azure AD) B2C 的 OAuth 2.0 實作，您就可以在自己的行動及桌面應用程式中，新增註冊、登入及其他身分識別管理工作。 本指南與語言無關， 而是在說明如何傳送和接收 HTTP 訊息，但不使用我們的任何開放原始碼程式庫。

<!-- TODO: Need link to libraries -->

如需 OAuth 2.0 授權碼流程的說明，請參閱 [OAuth 2.0 規格的 4.1 節](http://tools.ietf.org/html/rfc6749)。 您可以在大多數的應用程式類型 (包括 [Web 應用程式](active-directory-b2c-apps.md#web-apps)和[原生安裝應用程式](active-directory-b2c-apps.md#mobile-and-native-apps)) 中，利用它來執行驗證及授權作業。 它可讓應用程式安全地取得 **access_token**，而這些權杖可用於存取[授權伺服器](active-directory-b2c-reference-protocols.md#the-basics)所保護的資源。

本指南將把重點擺在一種特別的 OAuth 2.0 授權碼流程上，也就是**公開用戶端**。 公開用戶端是指不可信任會安全地維護密碼完整性的任何用戶端應用程式。 這包括行動應用程式、桌面應用程式，以及幾乎任何在裝置上執行且需要取得 access_token 的應用程式。 如果您想要利用 Azure AD B2C 在 Web 應用程式中新增身分識別管理功能，您應該要使用 [OpenID Connect](active-directory-b2c-reference-oidc.md) ，而不是 OAuth 2.0。

Azure AD B2C 擴充標準的 OAuth 2.0 流程，功能更強大，而不僅止於簡單的驗證和授權。 它採用[**原則參數**](active-directory-b2c-reference-policies.md)，讓您能利用 OAuth 2.0 來為應用程式新增更多使用者體驗，例如註冊、登入和設定檔管理。 以下我們將示範如何利用 OAuth 2.0 和原則，在您的原生應用程式中實作上述每一種體驗。 我們也會示範如何取得用來存取 Web API 的 access_token。

下面的範例 HTTP 要求將使用我們的範例 B2C 目錄 **fabrikamb2c.onmicrosoft.com**，以及我們的範例應用程式和原則。 您可以隨意使用這些值來自行試驗要求，也可以把它們換成您自己的值。
了解如何 [取得您自己的 B2C 目錄、應用程式和原則](#use-your-own-b2c-directory)。

## <a name="1.-get-an-authorization-code"></a>1.取得授權碼
授權碼流程始於用戶端將使用者導向 `/authorize` 端點。 這是流程的互動部分，使用者將會實際地採取動作。 在這項要求中，用戶端會在 `scope` 參數中指出它需要向使用者要求的權限，以及在 `p` 參數中指出它要執行的原則。 以下提供三個範例 (插入換行以提高可讀性)，各使用不同的原則。

#### <a name="use-a-sign-in-policy"></a>使用登入原則
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>使用註冊原則
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>使用編輯設定檔原則
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| 參數 | 必要？ | 說明 |
| --- | --- | --- |
| client_id |必要 |[Azure 入口網站](https://portal.azure.com) 指派給您應用程式的應用程式識別碼。 |
| response_type |必要 |回應類型，必須針對授權碼流程來加入 `code`。 |
| redirect_uri |必要 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 它必須與您在入口網站中註冊的某個 redirect_uris 完全符合，不過它必須是 URL 編碼的。 |
| scope |必要 |範圍的空格分隔清單。 單一範圍值，向 Azure AD 指出受到要求的兩個權限。 使用用戶端識別碼做為範圍表示您的應用程式需要可針對您自己的服務或 Web API 使用的 **存取權杖** (以相同的用戶端識別碼表示)。  `offline_access` 範圍表示您的應用程式將需要 **refresh_token**，才能長久存取資源。  您也可以使用 `openid` 範圍從 Azure AD B2C 要求 **id_token**。 |
| response_mode |建議 |必須用來將所得的 authorization_code 傳回至應用程式的方法， 可以是 'query'、'form_post' 或 'fragment'。 |
| state |建議 |同樣會隨權杖回應傳回之要求中所包含的值。 它可以是您所想要內容中的字串。 隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。 驗證要求出現前，也會先使用此狀態為使用者在應用程式中的狀態資訊編碼，例如他們先前所在的網頁或正在執行的原則。 |
| p |必要 |系統將會執行的原則。 這是在您的 B2C 目錄中所建立的原則名稱。 原則名稱值的開頭必須是「b2c\_1\_」。 如需深入了解原則，請參閱 [可延伸的原則架構](active-directory-b2c-reference-policies.md)。 |
| prompt |選用 |需要的使用者互動類型。 此時唯一有效的值是 'login'，強制使用者針對該要求輸入其認證。 單一登入將沒有作用。 |

此時會要求使用者完成原則的工作流程。 視原則的定義方式，這可能會牽涉到讓使用者輸入自己的使用者名稱及密碼、以社交身分識別登入、註冊目錄，或是其他任何數目的步驟。

當使用者完成原則之後，Azure AD 就會使用您在 `response_mode` 參數中指定的方法，將回應傳回至指定的 `redirect_uri` 來給您的應用程式。 在上述各種情況下，回應完全相同，與已執行的原則無關。

使用 `response_mode=query` 的成功回應看起來類似：

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 參數 | 說明 |
| --- | --- |
| code |應用程式要求的 authorization_code。 應用程式可以使用授權碼來要求目標資源的 access_token。 Authorization_codes 的存在時間很短， 通常會在大約 10 分鐘後過期。 |
| state |如需完整說明，請參閱前一個表格。 如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該要確認要求中的狀態值，與回應中的完全相同。 |

錯誤回應也可以傳送到 `redirect_uri` ，讓應用程式能適當地處理：

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 說明 |
| --- | --- |
| 錯誤 |錯誤碼字串，可用來為發生的錯誤類型分類，以針對錯誤做出反應。 |
| error_description |特定的錯誤訊息，可協助開發人員辨識驗證錯誤的根本原因。 |
| state |如需完整說明，請參閱本節的第一個表格。 如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該要確認要求中的狀態值，與回應中的完全相同。 |

## <a name="2.-get-a-token"></a>2.取得權杖
您已經取得 authorization_code，因此可以藉由傳送 `POST` 要求給 `/token` 端點，把權杖的 `code` 兌換成所需的資源。 在 Azure AD B2C 中，您唯一可以要求權杖的資源，就是應用程式本身的後端 Web API。 用來為您自己要求權杖的慣例是使用應用程式的用戶端識別碼做為範圍：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| 參數 | 必要？ | 說明 |
| --- | --- | --- |
| p |必要 |用來取得授權碼的原則。 您無法在此要求中使用不同的原則。 請注意，您要把這個參數新增到「查詢字串」 ，而不是 POST 主體中。 |
| client_id |必要 |[Azure 入口網站](https://portal.azure.com) 指派給您應用程式的應用程式識別碼。 |
| grant_type |必要 |授與的類型，針對授權碼流程來說，必須是 `authorization_code` 。 |
| scope |建議 |範圍的空格分隔清單。 單一範圍值，向 Azure AD 指出受到要求的兩個權限。 使用用戶端識別碼做為範圍表示您的應用程式需要可針對您自己的服務或 Web API 使用的 **存取權杖** (以相同的用戶端識別碼表示)。  `offline_access` 範圍表示您的應用程式將需要 **refresh_token**，才能長久存取資源。  您也可以使用 `openid` 範圍從 Azure AD B2C 要求 **id_token**。 |
| code |必要 |您在流程的第一個階段中取得的 authorization_code。 |
| redirect_uri |必要 |應用程式的 redirect_uri，指出您在此處收到 authorization_code。 |

成功的權杖回應如下：

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 參數 | 說明 |
| --- | --- |
| not_before |權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token_type |權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| access_token |您所要求的已簽署 JSON Web Token (JWT)。 |
| scope |權杖有效的範圍，可用於快取權杖供以後使用。 |
| expires_in |權杖的有效時間長度 (以秒為單位)。 |
| refresh_token |OAuth 2.0 refresh_token。 應用程式在目前的權杖過期之後，可以使用這個權杖來取得其他權杖。 Refresh_token 的存在時間很長，且可以用來長期保留資源存取權。 如需詳細資訊，請參閱 [B2C 權杖參考](active-directory-b2c-reference-tokens.md)。 |

錯誤回應格式如下：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 說明 |
| --- | --- |
| 錯誤 |錯誤碼字串，可用來為發生的錯誤類型分類，以針對錯誤做出反應。 |
| error_description |特定的錯誤訊息，可協助開發人員辨識驗證錯誤的根本原因。 |

## <a name="3.-use-the-token"></a>3.使用權杖
您已經成功取得 `access_token`，因此您可以在對後端 Web API 發出的要求中使用該權杖，方法是在 `Authorization` 標頭中加入該權杖：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4.-refresh-the-token"></a>4.重新整理權杖
存取權杖和 ID 權杖只會短暫存在。 因此在該權杖過期之後，您必須重新整理權杖，才能繼續存取資源。 方法是向 `/token` 端點送出另一個 `POST` 要求， 但這次要提供 `refresh_token`，而不是 `code`：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 參數 | 必要？ | 說明 |
| --- | --- | --- |
| p |必要 |用來取得原始 refresh_token 的原則。 您無法在此要求中使用不同的原則。 請注意，您要把這個參數新增到「查詢字串」 ，而不是 POST 主體中。 |
| client_id |建議 |[Azure 入口網站](https://portal.azure.com) 指派給您應用程式的應用程式識別碼。 |
| grant_type |必要 |授與的類型，針對授權碼流程的這部分來說，必須是 `refresh_token` 。 |
| scope |建議 |範圍的空格分隔清單。 單一範圍值，向 Azure AD 指出受到要求的兩個權限。 使用用戶端識別碼做為範圍表示您的應用程式需要可針對您自己的服務或 Web API 使用的 **存取權杖** (以相同的用戶端識別碼表示)。  `offline_access` 範圍表示您的應用程式將需要 **refresh_token**，才能長久存取資源。  您也可以使用 `openid` 範圍從 Azure AD B2C 要求 **id_token**。 |
| redirect_uri |選用 |應用程式的 redirect_uri，指出您在此處收到 authorization_code。 |
| refresh_token |必要 |您在流程的第二個階段中取得的原始 refresh_token。 |

成功的權杖回應如下：

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 參數 | 說明 |
| --- | --- |
| not_before |權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token_type |權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| access_token |您所要求的已簽署 JSON Web Token (JWT)。 |
| scope |權杖有效的範圍，可用於快取權杖供以後使用。 |
| expires_in |權杖的有效時間長度 (以秒為單位)。 |
| refresh_token |OAuth 2.0 refresh_token。 應用程式在目前的權杖過期之後，可以使用這個權杖來取得其他權杖。 Refresh_token 的存在時間很長，且可以用來長期保留資源存取權。 如需詳細資訊，請參閱 [B2C 權杖參考](active-directory-b2c-reference-tokens.md)。 |

錯誤回應格式如下：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 說明 |
| --- | --- |
| 錯誤 |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error_description |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

## <a name="use-your-own-b2c-directory"></a>使用您自己 B2C 目錄
如果您想要親自嘗試這些要求，您必須先執行下列三個步驟，然後用您自己值來取代上面的範例值：

* [建立 B2C 目錄](active-directory-b2c-get-started.md) ，並在要求中使用您目錄的名稱。
* [建立應用程式](active-directory-b2c-app-registration.md)來取得應用程式識別碼和 redirect_uri。 您可以在應用程式中加入 **原生用戶端** 。
* [建立您的原則](active-directory-b2c-reference-policies.md) 來取得原則名稱。

<!--HONumber=Oct16_HO2-->


