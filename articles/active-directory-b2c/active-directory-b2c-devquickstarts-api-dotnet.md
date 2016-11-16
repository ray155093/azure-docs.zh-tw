---
title: Azure AD B2C | Microsoft Docs
description: "如何使用 Azure Active Directory B2C 建置 .NET Web API，並使用 OAuth 2.0 存取權杖進行驗證加以保護。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 370978187cffa2e5a9544bf99e6a15e13f97ac53


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C：建置 .NET Web API
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

透過 Azure Active Directory (Azure AD) B2C，您可以使用 OAuth 2.0 存取權杖來保護 Web API。 這些權杖可讓您的用戶端 app 使用 Azure AD B2C 來對 API 進行驗證。 本文將說明如何建立 .NET Model-View-Controller (MVC)「待辦事項清單」API，以便使用者進行 CRUD 工作。 Web API 會使用 Azure AD B2C 進行保護，只允許已驗證的使用者管理他們的待辦事項清單。

## <a name="create-an-azure-ad-b2c-directory"></a>建立 Azure AD B2C 目錄
您必須先建立目錄或租用戶，才可使用 Azure AD B2C。 目錄是適用於所有使用者、app、群組等項目的容器。 如果您還沒有此資源，請先 [建立 B2C 目錄](active-directory-b2c-get-started.md) ，再繼續進行本指南。

## <a name="create-an-application"></a>建立應用程式
接著，您必須在 B2C 目錄中建立應用程式。 這會提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。 若要建立應用程式，請遵循 [這些指示](active-directory-b2c-app-registration.md)。 請務必：

* 在應用程式中加入 **Web 應用程式**或 **Web API**。
* 針對 Web 應用程式使用**重新導向的統一資源識別項**`https://localhost:44316/`。 這是適用於此程式碼範例的 Web 應用程式用戶端的預設位置。
* 複製指派給您的應用程式的 **應用程式識別碼** 。 稍後您將會用到此資訊。
  
  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則
在 Azure AD B2C 中，每個使用者經驗皆由 [原則](active-directory-b2c-reference-policies.md)所定義。 此程式碼範例中的用戶端包含三個身分識別使用體驗：註冊、登入和編輯設定檔。 您必須為每個類型建立一個原則，如 [原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)中所述。 建立您的三個原則時，請務必：

* 在識別提供者刀鋒視窗中，選擇 [使用者識別碼註冊] 或 [電子郵件註冊]。
* 在註冊原則中，選擇 [顯示名稱]  和其他註冊屬性。
* 針對每個原則選擇 [顯示名稱] 和 [物件識別碼] 宣告做為應用程式宣告。 您也可以選擇其他宣告。
* 建立每個原則後，請複製原則的 [名稱]  。 稍後您將需要這些原則名稱。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立這三個原則之後，就可以開始建置您的 app。

## <a name="download-the-code"></a>下載程式碼
本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet)上。 如要依照指示建置範例，請 [下載 .zip 檔案格式的基本架構專案](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip)。 您也可以複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

完整的 App 也[提供 .zip 檔案格式](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip)，或放在相同儲存機制的 `complete` 分支中。

下載範例程式碼後，請開啟 Visual Studio .sln 檔案開始進行。 您的方案現在包含兩個專案：`TaskWebApp``TaskService`。 `TaskWebApp` 是使用者所互動的 MVC Web 應用程式。 `TaskService` 是應用程式的後端 Web API，其會儲存每位使用者的待辦事項清單。

## <a name="configure-the-task-web-app"></a>設定工作 Web 應用程式
使用者與 `TaskWebApp` 互動時，用戶端會傳送要求給 Azure AD，然後傳回可用於呼叫 `TaskService` Web API 的權杖。 若要讓使用者登入並取得權杖，您必須提供應用程式的部分相關資訊給 `TaskWebApp`。 在 `TaskWebApp` 專案中，開啟專案根目錄中的 `web.config` 檔案，然後取代 `<appSettings>` 區段中的值。  您可以將 `AadInstance`、`RedirectUri` 和 `TaskServiceUrl` 值保留原狀。

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

本文未涵蓋建置 `TaskWebApp` 用戶端。  若要了解如何使用 Azure AD B2C 建置 Web 應用程式，請參閱 [.NET Web 應用程式教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="secure-the-api"></a>保護 API
當您已有可代替使用者呼叫 API 的用戶端時，您可以使用 OAuth 2.0 持有人權杖來保護 `TaskService` 。 您的 API 可以使用 Microsoft 的 Open Web Interface for .NET (OWIN) 程式庫來接受並驗證權杖。

### <a name="install-owin"></a>安裝 OWIN
首先要安裝 OWIN OAuth 驗證管線：

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>輸入 B2C 詳細資料
開啟 `TaskService` 專案根目錄中的 `web.config` 檔案，取代 `<appSettings>` 區段中的值。 這些值將用於整個 API 和 OWIN 程式庫。  您可以讓 `AadInstance` 值保持不變。

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>新增 OWIN 啟動類別
將 OWIN 啟動類別新增至名為 `Startup.cs` 的 `TaskService` 專案。  以滑鼠右鍵按一下專案，依序選取 [新增] 和 [新增項目]，然後搜尋 OWIN。

```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>設定 OAuth 2.0 驗證
開啟檔案 `App_Start\Startup.Auth.cs` 並實作 `ConfigureAuth(...)` 方法：

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>保護工作控制器
將應用程式設定為使用 OAuth 2.0 驗證之後，只需將 `[Authorize]` 標記加入工作控制器，就能保護您的 Web API。 所有的待辦事項清單操作都會在此控制器中進行，因此您應該在類別層級上保護整個控制器。 您也可以將 `[Authorize]` 標記加入個別的動作，以進行更細微的控制。

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>從權杖取得使用者資訊
`TasksController` 會將工作儲存在資料庫中，而每個工作都有一個「擁有」此工作的相關聯使用者。 擁有者是藉由使用者的**物件識別碼**來識別。 (這就是為什麼您需要在所有原則中新增物件識別碼以做為應用程式宣告的原因)。

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>執行範例應用程式
最後，請建置並執行 `TaskWebApp` 和 `TaskService`。 使用電子郵件地址或使用者名稱來註冊應用程式。 在使用者的待辦事項清單中建立一些工作，觀察即使停止並重新啟動用戶端之後，這些工作仍持續存在 API 中。

## <a name="edit-your-policies"></a>編輯您的原則
在您使用 Azure AD B2C 來保護 API 之後，就可以測試應用程式的原則，並檢視對 API 產生的效果 (或沒有效果)。 您可以操作原則中的應用程式宣告，然後變更 Web API 中可用的使用者資訊。 如本文先前所述，您加入的任何宣告都可在 `ClaimsPrincipal` 物件中供您的 .NET MVC Web API 使用。

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Nov16_HO2-->


