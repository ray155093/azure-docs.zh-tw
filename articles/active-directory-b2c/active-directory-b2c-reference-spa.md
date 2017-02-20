---
title: Azure Active Directory B2C | Microsoft Docs
description: "如何使用隱含流程建置單一頁面應用程式。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 96c9425d8f58d3b617330615573a479429f12b11
ms.openlocfilehash: 44a7cf18afdf6a523c5f7fb03ee49f72cdd22564


---
# <a name="azure-active-directory-b2c-sign-in-for-single-page-apps-using-auth-20-implicit-flow"></a>Azure Active Directory B2C：使用 Auth 2.0 隱含流程的單一頁面應用程式登入
許多新式應用程式都有一個單頁應用程式前端，主要是以 JavaScript 撰寫。 通常是使用 AngularJS、Ember.js、Durandal.js 等架構來撰寫它們。 主要在瀏覽器上執行的單一頁面和其他 JavaScript 應用程式，在驗證時會面臨一些有趣的挑戰：

* 這些應用程式的安全性特性與傳統伺服器型 Web 應用程式大不相同。
* 許多授權伺服器與身分識別提供者不支援 CORS 要求。
* 重新導向離開應用程式的完整網頁瀏覽器變得對使用者經驗特別有侵入性。

為支援這些應用程式，Azure AD B2C 使用 OAuth 2.0 隱含流程。  如需 OAuth 2.0 授權隱含授權流程的說明，請參閱 [OAuth 2.0 規格的 4.2 節](http://tools.ietf.org/html/rfc6749) 。  在此流程中，應用程式會直接從 Azure AD 授權端點接收權杖，而不需要執行任何伺服器對伺服器交換。 所有驗證邏輯和工作階段處理都完全在 JavaScript 用戶端中進行，而不需要執行額外的頁面重新導向。

Azure AD B2C 擴充標準的 OAuth 2.0 隱含流程，功能更強大，而不僅止於簡單的驗證與授權。 它採用[**原則參數**](active-directory-b2c-reference-policies.md)，讓您能利用 OAuth 2.0 來為應用程式新增更多使用者體驗，例如註冊、登入和設定檔管理。 我們將在這裡說明如何使用隱含流程與 Azure AD 在您的單一頁面應用程式中實作這些體驗。  您也可以查看我們的 [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) 或 [.NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) 範例以開始進行。

下面的範例 HTTP 要求使用我們的範例 B2C 目錄 **fabrikamb2c.onmicrosoft.com**，以及我們的範例應用程式和原則。 您可以隨意使用這些值來自行試驗要求，也可以把它們換成您自己的值。
了解如何 [取得您自己的 B2C 目錄、應用程式和原則](#use-your-own-b2c-tenant)。

## <a name="protocol-diagram"></a>通訊協定圖表
整個隱含登入流程看起來類似下列圖表 - 以下將詳細說明每個步驟。

![OpenId Connect 區隔線](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>傳送驗證要求
當 Web 應用程式需要驗證使用者並執行原則時，它會將使用者導向至 `/authorize` 端點。 這是流程中的互動式部分，能讓使用者根據原則來實際採取動作，並從 Azure AD 端點取得 `id_token`。

在這項要求中，用戶端會在 `scope` 參數中指出它需要向使用者要求的權限，以及在 `p` 參數中指出它要執行的原則。 以下提供三個範例 (插入換行以提高可讀性)，各使用不同的原則。 為了瞭解每個要求的運作方式，請試著將要求貼到瀏覽器來執行。

#### <a name="use-a-sign-in-policy"></a>使用登入原則
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>使用註冊原則
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>使用編輯設定檔原則
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 參數 |  | 說明 |
| --- | --- | --- |
| client_id |必要 |[Azure 入口網站](https://portal.azure.com/) 指派給您應用程式的應用程式識別碼。 |
| response_type |必要 |必須包含 OpenID Connect 登入的 `id_token` 。  它也可能包含 response_type `token`。 這裡使用 `token` ，讓您的應用程式能夠立即從授權端點接收存取權杖，而不需要向授權端點進行第二次要求。  如果您使用 `token` response_type，`scope` 參數必須包含範圍，以指出要對哪個資源發出權杖。 |
| redirect_uri |建議 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 它必須與您在入口網站中註冊的某個 redirect_uris 完全符合，不過它必須是 URL 編碼的。 |
| response_mode |建議使用 |指定將產生的權杖送回到應用程式所應該使用的方法。  對於隱含流程應該是 `fragment` 。 |
| scope |必要 |範圍的空格分隔清單。 單一範圍值，向 Azure AD 指出受到要求的兩個權限。 `openid` 範圍指出要以 **id_token** 的形式 (本文章稍後將進一步說明) 來登入使用者，以及取得使用者相關資料的權限。 對於 Web 應用程式， `offline_access` 範圍是選擇性。 它表示您的應用程式將需要 **refresh_token**，才能長久存取資源。 |
| state |建議 |同樣會隨權杖回應傳回之要求中所包含的值。 它可以是您所想要內容中的字串。 隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。 驗證要求出現前，也會先使用此狀態為使用者在應用程式中的狀態資訊編碼，例如他們先前所在的網頁。 |
| nonce |必要 |包含在要求中的值 (由應用程式所產生)，將會包含在所得的 id_token 中來做為宣告。 應用程式接著便可確認此值，以減少權杖重新執行攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| p |必要 |系統將會執行的原則。 這是在您的 B2C 租用戶中所建立的原則名稱。 原則名稱值的開頭必須是「b2c\_1\_」。 如需深入了解原則，請參閱 [可延伸的原則架構](active-directory-b2c-reference-policies.md)。 |
| prompt |選用 |需要的使用者互動類型。 此時唯一有效的值是 'login'，強制使用者針對該要求輸入其認證。 單一登入將沒有作用。 |

此時會要求使用者完成原則的工作流程。 視原則的定義方式，這可能會牽涉到讓使用者輸入自己的使用者名稱及密碼、以社交身分識別登入、註冊目錄，或是其他任何數目的步驟。

當使用者完成原則之後，Azure AD 就會使用您在 `response_mode` 參數中指定的方法，將回應傳回至指定的 `redirect_uri` 來給您的應用程式。 在上述各種情況下，回應完全相同，與已執行的原則無關。

#### <a name="successful-response"></a>成功回應
使用 `response_mode=fragment` 和 `response_type=id_token+token` 的成功回應如下所示 (內含換行符號以利閱讀)：

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| 參數 | 說明 |
| --- | --- |
| access_token |應用程式要求的存取權杖。  存取權杖不得進行解碼或檢查，可被視為不透明的字串。 |
| token_type |權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| expires_in |access_token 的有效時間長度 (以秒為單位)。 |
| scope |權杖的有效範圍，可用於快取權杖供以後使用。 |
| id_token |應用程式要求的 id_token。 您可以使用 id_token 確認使用者的身分識別，並以使用者開始工作階段。 如需 id_token 及其內容的詳細資料，請參閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。 |
| state |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應
錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 說明 |
| --- | --- |
| 錯誤 |用於將發生之錯誤分類的錯誤碼字串。 錯誤碼可用於錯誤處理。 |
| error_description |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |
| state |如需完整說明，請參閱前一個表格。 如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。|

## <a name="validate-the-idtoken"></a>驗證 id_token
只接收 id_token 並不足以驗證使用者，您必須驗證 id_token 的簽章，並依照應用程式的需求來確認權杖中的宣告。 Azure AD B2C 使用 [JSON Web Tokens (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰加密編譯來簽署權杖及驗證其是否有效。

視您偏好的語言而定，有許多針對驗證 JWT 的開放原始碼程式庫可用。 建議您探索這些程式庫，而不是實作您自己的驗證邏輯。 當您在思考如何適當地運用那些程式庫時，這裡的資訊會很有用。

Azure AD B2C 具有 OpenID Connect 中繼資料端點，可讓應用程式在執行階段提取 Azure AD B2C 的相關資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 B2C 租用戶中的每個原則都有一份 JSON 中繼資料文件。 例如，`fabrikamb2c.onmicrosoft.com` 中適用於 `b2c_1_sign_in` 原則的中繼資料文件位於：

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此設定文件的屬性之一是 `jwks_uri`，就上述原則而言，它的值是：

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`。

若要判斷哪個原則用來簽署 id_token (以及何處可擷取中繼資料)，您有兩個選項。 首先，原則名稱包含在 id_token 的 `acr` 宣告中。 如需有關如何剖析 id_token 中的宣告的相關資訊，請參閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。 另一個選項是當您發出要求時在 `state` 參數的值中將原則編碼，然後將它解碼以判斷使用了哪個原則。 任一種方法都絕對有效。

當您從 OpenID Connect 中繼資料端點取得中繼資料文件之後，就可以使用 RSA 256 (位於此端點) 公用金鑰來驗證 id_token 的簽章。 此端點可能隨時會列出多個金鑰，每個金鑰由 `kid`識別。 Id_token 標頭也包含 `kid` 宣告，其指出簽署 id_token 所使用的金鑰。 如需詳細資訊 (包括[驗證權杖](active-directory-b2c-reference-tokens.md#token-validation))，請參閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。
<!--TODO: Improve the information on this-->

驗證 id_token 的簽章之後，也需要驗證數個宣告，例如：

* 驗證 `nonce` 宣告，以防止權杖重新執行攻擊。 其值應該是您在登入要求中所指定的內容。
* 驗證 `aud` 宣告，以確保已針對您的應用程式簽發 id_token。 這個值就是您應用程式的應用程式識別碼。
* 驗證 `iat` 與 `exp` 宣告，以確保 id_token 沒有過期。

另外還有幾個您應該執行的驗證，在 [OpenID Connect 核心規格](http://openid.net/specs/openid-connect-core-1_0.html)中會有其詳細說明。  視您的案例而定，您可能也會想要驗證其他宣告。 一些常見的驗證包括：

* 確保使用者/組織已為應用程式註冊。
* 確保使用者有適當的授權/權限。
* 確保驗證方式有一定的強度，例如 Azure Multi-Factor Authentication。

如需 id_token 中的宣告的詳細資訊，請參閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。

當您徹底驗證 id_token 之後，即可利用使用者開始工作階段，並使用 id_token 中的宣告來取得應用程式中使用者的相關資訊。 這項資訊可以用於顯示、記錄、授權等等。

## <a name="get-access-tokens"></a>取得存取權杖
如果您的 Web 應用程式只需要執行原則，您可以略過接下來的幾節。 適用於這幾節的 Web 應用程式需要對 Web API 進行已驗證的呼叫，而且還受到 Azure AD B2C 的保護。

您已經將使用者註冊到單一頁面應用程式，您可以取得存取權杖以呼叫受到 Azure AD 保護的 Web API。  即使您已經收到使用 `token` response_type 的權杖，仍可使用此方法來取得其他資源的權杖，而不需重新導向使用者進行再次登入。

在正常的 Web 應用程式流程中，您會透過對 `/token` 端點進行要求來完成這個操作。  不過，該端點不支援 CORS 要求，因此進行 AJAX 呼叫以取得及重新整理權杖是不可能的。  相反地，您可以在隱藏的 iframe 中使用隱含流程，為其他 Web API 取得新權杖：

```
// Line breaks for legibility only

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| 參數 |  | 說明 |
| --- | --- | --- |
| client_id |必要 |[Azure 入口網站](https://portal.azure.com/) 指派給您應用程式的應用程式識別碼。 |
| response_type |必要 |必須包含 OpenID Connect 登入的 `id_token` 。  它也可能包含 response_type `token`。 這裡使用 `token` ，讓您的應用程式能夠立即從授權端點接收存取權杖，而不需要向授權端點進行第二次要求。  如果您使用 `token` response_type，`scope` 參數必須包含範圍，以指出要對哪個資源發出權杖。 |
| redirect_uri |建議使用 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。  其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 |
| scope |必要 |範圍的空格分隔清單。  若要取得權杖，請包含您感興趣資源要求的所有範圍。 |
| response_mode |建議使用 |指定將產生之權杖送回到應用程式要使用的方法。  可以是 `query`、`form_post` 或 `fragment` 其中一個。 |
| state |建議使用 |會隨權杖回應傳回之要求中所包含的值。  其可以是您想要之任何內容的字串。  隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。  此狀態也用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| nonce |必要 |由應用程式產生且包含在要求中的值，它會以宣告形式包含在產生的 id_token 中。  應用程式接著便可確認此值，以減少權杖重新執行攻擊。  此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| prompt |必要 |若要重新整理並取得隱藏 iframe 中的權杖，請使用 `prompt=none` 以確保 iframe 不會懸置在登入頁面上，並立即返回。 |
| login_hint |必要 |如需重新整理並取得隱藏 iframe 中的權杖，您必須在此提示中包含使用者的使用者名稱，以便區分使用者在特定時間點可能具有的多個工作階段。 您可以使用 `preferred_username` 宣告擷取先前登入的使用者名稱。 |
| domain_hint |必要 |可以是 `consumers` 或 `organizations` 其中一個。  若要重新整理並取得隱藏 iframe 中的權杖，您必須在要求中包含 domain_hint。  從先前登入的 id_token 擷取 `tid` 宣告，以判斷要使用哪一個值。  如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用 `domain_hint=consumers`。  否則，使用 `domain_hint=organizations`。 |

一旦設定 `prompt=none` 參數，此要求會立即成功或失敗，並且返回您的應用程式。  會使用 `response_mode` 參數中指定的方法，將成功的回應傳送至指定的 `redirect_uri` 給您的應用程式。

#### <a name="successful-response"></a>成功回應
使用 `response_mode=fragment` 的成功回應如下所示：

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| 參數 | 說明 |
| --- | --- |
| access_token |應用程式要求的權杖。 |
| token_type |一律為 `Bearer`。 |
| state |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |
| expires_in |存取權杖的有效期 (以秒為單位)。 |
| scope |存取權杖有效的範圍。 |

#### <a name="error-response"></a>錯誤回應
錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理。  如果是 `prompt=none`，預期的錯誤為：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 參數 | 說明 |
| --- | --- |
| 錯誤 |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error_description |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

如果您在 iframe 要求中收到此錯誤，使用者必須再次以互動方式登入以擷取新的權杖。  您可以選擇對於您的應用程式合理的任何方式處理這種情況。

## <a name="refreshing-tokens"></a>重新整理權杖
`id_token` 和 `access_token` 馬上就會到期，因此您的應用程式必須準備好定期重新整理這些權杖。  若要重新整理其中任何一個類型的權杖，請使用 `prompt=none` 參數來控制 Azure AD 的行為，執行上述的相同隱藏的 iframe 要求。  若要接收新的 `id_token`，請務必使用 `response_type=id_token` 和 `scope=openid`，以及 `nonce` 參數。

## <a name="send-a-sign-out-request"></a>傳送登出要求
當您想要將使用者登出應用程式時，請將使用者重新導向到 Azure AD 進行登出。 如果您沒有這麼做，使用者可能不必重新輸入認證，就能夠向您的應用程式重新驗證自己的身分。 這是因為使用者將擁有有效的 Azure AD 單一登入工作階段。

您只要把使用者重新導向至 `end_session_endpoint` (列於之前＜驗證 id_token＞一節中所述的 OpenID Connect 中繼資料文件中) 即可：

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 參數 | 必要？ | 說明 |
| --- | --- | --- |
| p |必要 |用來將使用者登出應用程式的原則。 |
| post_logout_redirect_uri |建議 |使用者在成功登出之後，應該要前往的 URL。 如果沒有包含這項資料，Azure AD B2C 會向使用者顯示一般的訊息。 |

> [!NOTE]
> 雖然將使用者導向至 `end_session_endpoint` 能清除使用者的部分 Azure AD B2C 單一登入狀態，但無法讓使用者登出其社交識別提供者 (IDP) 工作階段。 如果使用者之後在登入時選取相同的 IDP，該使用者不必輸入自己的認證，就能讓系統重新驗證自己的身分。 雖然使用者想要登出您的 B2C 應用程式，但這並不一定代表他們想要完全登出自己的 Facebook 帳戶。 不過，如果是使用本機帳戶，使用者的工作階段便會正確地結束。
> 
> 

## <a name="use-your-own-b2c-tenant"></a>使用您自己的 B2C 租用戶
如果您想要親自嘗試這些要求，您必須先執行下列三個步驟，然後用您自己值來取代上面的範例值：

* [建立 B2C 租用戶](active-directory-b2c-get-started.md)，並在要求中使用您租用戶的名稱。
* [建立應用程式](active-directory-b2c-app-registration.md)來取得應用程式識別碼和 redirect_uri。 您可以在應用程式中加入 **Web 應用程式/Web API**，並選擇性地建立**應用程式祕密**。
* [建立您的原則](active-directory-b2c-reference-policies.md) 來取得原則名稱。

## <a name="samples"></a>範例

* [使用 Node.JS 建立單一頁面應用程式](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [使用 .NET 建立單一頁面應用程式](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)




<!--HONumber=Feb17_HO1-->


