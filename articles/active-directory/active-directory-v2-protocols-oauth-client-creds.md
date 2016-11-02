
<properties
    pageTitle="Azure AD v2.0 OAuth 用戶端認證流程 | Microsoft Azure"
    description="使用 Azure AD 實作 OAuth 2.0 驗證通訊協定建置 Web 應用程式。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dastrock"/>


# <a name="v2.0-protocols---oauth-2.0-client-credentials-flow"></a>v2.0 通訊協定 - OAuth 2.0 用戶端認證流程

[OAuth 2.0 用戶端認證授與](http://tools.ietf.org/html/rfc6749#section-4.4)(有時稱為「雙腳 OAuth」) 可供使用應用程式的身分識別來存取 Web 裝載資源。  通常用於必須在背景中執行的伺服器對伺服器互動，使用者不需立即現身。  這些類型的應用程式通常稱為**精靈**或**服務帳戶**。

> [AZURE.NOTE]
    v2.0 端點並非支援每個 Azure Active Directory 案例和功能。  若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。

在較為典型的「三腳 OAuth」中，用戶端應用程式可獲得代表特定使用者存取資源的權限。  此權限是由使用者 **委派** 給應用程式 (通常在 [同意](active-directory-v2-scopes.md) 過程中)。  不過，在用戶端認證流程中，權限會 **直接** 授與應用程式本身。  當應用程式向資源出示權杖時，資源會強制應用程式本身有權執行一些動作 - 而不是某些使用者有權。

## <a name="protocol-diagram"></a>通訊協定圖表
整個用戶端認證流程看起來類似下列圖表 - 以下將詳細說明每個步驟。

![用戶端認證流程](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>取得直接授權 
應用程式通常收到直接授權來存取資源的方法有兩種︰透過資源上的存取控制清單，或透過 Azure AD 中的應用程式權限指派。  資源可選擇其他幾種方法來對其用戶端授權，而每個資源伺服器可選擇最適合其應用程式的方法。  以下兩種方法是 Azure AD 中最常見的方法，建議用於想要執行用戶端認證流程的用戶端和資源。

### <a name="access-control-lists"></a>存取控制清單
指定的資源提供者可能會根據它所知的應用程式識別碼清單，強制執行授權檢查並授與特定層級的存取權。  當資源從 v2.0 端點收到權杖時，它可以將此權杖解碼，並從 `appid` 和 `iss` 宣告擷取用戶端的應用程式識別碼。  然後，可以比對應用程式與它所維護的某一份存取控制清單 (ACL)。  各資源的存取控制清單細微度和方法可能大不相同。

此類 ACL 的常見使用案例是 Web 應用程式或 Web API 的測試執行器。  Web API 可能只會將其部份的權限授與給各種用戶端。  但為了在 API 上執行端對端測試，系統會建立測試用戶端，以便從 v2.0 端點取得權杖並將權杖傳送至 API。  API 可以接著將測試用戶端的應用程式識別碼納入 ACL，以取得 API 完整功能的完整存取權。  請注意，如果您的服務有此種清單，您應確保不僅驗證呼叫者的 `appid`，而且還會驗證權杖的 `iss` 是否可信任。

對於需要利用個人 Microsoft 帳戶存取消費者使用者所擁有資料的精靈和服務帳戶而言，此授權類型很常見。  若為組織所擁有的資料，建議您透過應用程式權限取得必要的授權。

### <a name="application-permissions"></a>應用程式權限
除了使用 ACL，API 可以公開一組可授與給應用程式的 **應用程式權限** 。  組織的系統管理員會將應用程式權限授與給應用程式，而此應用程式權限只可用於存取該組織與其員工所擁有的資料。  例如，Microsoft Graph 會公開數個應用程式權限︰

- 讀取所有信箱中的郵件
- 讀取和寫入所有信箱中的郵件
- 以任何使用者的身分傳送郵件
- 讀取目錄資料
- [+ 更多功能](https://graph.microsoft.io)

若要在您的應用程式中取得這些權限，您可以執行下列步驟。

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>在應用程式註冊入口網站中要求權限

- 在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 中瀏覽至您的應用程式，或[建立應用程式](active-directory-v2-app-registration.md) (如果尚未這麼做)。  您必須確定您的應用程式已建立至少一個應用程式密碼。
- 找出 [直接應用程式權限]  區段並新增您的應用程式所需的權限。
- 務必 [儲存]  應用程式註冊

#### <a name="recommended:-sign-the-user-into-your-app"></a>建議︰讓使用者登入您的應用程式

在建置使用應用程式權限的應用程式時，應用程式通常需要一個頁面/檢視，讓系統管理員核准應用程式的權限。  此頁面可以是應用程式註冊流程的一部分、應用程式設定的一部分，或專用「連接」流程的一部分。  在許多情況下，應用程式只在使用者利用工作或學校 Microsoft 帳戶登入之後顯示此「連接」檢視是很合理的。

將使用者登入應用程式，可讓您先識別使用者所屬的組織，再要求他們核准應用程式權限。  雖然不是絕對必要，但這麼做可協助您為您組織的使用者建立更直覺式的經驗。  若要讓使用者登入，請遵循我們的 [v2.0 通訊協定教學課程](active-directory-v2-protocols.md)。

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

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 必要 | 您想要要求權限的目錄租用戶。  可以 guid 或易記名稱格式提供。  如果不知道使用者屬於哪個租用戶並想要讓他們利用任何租用戶登入，請使用 `common`。 |
| client_id | 必要 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 指派給應用程式的應用程式識別碼。 |
| redirect_uri | 必要 | 您想要傳送回應的 redirect_uri，以便您的應用程式處理。  其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須是編碼的 url 並可有其他路徑區段。 |
| state | 建議使用 | 同樣會隨權杖回應傳回之要求中所包含的值。  其可以是您想要之任何內容的字串。  此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |

此時，Azure AD 會強制只有租用戶管理員可以登入來完成要求。  系統會要求系統管理員核准您在註冊入口網站中針對您的應用程式要求的所有直接應用程式權限。 

##### <a name="successful-response"></a>成功回應
如果系統管理員針對您的應用程式核准權限，則成功的回應如下︰

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 將要求的權限授與應用程式的目錄租用戶 (採用 guid 格式)。 |
| state | 同樣會隨權杖回應傳回之要求中所包含的值。  其可以是您想要之任何內容的字串。  此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| admin_consent | 將設定為 `True`。 |


##### <a name="error-response"></a>錯誤回應
如果系統管理員未針對您的應用程式核准權限，失敗的回應如下︰

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- | --------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error_description | 可協助開發人員識別錯誤根本原因的特定錯誤訊息。  |

從應用程式佈建端點收到成功回應後，您的應用程式便已取得它所要求的直接應用程式權限。  您現在可以繼續要求所需資源的權杖。

## <a name="get-a-token"></a>取得權杖
取得應用程式的必要授權後，您可以繼續取得 API 的存取權杖。  若要使用用戶端認證授與取得權杖，請將 POST 要求傳送至 `/token` v2.0 端點︰

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | --------------- |
| client_id | 必要 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 指派給應用程式的應用程式識別碼。 |
| scope | 必要 | 在這個要求中針對 `scope` 參數傳遞的值應該是所需資源的資源識別碼 (應用程式識別碼 URI)，並附加 `.default` 尾碼。  所以在指定的 Microsoft Graph 範例中，此值應該是 `https://graph.microsoft.com/.default`。  這個值會通知 v2.0 端點有關您為應用程式設定的所有直接應用程式權限，它應該會針對屬於所需資源的項目發出權杖。 |
| client_secret | 必要 | 您在應用程式註冊入口網站中為應用程式產生的應用程式密碼。 |
| grant_type | 必要 | 必須是 `client_credentials`。 | 

#### <a name="successful-response"></a>成功回應
成功回應將採用以下形式：

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| access_token | 所要求的存取權杖。 應用程式可以使用這個權杖驗證受保護的資源，例如 Web API。 |
| token_type | 表示權杖類型值。 Azure AD 唯一支援的類型是 `Bearer`。  |
| expires_in | 存取權杖的有效期 (以秒為單位)。 |

#### <a name="error-response"></a>錯誤回應
錯誤回應將採用以下形式：

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。  |
| error_codes | 有助於診斷的 STS 特定錯誤碼清單。  |
| timestamp | 發生錯誤的時間。 |
| trace_id | 有助於診斷的要求唯一識別碼。  |
| correlation_id | 有助於跨元件診斷的要求唯一識別碼。 |

## <a name="use-a-token"></a>使用權杖
既然您已取得權杖，您可以使用該權杖來對資源提出要求。  當權杖過期時，只要重複對 `/token` 端點的要求，即可取得全新的存取權杖。

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>程式碼範例
若要查看使用系統管理員同意端點實作 client_credentials 授與的應用程式範例，請參閱 [v2.0 精靈程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。


<!--HONumber=Oct16_HO2-->


