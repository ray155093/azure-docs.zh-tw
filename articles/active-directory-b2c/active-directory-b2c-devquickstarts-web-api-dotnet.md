---
title: Azure Active Directory B2C | Microsoft Docs
description: "如何利用 Azure Active Directory B2C 來建置會呼叫 Web API 的 Web 應用程式。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: b235c7a773b1c7c2a5f4d5825c6a20c1b0afb7fb


---
# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure AD B2C：從 .NET Web 應用程式呼叫 Web API
如果您利用 Azure Active Directory (Azure AD) B2C，只要幾個簡短的步驟，就在您的 Web 應用程式及 Web API 中新增功能強大的自助式身分識別管理功能。 本文章探討如何建立會利用持有人權杖來呼叫 Web API 的 .NET Model-View-Controller (MVC)「待辦事項清單」Web 應用程式

本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔。 而會著重在如何在使用者已通過驗證後呼叫 Web API。 如果您還沒進行 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md) ，請您先進行該教學課程，以便了解 Azure AD B2C 的基本概念。

## <a name="get-an-azure-ad-b2c-directory"></a>取得 Azure AD B2C 目錄
您必須先建立目錄或租用戶，才可使用 Azure AD B2C。  目錄就是您所有使用者、應用程式、群組等項目的容器。  如果您還沒有此資源，請先 [建立 B2C 目錄](active-directory-b2c-get-started.md) ，再繼續進行本指南。

## <a name="create-an-application"></a>建立應用程式
接著，您必須在 B2C 目錄中建立應用程式。 這會提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。 在此案例中，由於 Web 應用程式及 Web API 會構成一個邏輯應用程式，因此兩者都是由單一 **應用程式識別碼**來表示。 如果要建立應用程式，請遵循 [這些指示](active-directory-b2c-app-registration.md)。 請務必：

* 在應用程式中加入 **Web 應用程式/Web API** 。
* 在 [回覆 URL] 中輸入 `https://localhost:44316/`。 這是此程式碼範例的預設 URL。
* 複製指派給您應用程式的 [應用程式識別碼]  。 稍後您也會需要此資訊。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則
在 Azure AD B2C 中，每個使用者經驗皆由 [原則](active-directory-b2c-reference-policies.md)所定義。 這個 Web 應用程式包含三種身分識別體驗：註冊、登入及編輯設定檔。 您必須為每個類型建立一個原則，如 [原則參考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)所述。 建立這三個原則時，請務必：

* 在註冊原則中，選擇 [顯示名稱]  和其他註冊屬性。
* 在每個原則中，選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。 您也可以選擇其他宣告。
* 在您建立每個原則之後，請複製原則的 [名稱]  。 其前置詞應該為 `b2c_1_`。 稍後您將需要這些原則名稱。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則後，就可以開始建置您的應用程式。

請注意，本文不涵蓋如何使用您剛才建立的原則。 如需了解 Azure AD B2C 中原則的運作方式，請從 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

## <a name="download-the-code"></a>下載程式碼
本教學課程的程式碼 [保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet)。 若要遵循指示建立範例，您可以 [下載基本架構專案的 .zip 檔案](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip)。 您也可以複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

完整的 App 也[提供 .zip 檔案格式](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)，或放在相同儲存機制的 `complete` 分支中。

下載範例程式碼後，請開啟 Visual Studio .sln 檔案開始進行。

## <a name="configure-the-task-web-app"></a>設定工作 Web 應用程式
如要讓 `TaskWebApp` 與 Azure AD B2C 通訊，您必須提供部分常用的參數。 在 `TaskWebApp` 專案中，開啟專案根目錄中的 `web.config` 檔案，然後取代 `<appSettings>` 區段中的值。 您可以將 `AadInstance`、`RedirectUri` 和 `TaskServiceUrl` 值保留原狀。

```
  <appSettings>

    ...

    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>取得存取權杖和呼叫工作 API
本節將討論如何使用登入期間所收到的權杖與 Azure AD B2C，以便存取也是使用 Azure AD B2C 所保護的 Web API。

本文沒有如何保護 API 的詳細資料。 如要了解 Web API 如何利用 Azure AD B2C 來安全地驗證要求，請查看 [Web API 開始使用文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

### <a name="save-the-sign-in-token"></a>儲存登入權杖
首先，請驗證使用者 (使用您的任何一個原則)，並從 Azure AD B2C 接收登入權杖。  如果您不確定如何執行原則，請先回來查看 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md) ，以了解 Azure AD B2C 的基本概念。

開啟檔案 `App_Start\Startup.Auth.cs`。  您必須對 `OpenIdConnectAuthenticationOptions` 進行一項重要變更 - 您必須設定 `SaveSignInToken = true`。

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",

        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>在控制器中取得權杖
`TasksController` 負責與 Web API 通訊，並傳送 HTTP 要求給 API 來讀取、建立和刪除工作。  因為 API 受到 Azure AD B2C 的保護，您必須先擷取您在上面的步驟中儲存的權杖。

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;

    ...
}
```

`BootstrapContext` 包含您藉由執行您的其中一個 B2C 原則所獲得的登入權杖。

### <a name="read-tasks-from-the-web-api"></a>從 Web API 讀取工作
當您擁有權杖之後，就可以把權杖附加 HTTP `GET` 要求的 `Authorization` 標頭中，以安全地呼叫 `TaskService`：

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>在 Web API 上建立及刪除工作
請遵循與您傳送 `POST` 和 `DELETE` 要求給 Web API 時的相同模式，使用 `BootstrapContext` 擷取登入權杖。 我們已經為您實作了建立動作， 您可以嘗試在 `TasksController.cs` 中完成刪除動作。

## <a name="run-the-sample-app"></a>執行範例應用程式
最後，建置並執行應用程式。 註冊和登入，並為登入的使用者建立工作。 請登出應用程式，再以不同的使用者身分登入， 然後為該使用者建立工作。 您會發現，這些工作在 API 上是依照使用者來儲存的 ，因為 API 會從自己收到的權杖中擷取使用者的身分識別。

為供您參考，我們提供 [.zip 檔案格式](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)的完整範例。 您也可以從 Github 複製它：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->



<!--HONumber=Dec16_HO4-->


