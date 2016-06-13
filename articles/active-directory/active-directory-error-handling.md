<properties
	pageTitle="OAuth 2.0 中的錯誤處理 | Microsoft Azure"
	description="本文說明 OAuth 2.0 中的 Azure Active Directory 常見錯誤類別，以及其最佳處理做法。"
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

# OAuth 2.0 中的錯誤處理

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

在本文中，我們將了解當您使用 Azure Active Directory (Azure AD) 授權應用程式時，所可能遇到之常見錯誤類別的最佳處理做法。如需授權端點和權杖發行端點的詳細資訊，請參閱 [Azure AD 中的應用程式驗證流程](active-directory-protocols-oauth-code.md)。

## 授權端點錯誤

在授權程序的第一個步驟中，用戶端應用程式會傳送要求給 Azure AD 的 `/authorize` 端點，並傳送使用者所需權限的清單。

授權端點錯誤源自 `/authorize` 端點。這些錯誤會傳回為網頁上的 HTTP 200 錯誤，或是在有用戶端應用程式可供處理錯誤時，使用 HTTP 302 重新導向代碼來傳回。

以下是當要求遺漏必要的 `response_type` 參數時，來自 Azure AD 授權端點的範例 HTTP 302 錯誤回應。

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?error=invalid_request&error_description=AADSTS90014%3a+The+request+body+must+contain+the+following+parameter%3a+%27response_type%27.%0d%0aTrace+ID%3a+57f5cb47-2278-4802-a018-d05d9145daad%0d%0aCorrelation+ID%3a+570a9ed3-bf1d-40d1-81ae-63465cc25488%0d%0aTimestamp%3a+2013-12-31+05%3a51%3a35Z&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| 參數 | 說明 |
|-----------|-------------|
| 錯誤 | [OAuth 2.0 授權架構](http://tools.ietf.org/html/rfc6749)的 5.2 節中所定義的錯誤碼值。下一份資料表會描述 Azure AD 傳回的錯誤碼。 |
| error\_description | 更詳細的錯誤描述。此訊息的目的並非要方便使用者了解。 |
| state | 狀態值是隨機產生的非重複使用值，會在要求中傳送並在回應中傳回以防止跨網站偽造要求 (CSRF) 攻擊。 |

### 授權端點錯誤的錯誤碼

下表說明各種可能在錯誤回應的 `error` 參數中傳回的錯誤碼。

| 錯誤碼 | 說明 | 用戶端動作 |
|------------|-------------|---------------|
| invalid\_request | 通訊協定錯誤，例如遺漏必要的參數。 | 修正並重新提交要求。這是通常會在初始測試期間擷取到的開發錯誤。|
| unauthorized\_client | 不允許用戶端應用程式要求授權碼。 | 這通常會在用戶端應用程式未在 Azure AD 中註冊，或未加入至使用者的 Azure AD 租用戶時發生。應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| access\_denied | 資源擁有者拒絕同意 | 用戶端應用程式可以通知使用者，除非使用者同意，否則無法繼續進行。 |
| unsupported\_response\_type | 授權伺服器不支援要求中的回應類型。 | 修正並重新提交要求。這是通常會在初始測試期間擷取到的開發錯誤。|
|server\_error | 伺服器發生非預期的錯誤。 | 重試要求。這些錯誤可能是由暫時性狀況所引起。用戶端應用程式可能會向使用者解釋，其回應因為暫時性錯誤而延遲。 |
| temporarily\_unavailable | 伺服器暫時過於忙碌而無法處理要求。 | 重試要求。用戶端應用程式可能會向使用者解釋，其回應因為暫時性狀況而延遲。 |
| invalid\_resource |目標資源無效，因為它不存在、Azure AD 無法找到它，或是它並未正確設定。| 這表示尚未在租用戶中設定資源 (如果存在)。應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |

## 權杖發行端點錯誤

一旦應用程式從 `/authorize` 端點收到授權碼，就會將它傳遞至 `/token` 端點以接收指定資源的存取權杖。

權杖發行端點錯誤源自 `/token` 端點。這些是 HTTP 錯誤碼，因為用戶端會直接呼叫權杖發行端點。除了 HTTP 狀態碼，Azure AD 權杖發行端點也會傳回 JSON 文件與描述錯誤的物件。

例如，要求中的 `client_id` 參數無效時的錯誤，看起來可能像︰

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8

{"error":"invalid_request","error_description":"AADSTS90011: Request is ambiguous, multiple application identifiers found. Application identifiers: '197451ec-ade4-40e4-b403-02105abd9049, 597451ec-ade4-40e4-b403-02105abd9049'.\r\nTrace ID: 4457d068-2a03-42b2-97f2-d55325289d86\r\nCorrelation ID: 6b3474d8-233e-463f-b0a3-86433d8ba889\r\nTimestamp: 2013-12-31 06:31:41Z","error_codes":[90011],"timestamp":"2013-12-31 06:31:41Z","trace_id":"4457d068-2a03-42b2-97f2-d55325289d86","correlation_id":"6b3474d8-233e-463f-b0a3-86433d8ba889"}
```
### HTTP 狀態碼

下表列出權杖發行端點傳回的 HTTP 狀態碼。在某些情況下，錯誤碼就足以描述回應，但若發生錯誤，您就必須剖析隨附的 JSON 文件並檢查其錯誤碼。

| HTTP 代碼 | 說明 |
|-----------|-------------|
| 400 | 預設的 HTTP 代碼。用於大部分情況，通常是因為要求的格式不正確。修正並重新提交要求。 |
| 401 | 驗證失敗。例如，要求遺漏 client\_secret 參數。|
| 403 | 授權失敗。例如，使用者沒有存取資源的權限。 |
| 500 | 服務發生內部錯誤。重試要求。 |

### 錯誤回應中的 JSON 文件物件

| JSON 物件 | 說明 |
|-------------|-------------|
| 錯誤 | [OAuth 2.0 授權架構](http://tools.ietf.org/html/rfc6749)的 5.2 節中所定義的錯誤碼值。下一份資料表會描述 Azure AD 傳回的錯誤碼。 |
| error\_description | 更詳細的錯誤描述。此訊息的目的並非要方便使用者了解。 |
| timestamp | 錯誤發生日期和時間，使用國際標準時間 (UTC)。 |
| trace\_id | 識別錯誤追蹤的識別碼。 |
| correlation\_id | 	用戶端所產生的值。權杖發行端點的要求在 `client-request-id` 標頭中包含此識別碼時，此值會包含在 JSON 錯誤文件中。相同工作階段中的其他呼叫可能使用此識別碼。 |
| error\_codes | 包含對應至不同服務狀況的錯誤碼清單。請勿在應用程式邏輯中使用這些錯誤碼。不過，您可以使用它們來診斷問題，並依錯誤碼來線上搜尋資訊。 |

### JSON 文件錯誤碼值

| 錯誤碼 | 說明 | 用戶端動作 |
|------------|-------------|---------------|
| invalid\_request | 通訊協定錯誤，例如遺漏必要的參數。 | 修正並重新提交要求 |
| invalid\_grant | 授權授與 (或其參數) 或重新整理權杖無效、過期或已撤銷。 | 修正並重新提交要求。 |
| unauthorized\_client | 未授權驗證用戶端使用此授權授與類型。 | 這通常會在用戶端應用程式未在 Azure AD 中註冊，或未加入至使用者的 Azure AD 租用戶時發生。應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| invalid\_client | 用戶端驗證失敗。 | 用戶端認證無效。若要修正，應用程式系統管理員必須更新認證。 |
| unsupported\_grant\_type | 授權伺服器不支援授權授與類型。 | 變更要求中的授與類型。這種類型的錯誤應該只會在開發期間發生，並且會在初始測試期間偵測出來。 |
| invalid\_resource | 目標資源無效，因為它不存在、Azure AD 無法找到它，或是它並未正確設定。 | 這表示尚未在租用戶中設定資源 (如果存在)。應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| interaction\_required | 要求需要使用者互動。例如，必須進行其他驗證步驟。 | 以相同資源重試要求。 |
| temporarily\_unavailable | 伺服器暫時過於忙碌而無法處理要求。 | 重試要求。用戶端應用程式可能會向使用者解釋，其回應因為暫時性狀況而延遲。|

## 受保護資源的錯誤

如果受保護的資源實作 OAuth 2.0 授權架構的 [RFC 6750](http://tools.ietf.org/html/rfc6750) 規格，這些便是它 (例如 Web API) 可能傳回的錯誤。

實作 RFC 6750 的受保護資源會發出 HTTP 狀態碼。如果要求不包含驗證認證或是遺漏權杖，回應中會包含 `WWW-Authenticate` 標頭。當要求失敗時，資源伺服器會回應 HTTP 狀態碼和錯誤碼。

以下是用戶端要求未包含持有人權杖時的不成功回應範例︰

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

## 錯誤參數

| 參數 | 說明 |
|-----------|-------------|
| authorization\_uri | 授權伺服器的 URI (實體端點)。此值也可做為查閱索引鍵，以從探索端點取得伺服器的詳細資訊。<p><p> 用戶端必須確認授權伺服器受到信任。當資源受到 Azure AD 保護時，便足以確認 URL 開頭為 https://login.windows.net 或 Azure AD 支援的其他主機名稱。租用戶特定資源應該一律會傳回租用戶特定授權 URI。 |
| 錯誤 | [OAuth 2.0 授權架構](http://tools.ietf.org/html/rfc6749)的 5.2 節中所定義的錯誤碼值。|
| error\_description | 更詳細的錯誤描述。此訊息的目的並非要方便使用者了解。|
| resource\_id | 傳回資源的唯一識別碼。用戶端應用程式可在要求資源的權杖時，使用此識別碼做為 `resource` 參數的值。<p><p> 用戶端應用程式務必要確認此值，否則惡意服務或許可以引發**提升權限**攻擊 <p><p> 防止攻擊的建議策略是確認 `resource_id` 符合要存取的 Web API URL 的基礎。例如，如果要存取 https://service.contoso.com/data，`resource_id` 可以是 htttps://service.contoso.com/。除非有可確認識別碼的可靠替代方法，否則用戶端應用程式必須拒絕開頭不是基礎 URL 的 `resource_id`。 |

## 持有人配置錯誤碼

RFC 6750 規格會針對在回應中使用 WWW 驗證標頭和持有人配置的資源，定義下列錯誤。

| HTTP 狀態碼 | 錯誤碼 | 說明 | 用戶端動作 |
|------------------|------------|-------------|---------------|
| 400 | invalid\_request | 要求的格式不正確。例如，它可能遺漏參數或使用相同參數兩次。 | 修正錯誤，然後重試要求。這種類型的錯誤應該只會在開發期間發生，並且會在初始測試中偵測出來。 |
| 401 | invalid\_token | 存取權杖遺漏、無效或已撤銷。error\_description 參數的值會提供其他詳細資料。 | 從授權伺服器要求新的權杖。如果新的權杖失敗，則表示已發生未預期的錯誤。傳送錯誤訊息給使用者，並在隨機的延遲之後重試。 |
| 403 | insufficient\_scope | 存取權杖不包含存取資源所需的模擬權限。 | 將新的授權要求傳送至授權端點。如果回應包含範圍參數，則在資源的要求中使用範圍值。 |
| 403 | insufficient\_access | 權杖的主體沒有存取資源所需的權限。 | 提示使用者使用不同的帳戶，或要求所指定資源的權限。 |

<!---HONumber=AcomDC_0601_2016-->