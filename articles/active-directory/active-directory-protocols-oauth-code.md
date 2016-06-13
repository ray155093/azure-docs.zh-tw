<properties
	pageTitle="Azure AD .NET 通訊協定概觀 | Microsoft Azure"
	description="本文說明如何使用 HTTP 訊息來使用 Azure Active Directory 和 OAuth 2.0 授權存取租用戶中的 Web 應用程式和 Web API。"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# 使用 OAuth 2.0 和 Azure Active Directory 授權存取 Web 應用程式

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Azure Active Directory (Azure AD) 使用 OAuth 2.0 讓您授權存取 Azure AD 租用戶中的 Web 應用程式和 Web API。本指南與語言無關，描述在不使用我們的任何開放原始碼程式庫的情況下，如何傳送和接收 HTTP 訊息。

如需 OAuth 2.0 授權碼流程的說明，請參閱 [OAuth 2.0 規格的 4.1 節](https://tools.ietf.org/html/rfc6749#section-4.1)。在大部分的應用程式類型中，其用於執行驗證與授權，包括 Web Apps 和原始安裝的應用程式。

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## OAuth 2.0 授權流程

概括而言，應用程式的整個授權流程看起來有點像這樣：

![OAuth 授權碼流程](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## 要求授權碼

授權碼流程始於用戶端將使用者導向 `/authorize` 端點。在這項要求中，用戶端會指出必須向使用者索取的權限。您可以在 Azure 傳統入口網站的應用程式頁面中，於底端隱藏式選單的 [檢視端點] 按鈕取得 OAuth 2.0 端點。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 必要 | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。租用戶獨立權杖允許的值為租用戶識別碼，例如 `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` 或 `common` |
| client\_id | 必要 | 向 Azure AD 註冊應用程式時，指派給您的應用程式的識別碼。您可在 Azure 管理入口網站中找到此值。按一下 [Active Directory]，按一下目錄，按一下應用程式，然後按一下 [設定] |
| response\_type | 必要 | 授權碼流程必須包含 `code`。 |
| redirect\_uri | 建議使用 | 應用程式的 redirect\_uri，您的應用程式可在此傳送及接收驗證回應。其必須完全符合您在入口網站中註冊的其中一個 redirect\_uris，不然就必須得是編碼的 url。對於原生和行動應用程式，請使用 `urn:ietf:wg:oauth:2.0:oob` 的預設值。 |
| scope | 必要 | 您要使用者同意的[範圍](active-directory-v2-scopes.md)空格分隔清單。 |
| response\_mode | 建議使用 | 指定將產生的權杖送回到應用程式所應該使用的方法。可以是 `query` 或 `form_post`。 |
| state | 建議使用 | 同樣會隨權杖回應傳回之要求中所包含的值。其可以是您想要之任何內容的字串。隨機產生的唯一值通常用於[防止跨網站偽造要求攻擊](http://tools.ietf.org/html/rfc6749#section-10.12)。此狀態也用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| prompt | 選用 | 表示需要的使用者互動類型。此時的有效值為「登入」、「無」和「同意」。`prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。`prompt=none` 則相反 - 它會確保不會對使用者顯示任何互動式提示。如果要求無法透過單一登入以無訊息方式完成，v2.0 端點會傳回錯誤。`prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| login\_hint | 選用 | 如果您事先知道其使用者名稱，可用來預先填入使用者登入頁面的使用者名稱/電子郵件地址欄位。通常應用程式會在重新驗證期間使用此參數，已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。 |
| domain\_hint | 選用 | 可以是 `consumers` 或 `organizations` 其中一個。如果包含，它會略過使用者在 v2.0 登入頁面上經歷的以電子郵件為基礎的探索程序，導致稍微更佳流暢的使用者經驗。通常應用程式會在重新驗證 (擷取上一次登入的 `tid`) 期間使用此參數。如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用 `domain_hint=consumers`。否則，使用 `domain_hint=organizations`。 |

> [AZURE.NOTE] 如果使用者隸屬於組織，組織的系統管理員可以代表使用者同意或拒絕，或允許使用者自行同意。只有當系統管理員允許時，使用者才會獲得同意的選項。

此時，會要求使用者輸入其認證，並同意 `scope` 查詢參數所指出的權限。一旦使用者驗證並授與同意，Azure AD 便會在要求的 `redirect_uri` 位址中傳送回應給應用程式。

### 成功回應

成功的回應看起來可能像這樣︰

```
http://localhost:12345/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&state=12345
&session_state=733ad279-b681-49c3-9215-951abf94d2c5
```

| 參數 | 說明 |
| -----------------------| --------------- |
| admin\_consent | 如果系統管理員已對同意要求提示表示同意，則值為 True。|
| code | 應用程式要求的授權碼。應用程式可以使用授權碼要求目標資源的存取權杖。 |
| session\_state | 識別目前使用者工作階段的唯一值。這個值是 GUID，但應視為不檢查即傳遞的不透明值。 |
| state | 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求中的狀態值
如果要求中包含狀態參數，回應中就會出現相同的值。應用程式最好確認要求和回應中的狀態值完全相同。

### 錯誤回應

錯誤回應也可以傳送到 `redirect_uri`，讓應用程式能適當地處理。

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

## 使用授權碼來要求存取權杖

既然您已經取得授權碼並獲得使用者授權，您就可以將 POST 要求傳送至 `/token` 端點，兌換授權碼以取得所需資源的存取權杖：

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: client_secret only required for web apps
```

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | 必要 | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。租用戶獨立權杖允許的值為租用戶識別碼，例如 `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` 或 `common` |
| client\_id | 必要 | 向 Azure AD 註冊應用程式時，指派給您的應用程式的識別碼。您可在 Azure 傳統入口網站中找到此資訊。按一下 [Active Directory]，按一下目錄，按一下應用程式，然後按一下 [設定] |
| grant\_type | 必要 | 必須是授權碼流程的 `authorization_code`。 |
| code | 必要 | 您在上一節中取得的 `authorization_code` |
| redirect\_uri | 必要 | 用來取得 `authorization_code` 的相同 `redirect_uri` 值。 |
| client\_secret | Web Apps 所需 | 您在應用程式註冊入口網站中為應用程式建立的應用程式密碼。其不應用於原生應用程式，因為裝置無法穩當地儲存 client\_secret。Web Apps 和 Web API 都需要應用程式密碼，其能夠將 `client_secret` 安全地儲存在伺服器端。 |


### 成功回應

成功的回應看起來可能像這樣︰

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| access\_token | 所要求的存取權杖。應用程式可以使用這個權杖驗證受保護的資源，例如 Web API。 |
| token\_type | 表示權杖類型值。Azure AD 唯一支援的類型是 Bearer。如需持有人權杖的詳細資訊，請參閱 [OAuth2.0 授權架構︰持有人權杖用法 (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires\_in | 存取權杖的有效期 (以秒為單位)。 |
| scope | 授與用戶端應用程式的模擬權限。預設權限為 `user_impersonation`。受保護資源的擁有者可以在 Azure AD 中註冊其他的值。|
| refresh\_token | OAuth 2.0 重新整理權杖。應用程式可以使用這個權杖，在目前的存取權杖過期之後，取得其他的存取權杖。重新整理權杖的有效期很長，而且可以用來長期保留資源存取權。 |
| id\_token | 不帶正負號的 JSON Web Token (JWT)。應用程式可以 base64Url 解碼這個權杖的區段，要求已登入使用者的相關資訊。應用程式可以快取並顯示值，但不應依賴這些值來取得任何授權或安全性界限。 |

### JWT 權杖宣告
`id_token` 參數值中的 JWT 權杖可以解碼為下列宣告︰

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

`id_token` 參數包含下列宣告類型。如需 JSON Web 權杖的詳細資訊，請參閱 [JWT IETF 草稿規格](http://go.microsoft.com/fwlink/?LinkId=392344)。如需有關權杖類型及宣告的詳細資訊，請參閱[支援的權杖和宣告類型](active-directory-token-and-claims.md)

| 宣告類型 | 說明 |
|------------|-------------|
| aud | 權杖的對象。向用戶端應用程式發出權杖時，對象是用戶端的 `client_id`。
| exp | 到期時間。權杖的到期時間。權杖若要有效，目前的日期/時間必須小於或等於 `exp` 值。時間會表示為從 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 到權杖發出時間的秒數。 |
| family\_name | 使用者的姓氏。應用程式可以顯示這個值。 |
| given\_name | 使用者的名字。應用程式可以顯示這個值。 |
| iat | 發出時間。發出 JWT 的時間。時間會表示為從 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 到權杖發出時間的秒數。 |
| iss | 識別權杖簽發者 |
| nbf | 生效時間。權杖生效的時間。權杖若要有效，目前的日期/時間必須大於或等於 Nbf 值。時間會表示為從 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 到權杖發出時間的秒數。 |
| oid | Azure AD 中使用者物件的物件識別碼 (ID)。 |
| sub | 權杖主旨識別碼。這是權杖所描述之使用者的持續性和不可變的識別碼。請在快取邏輯中使用此值。 |
| tid | 發出權杖之 Azure AD 租用戶的租用戶識別碼 (ID)。 |
| unique\_name | 可以對使用者顯示的唯一識別碼。這通常是使用者主體名稱 (UPN)。 |
| upn | 使用者的使用者主體名稱。 |
| ver | 版本。JWT 權杖的版本，通常為 1.0。 |

### 錯誤回應

範例錯誤回應看起來可能像這樣︰

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |
| error\_codes | 有助於診斷的 STS 特定錯誤碼清單。 |
| timestamp | 發生錯誤的時間。 |
| trace\_id | 有助於診斷的要求唯一識別碼。 |
| correlation\_id | 有助於跨元件診斷的要求唯一識別碼。|

## 使用存取權杖來存取資源

既然您已經成功取得 `access_token`，您就可以透過在 `Authorization` 標頭中包含權杖，在 Web API 的要求中使用權杖。[RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) 規格會說明如何在 HTTP 要求中使用持有人權杖來存取受保護的資源。

### 範例要求

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

## 重新整理存取權杖

存取權杖有效期很短，到期後必須重新整理，才能繼續存取資源。您可以重新整理 `access_token`，方法是向 `/token` 端點送出另一個 `POST` 要求，但這次提供 `refresh_token`，而不提供 `code`。

不會提供重新整理權杖的存留期，而且此存留期會根據原則設定，以及 Azure AD 撤銷授權碼授與的時間而有所不同。應用程式應該預期並處理新的存取權杖要求失敗時的情況。在此情況下，它應該傳回給要求新存取權杖的程式碼。

使用重新整理權杖來取得新存取權杖之**租用戶專屬**端點 (您也可以使用**一般**端點) 的要求範例看起來像這樣︰

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| 參數 | 說明 |
|-----------|-------------|
| access\_token | 所要求的新存取權杖。|
| expires\_in | 權杖的剩餘存留期 (秒)。一般值為 3600 (1 小時)。 |
| expires\_on | 權杖的到期日期和時間。日期會表示為從 1970-01-01T0:0:0Z UTC 至到期時間的秒數。 |
| refresh\_token | 此回應中的存取權杖到期時，可用來要求新存取權杖的新 OAuth 2.0 refresh\_token。 |
| resource | 識別存取權杖可用來存取的受保護的資源。 |
| scope | 授與原生用戶端應用程式的模擬權限。預設權限為 **user\_impersonation**。目標資源的擁有者可以在 Azure AD 中註冊替代值。 |
| token\_type | 權杖類型。唯一支援的值為 **bearer**。 |

### 成功回應

成功的權杖回應如下：

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### 錯誤回應

範例錯誤回應看起來可能像這樣︰

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

<!---HONumber=AcomDC_0601_2016-->