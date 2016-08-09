<properties 
   pageTitle="資源管理員 API 的授權 | Microsoft Azure"
   description="用來授權 Azure Resource Manager API 與 Active Directory 以整合應用程式與 Azure 的開發人員指南。"
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/12/2016"
   ms.author="dugill;tomfitz" />


# 利用 Azure Resource Manager API 進行授權的開發人員指南

如果您的軟體開發人員想要整合應用程式與 Azure，或管理客戶的 Azure 資源，本主題說明如何使用 Azure Resource Manager API 進行驗證。

您的應用程式可透過數種方式存取資源管理員 API︰

1. **使用者 + 應用程式存取**︰當應用程式能代表已登入的使用者存取資源時，請使用這個方法。這適用於僅處理「互動式管理」Azure 資源的應用程式，例如 Web 應用程式和命令列工具。
1. **僅應用程式存取**︰如果必須將資源的直接存取權授與應用程式的身分識別，請使用這個方法。這適用於協助程式服務和已排程的工作，其長期需要 Azure「離線存取權」。

本主題逐步說明如何建立應用程式，以運用這兩種授權方法。

您將建置的 Web 應用程式以︰

1. 登入 Azure 使用者
2. 代表使用者查詢資源管理員 (使用者 + 應用程式存取)，以取得該使用者擁有的 Azure 訂用帳戶清單
3. 可讓使用者將訂用帳戶「連接」至應用程式，藉此將訂用帳戶上的直接存取權授與應用程式
4. 若要執行離線作業 (僅限應用程式存取)，請存取資源管理員應用程式

以下是您將撰寫的 Web 應用程式的端對端流程。

![ARM 驗證 - 應用程式註冊 1](./media/resource-manager-api-authentication/ARM-Auth-Swim-Lane.png)

本主題的所有程式碼都會作為 Web 應用程式執行，您可以在 [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense) 上試用。

身為使用者，您可以選取要用於登入的帳戶類型︰

![選取登入](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-1.png)

提供您的認證。

![提供認證](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-2.png)

授與應用程式存取您的 Azure 訂用帳戶︰
 
 ![授與存取權](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-3.png)
 
將您的訂用帳戶連線至應用程式進行監視︰

![連線訂用帳戶](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4.png)

中斷連接或修復應用程式的連線︰

![中斷連線訂用帳戶](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-5.png)

## 向 Azure Active Directory 註冊您的應用程式

首先，使用 Azure Active Directory (AD) 註冊您的 Web 應用程式。應用程式註冊會為您在 Azure AD 中的應用程式建立中央身分識別。它會保留您的應用程式的基本資訊，例如您的應用程式將用來驗證和存取 Azure Resource Manager API 的 OAuth 用戶端識別碼、回覆 URL 和認證。應用程式註冊也會記錄您的應用程式需要的各種委派權限，以便代表使用者存取 Microsoft API。

[使用入口網站建立 Active Directory 應用程式和服務主體](resource-group-create-service-principal-portal.md)主題顯示設定您的應用程式所需要的所有步驟。當您建立具有下列屬性的應用程式時，請參閱該主題︰

- 名為 **CloudSense** 的 Web 應用程式
- 格式 * *http://{domain_name_of_your_directory}/{name_of_the_app}** 的登入 URL 和應用程式識別碼 URI。
- 用於登入應用程式的驗證金鑰
- 委派 [Azure 服務管理 API] 的 [存取 Azure 服務管理] 權限。保留 [Azure Active Directory] 的預設值 [啟用單一登入並讀取使用者的設定檔]。
- 多租用戶應用程式

### 選擇性組態 - 憑證認證

Azure AD 也支援應用程式的憑證認證︰您建立自我簽署憑證、保留私密金鑰，然後將公開金鑰新增至 Azure AD 應用程式註冊。若為驗證，您的應用程式會使用您的私密金鑰將小裝載傳送至簽署的 Azure AD，且 Azure AD 會使用您註冊的公開金鑰來驗證簽章。

如需設定憑證的相關資訊，請參閱[在 Office 365 中建置服務與常駐應用程式](https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365)。標題為＜設定應用程式的 X.509 公開憑證＞一節有設定憑證的逐步指示。或者，請參閱[使用 Azure Resource Manager 驗證服務主體](resource-group-authenticate-service-principal.md)，例如透過 Azure PowerShell 或 Azure CLI 設定憑證的範例。

## 驗證使用者並取得存取權杖

您現在已具備開始編碼應用程式的所有條件。本主題提供端對端流程之每個步驟的 REST API 範例，及每個步驟的相關 C# 程式碼的連結。完整的 ASP.NET MVC 應用程式範例可在 [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense) 取得。

您在使用者決定將其 Azure 訂用帳戶連線至您應用程式的時點開始。

您必須要求使用者兩件事︰

1. **目錄網域名稱**︰使用者的 Azure 訂用帳戶相關聯的 Azure Active Directory 的網域名稱。OAuth 2.0 授權要求必須傳送至此 Azure AD。使用者可瀏覽到 Azure 入口網站，並選取右上角的帳戶，以瞭解其 Azure AD 的網域名稱。您可以將視覺化指示提供給使用者，如︰

     ![](./media/resource-manager-api-authentication/show-directory.png)
   
1. **Microsoft 帳戶與工作帳戶**︰判斷使用者使用 Microsoft 帳戶 (也稱為 Live Id) 或工作帳戶 (也稱為組織帳戶) 來管理其 Azure 訂用帳戶。若是 Microsoft 帳戶，您的應用程式會使用查詢字串參數 (&domain\_hint=live.com) 將使用者重新導向至 Azure Active Directory 登入頁面，它會指示 Azure AD 將使用者直接帶到 Microsoft 帳戶登入頁面。會以相同的方式處理您為任一類帳戶接收的授權碼和權杖。

您的應用程式接著會使用 OAuth 2.0 授權要求，將使用者重新導向到 Azure AD - 以驗證使用者的認證及取回授權碼。應用程式將使用授權碼來存取資源管理員的權杖。

### 驗證要求 (OAuth 2.0)

將開啟識別碼連線/OAuth2.0 授權要求發給 Azure AD 授權端點︰

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Authorize

[授權碼授與流程](https://msdn.microsoft.com/library/azure/dn645542.aspx)主題說明適用於此要求的查詢字串參數。

下列範例示範如何要求 OAuth2.0 授權︰

    https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD 驗證使用者，並在需要時要求使用者將權限授與應用程式。它會將授權碼傳回至應用程式的回覆 URL。根據要求的 response\_mode，Azure AD 會在查詢字串中傳回資料，或作為張貼資料。

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### 驗證要求 (Open ID Connect)

如果您不只想要代表使用者存取 Azure Resource Manager，並且允許使用者使用其 Azure AD 帳戶登入您的應用程式，請發出 Open ID Connect 授權要求。使用 Open ID Connect，您的應用程式也會收到 Azure AD 的 id\_token，讓應用程式用來登入使用者。

OAuth2.0 授權要求查詢字串參數為︰

| QS 參數 | 值
|----|----
| client\_id | 您的應用程式的用戶端識別碼
| response\_mode | **form\_post** 或 **query**
| response\_type | **code+id\_token**
| redirect\_uri | 您的應用程式的 URL 編碼的回覆 URL。例如：http://www.vipswapper.com/cloudsense/Account/SignIn |
| resource | Azure 服務管理 API 的 URL 編碼識別碼︰https://management.core.windows.net/ |
| scope | openid+profile
| nonce | 用來將授權要求繫結傳回之 id\_token 的資料片段，以確保授權回應明確並且不會重新播放。
| domain\_hint | live.com <br />**注意**：只有當使用者使用 Microsoft 帳戶來管理其 Azure 訂用帳戶時，才使用 domain\_hint 參數。
| state | 選擇性地指定您要 Azure AD 與回應一起傳回的任何狀態資料。

Open ID Connect 的要求範例是︰

     https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD 驗證使用者，並在需要時要求使用者將權限授與應用程式。它會將授權碼傳回至應用程式的回覆 URL。根據要求的 response\_mode，Azure AD 會在查詢字串中傳回資料，或作為張貼資料。

Open ID Connect 回應的範例是︰

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### 驗證 id\_token

將您的應用程式登入使用者之前，它必須先驗證 id\_token。權杖驗證是相關的主題，我建議您針對您的開發平台使用標準的 JSON Web 權杖處理常式程式庫 (請參閱 [.NET Azure AD JWT 處理常式的原始程式碼](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs))。這表示應用程式的安全性是您的責任，因此請確定您用於正確處理 id\_token 的程式庫會驗證權杖的下列層面︰

- **權杖的時機**︰檢查 nbf 和 exp 宣告以確保權杖不會太新或太舊。這是慣用作法，會保留一些寬限 (5 分鐘) 以容納時間差異。
- **簽發者**︰檢查 iss 宣告以確保權杖簽發者是 Azure Active Directory：https://sts.windows.net/{tenant_id_of_the_directory}
- **觀眾**︰檢查 aud 宣告以確保已為您的應用程式新建權杖。值必須是您的應用程式的用戶端識別碼。
- **Nonce**︰檢查 nonce 宣告以檢查您在授權要求中傳送的 nonce 資料，以確保您的應用程式已新建回應，且並未重新播放權杖。
- **簽章**︰您的應用程式必須確認權杖已由 Azure Active Directory 簽署。Azure AD 簽署金鑰經常捲動，因此您的應用程式必須每日輪詢重新整理的金鑰，或在簽章驗證失敗時錯誤移轉至重新整理的金鑰。如需詳細資訊，請參閱 [Azure AD 中簽署金鑰變換的相關重要資訊](active-directory/active-directory-signing-key-rollover.md)。

驗證 **id\_token** 後，使用 oid 宣告值作為使用者的不變且不可重複使用的識別碼。將 **unique\_name** 或 upn/email 宣告作為人類可讀取的使用者顯示名稱。您也可以針對顯示用途使用選擇性的 given\_name/family\_name 宣告。

### 權杖要求 (OAuth2.0 程式碼授與流程)

您的應用程式現在已從 Azure AD 收到授權碼，現在是取得 Azure Resource Manager 的存取權杖的時候了。將 OAuth2.0 程式碼授與權杖要求張貼至 Azure AD 權杖端點︰

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

[授權碼授與流程](https://msdn.microsoft.com/library/azure/dn645542.aspx)主題說明適用於此要求的查詢字串參數。

下列範例顯示如何使用密碼認證來要求程式碼授與權杖：

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

使用憑證認證時，使用您的應用程式的憑證認證的私密金鑰來建立 JSON Web 權杖 (JWT) 和登入 (RSA SHA256)。權杖的宣告類型會顯示在[授權碼授與流程](https://msdn.microsoft.com/library/azure/dn645542.aspx)。如需參考，請參閱 [Active Directory 驗證程式庫 (.NET) 程式碼](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/master/src/ADAL.NET/CryptographyHelper.cs)來簽署用戶端判斷提示 JWT 權杖。

請參閱 [Open ID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)來取得用戶端驗證的詳細資訊。以下是[範例用戶端判斷提示 JWT 權杖](https://www.authnauthz.com/OAuth/ParseJWTToken?token=eyJhbGciOiJSUzI1NiIsIng1dCI6IlFwcXdKZnJNZ003ekJ4M1hkM2NSSFdkYVFsTSJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ud2luZG93cy5uZXRcL2FhbHRlc3RzLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQyODk2Mjk5MSwiaXNzIjoiOTA4M2NjYjgtOGE0Ni00M2U3LTg0MzktMWQ2OTZkZjk4NGFlIiwianRpIjoiMmYyMjczMzQtZGQ3YS00NzZkLWFlOTYtYzg4NDQ4YTkxZGM0IiwibmJmIjoxNDI4OTYyMzkxLCJzdWIiOiI5MDgzY2NiOC04YTQ2LTQzZTctODQzOS0xZDY5NmRmOTg0YWUifQ.UXQE9H-FlwxYQmRVG0-p7pAX9TFgiRXcYr7GhbcC7ndIPHKpZ5tfHWPEgBl3ZVRvF2l8uA7HEV86T7t2w7OHhHwLBoW7XTgj-17hnV1CY21MwjrebPjaPIVITiilekKiBASfW2pmss3MjeOYcnBV2MuUnIgt4A_iUbF_-opRivgI4TFT4n17_3VPlChcU8zJqAMpt3TcAxC3EXXfh10Mw0qFfdZKqQOQxKHjnL8y7Of9xeB9BBD_b22JNRv0m7s0cYRx2Cz0cUUHw-ipHhWaW7YwhVRMfK6BMkaDUgaie4zFkcgHb7rm1z0rM1CvzIqP-Mwu3oEqYpY9cYo8nEjMyA)。

下列範例顯示如何使用憑證認證來要求程式碼授與權杖：

	POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
	
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 1012
	
	grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

程式碼授與權杖回應的範例︰

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### 處理程式碼授與權杖回應

成功的權杖回應會包含 Azure Resource Manager 的 (使用者 + 應用程式) 存取權杖。應用程式將使用此存取權杖來代表使用者存取資源管理員。Azure AD 所發出的存取權杖存留期是一小時。Web 應用程式不太可能必須更新 (使用者 + 應用程式) 存取權杖；但如果需要，您可以使用您的應用程式在權杖回應中收到的重新整理權杖。將 OAuth2.0 權杖要求張貼至 Azure AD 權杖端點︰

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

使用重新整理要求的參數說明於[授權碼授與流程](https://msdn.microsoft.com/library/azure/dn645542.aspx)。

下列範例顯示如何使用重新整理權杖：

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

請注意，雖然重新整理權杖可用來取得 Azure Resource Manager 的新存取權杖，但它們並不適合您的應用程式離線存取。重新整理權杖存留期有限，且重新整理權杖會繫結至使用者。如果使用者離開組織，使用重新整理權杖的應用程式將無法存取。這個方法不適合小組用來管理其 Azure 資源的應用程式。

## 列出可用的訂用帳戶

您的應用程式現在具有權杖，可代表使用者存取 Azure Resource Manager。

體驗的下一步是讓使用者將其 Azure 訂用帳戶連接到您的應用程式，如此即使使用者不存在，您的應用程式也可以管理這些訂用帳戶 (長期離線存取)。向使用者顯示其可以管理存取權的 Azure 訂用帳戶清單，並允許使用者將 RBAC 角色直接指派給應用程式的身分識別。

![ARM 驗證 - 範例應用程式 Ux 4](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4-full.png)

### 列出使用者擁有任何存取權的訂用帳戶

我們應該先呼叫[資源管理員列出訂用帳戶](https://msdn.microsoft.com/library/azure/dn790531.aspx) API，以列出使用者擁有任何種類之存取權的所有訂用帳戶。然後我們會識別使用者可管理存取權的訂用帳戶。

ASP.net MVC 範例應用程式的 [GetUserSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L79) 方法會實作這個呼叫。

要求列出訂用帳戶的範例是︰

    GET https://management.azure.com/subscriptions?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

回應列出訂用帳戶的範例是︰

    HTTP/1.1 200 OK

    {"value":[{"id":"/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2","subscriptionId":"34370e90-ac4a-4bf9-821f-85eeedeae1a2","displayName":"Sandbox","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}},{"id":"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e","subscriptionId":"c276fc76-9cd4-44c9-99a7-4fd71546436e","displayName":"Production","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}}]}

### 取得使用者在訂用帳戶上的權限

只能為使用者可管理存取權的訂用帳戶顯示連線/中斷連線動作。針對每個訂用帳戶，您會呼叫[資源管理員列出權限](https://msdn.microsoft.com/library/azure/dn906889.aspx) API 來判斷使用者是否具有訂用帳戶的存取管理權限。

ASP.net MVC 範例應用程式的 [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L132) 方法會實作這個呼叫。

下列範例示範如何要求使用者在訂用帳戶上的權限。83cfe939-2402-4581-b761-4f59b0a041e4 是訂用帳戶的識別碼。

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

以下是針對取得使用者在訂用帳戶上的權限所做的回應範例︰

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

權限 API 會傳回多個權限。每個權限包含允許的動作 (actions) 和不允許的動作 (notactions)。如果動作出現在任何權限的允許 actions 清單中，而且不存在於該權限的 notactions 清單，則使用者可執行該動作。**microsoft.authorization/roleassignments/write** 是授與存取管理權限的動作。您的應用程式必須剖析權限結果，才能在每個權限的 actions 和 notactions 中的此動作字串上，尋找 regex 相符項。

### 選擇性︰列出存在使用者帳戶的目錄

使用者的帳戶可以存在多個 Azure Active Directory 中。使用者一開始可能未指定正確的目錄名稱 - 在此情況下，所需的訂用帳戶不會顯示在清單中。

[資源管理員清單租用戶](https://msdn.microsoft.com/library/azure/dn790536.aspx) API 會列出存在使用者帳戶的所有目錄的身分識別清單。您可以呼叫 API 來判斷使用者的帳戶是否在多個目錄中，並選擇性地為使用者顯示訊息，如「找不到您要尋找的訂用帳戶？ 可能位於您不屬於的其他 Azure Active Directory 中。按一下這裡以切換目錄。」

ASP.NET MVC 範例應用程式的 [GetUserOrganizations](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) 方法會實作這個呼叫。

要求列出目錄的範例是︰

    GET https://management.azure.com/tenants?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1Qi****8DJf1UO4a-ZZ_TJmWFlwO1mM7Cw6JWtfY2lGc5A

回應列出目錄的範例是︰

    HTTP/1.1 200 OK

    {"value":[{"id":"/tenants/7fe877e6-a150-4992-bbfe-f517e304dfa0","tenantId":"7fe877e6-a150-4992-bbfe-f517e304dfa0"},{"id":"/tenants/62e173e9-301e-423e-bcd4-29121ec1aa24","tenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24"}]}

## 將訂用帳戶連接到應用程式

您現在有一份使用者可連接到您應用程式的 Azure 訂用帳戶清單。下一步是授與使用者命令來建立連線。當使用者選取 [連接] 時，您的應用程式︰

1. 將適當的 RBAC 角色指派給您的應用程式在訂用帳戶上的身分識別。
2. 查詢訂用帳戶上的應用程式權限，或使用僅限應用程式權杖來存取資源管理員，藉此驗證存取指派。
1. 記錄應用程式「已連線的訂用帳戶」資料結構中的連線 - 保存訂用帳戶的識別碼。

讓我們更仔細檢視第一個步驟︰若要將適當的 RBAC 角色指派給應用程式身分識別，您必須決定︰

- 您在使用者的 Azure Active Directory 中的應用程式身分識別的物件識別碼
- 您的應用程式在訂用帳戶上需要的 RBAC 角色的識別碼

我們將深入探討第一個部分︰當您的應用程式第一次驗證來自 Azure AD 的使用者時，會在 Azure AD 中建立應用程式的服務主體物件。Azure 允許將 RBAC 角色指派給服務主體，以便將直接存取權授與 Azure 資源上的對應應用程式。這就我們要做的 - 讓我們開始查詢 Azure AD Graph API，判斷應用程式在登入使用者的 Azure AD 中的服務主體識別項。

您只有 Azure Resource Manager 的存取權杖 - 您需要新的存取權杖以呼叫 Azure AD Graph API。Azure AD 中的每個應用程式都有權查詢其本身的服務主體物件，因此我們不需要使用者 + 應用程式存取權杖就能夠執行此工作，僅限應用程式存取權杖已足夠。

<a id="app-azure-ad-graph">
### 取得 Azure AD Graph API 的僅限應用程式存取權杖

若要驗證您的應用程式，並取得 Azure AD Graph API 的權杖，請向 Azure AD 權杖端點發出用戶端認證授與 OAuth2.0 流程權杖要求 (**http://login.microsoftonline.com/{directory\_domain\_name}/OAuth2/Token**)。

ASP.net MVC 範例應用程式的 [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L73) 方法的 73-77 行使用 Active Directory Authentication Library for .NET，以取得 Graph API 的僅限應用程式存取權杖。

用戶端認證授與權杖要求資料 ︰

| 元素 | 值
|----|----
| grant\_type | **client\_credentials**
| client\_id | 您的應用程式的用戶端識別碼
| resource | 正在針對存取權杖要求之資源的 URL 編碼識別碼。在此案例中為 Azure AD 圖形 API 的識別碼：**https://graph.windows.net/**
| client\_secret 或 client\_assertion\_type + client\_assertion | 如果您的應用程式使用密碼認證，請使用 client\_secret。如果您的應用程式使用憑證認證，請使用 client\_assertion。

用戶端認證授與權杖要求範例︰

    POST https://login.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

用戶端認證授與權杖回應範例︰

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### 取得使用者 Azure AD 中的應用程式服務主體的 ObjectId

現在，使用僅限應用程式存取權杖來查詢 [Azure AD Graph 服務主體](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API，以判斷目錄中應用程式的服務主體的物件識別碼。

ASP.net MVC 範例應用程式的 [GetObjectIdOfServicePrincipalInOrganiation](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L66) 方法會實作這個呼叫。

下列範例示範如何要求應用程式的服務主體。a0448380-c346-4f9f-b897-c18733de9394 是應用程式的用戶端識別碼。

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

下列範例顯示應用程式的服務主體要求的回應

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### 取得 Azure RBAC 角色識別碼

您必須將適當的 RBAC 角色，指派給應用程式在所選訂用帳戶上的服務主體。因此，您必須判斷 Azure RBAC 角色的識別碼。

您的應用程式的正確 RBAC 角色︰

- 如果您的應用程式只會監視訂用帳戶而不進行任何變更，它只需要訂用帳戶上的讀取器權限。指派**讀取器**角色。
- 如果您的應用程式管理 Azure 訂用帳戶，建立/修改/刪除實體，它將需要其中一個參與者權限。
  - 若要管理特定類型的資源，請指派資源的特定參與者角色 (虛擬機器參與者、虛擬網路參與者、儲存體帳戶參與者等)
  - 若要管理任何資源類型，請指派**參與者**角色。

您應用程式的角色指派會對使用者顯示，因此請選取必要的最低權限。

呼叫[資源管理員角色定義 API](https://msdn.microsoft.com/library/azure/dn906879.aspx) 來列出所有 Azure RBAC 角色和搜尋，然後逐一查看結果，按名稱尋找所需的角色定義。

ASP.net MVC 範例應用程式的 [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L354) 方法會實作這個呼叫。

下列要求範例示範如何取得 Azure RBAC 角色識別碼。09cbd307-aa71-4aca-b346-5f253e6e3ebb 是訂用帳戶的識別碼。

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

回應格式如下：

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

請注意，您不需要持續呼叫此 API。一旦決定角色定義的已知 GUID，您可以將角色定義識別碼建構為︰

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

以下是常用的內建角色的知名 guid：

| 角色 | Guid |
| ----- | ------ |
| 讀取者 | acdd72a7-3385-48ef-bd42-f606fba81ae7
| 參與者 | b24988ac-6180-42a0-ab88-20f7382dd24c
| 虛擬機器參與者 | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| 虛擬網路參與者 | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| 儲存體帳戶參與者 | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| 網站參與者 | de139f84-1756-47ae-9be6-808fbbe84772
| Web 方案參與者 | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server 參與者 | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| SQL DB 參與者 | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### 將 RBAC 角色指派給應用程式

您已具備所有條件，可使用[資源管理員建立角色指派](https://msdn.microsoft.com/library/azure/dn906887.aspx) API，將適當的 RBAC 角色指派給應用程式在所選訂用帳戶上的服務主體。

ASP.net MVC 範例應用程式的[GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L269) 方法會實作這個呼叫。

將 RBAC 角色指派給應用程式的要求範例︰

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2014-10-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

在要求中，使用下列值︰

| Guid | 說明 |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | 訂用帳戶的識別碼
| c3097b31-7309-4c59-b4e3-770f8406bad2 | 應用程式的服務主體的物件識別碼
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | 讀取器 RBAC 角色的知名 guid
| 4f87261d-2816-465d-8311-70a27558df4c | 為新角色指派建立的新 guid

回應格式如下：

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### 取得 Azure Resource Manager 的僅限應用程式存取權杖

下一步是驗證該應用程式在訂用帳戶上具有所需的存取權。對於這項工作，您應該使用 Azure Resource Manager 的僅限應用程式權杖，在訂用帳戶上執行測試工作。測試工作應驗證您的應用程式在訂用帳戶確實具有所需的存取權，以執行離線監視/管理。

要取得 Azure Resource Manager 的僅限應用程式存取權杖，請依照[取得 Azure AD Graph API 的僅限應用程式存取權杖](#app-azure-ad-graph)的指示，為資源參數使用不同值︰

    https://management.core.windows.net/

ASP.net MVC 範例應用程式的 [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) 方法的 210-214 行使用 Active Directory Authentication Library for .net，以取得 Azure Resource Manager 的僅限應用程式存取權杖。

#### 取得訂用帳戶上的應用程式權限

若要檢查應用程式在 Azure 訂用帳戶上是否具有必要的存取權，您也可以呼叫[資源管理員列出權限](https://msdn.microsoft.com/library/azure/dn906889.aspx) API，以類似的方式判斷使用者是否具有訂用帳戶的存取管理權限。不過，此時會使用您在上一個步驟中收到的僅限應用程式存取權杖來呼叫 API 權限。

ASP.NET MVC 範例應用程式的 [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) 方法會實作這個呼叫。

## 管理連接的訂用帳戶

將適當的 RBAC 角色指派給應用程式在訂用帳戶上的服務主體時，您的應用程式可使用 Azure Resource Manager 的僅限應用程式存取權杖以持續監視/管理它。

如果訂閱擁有者使用 Azure 管理入口網站或命令列工具來移除應用程式的角色指派，您的應用程式將無法再存取該訂用帳戶。在此情況下，您應該通知使用者：應用程式外部的訂用帳戶連線已中斷，然後提供他們 [修復] 連線的選項。[修復] 只會重新建立離線刪除的角色指派。


## 中斷連線訂用帳戶

就像您讓使用者將其訂用帳戶連接到您的應用程式，您也必須允許使用者中斷連線訂用帳戶。從存取管理的觀點而言，中斷連線意味移除應用程式的服務主體已在訂用帳戶上的角色指派。(選擇性) 也可能移除訂用帳戶的應用程式中的任何狀態。只有在訂用帳戶上具有存取管理權限的使用者能夠中斷連線訂用帳戶。

ASP.net MVC 範例應用程式的 [RevokeRoleFromServicePrincipalOnSubscription 方法](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L303)會實作這個呼叫。

好了，使用者現在能使用您的應用程式來輕鬆連接及管理其 Azure 訂用帳戶。

<!---HONumber=AcomDC_0727_2016-->