---
title: Azure Active Directory B2C | Microsoft Docs
description: 如何使用 Azure Active Directory B2C 建置支援登入、註冊和設定檔管理的 Web 應用程式。
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock

---
# Azure AD B2C：建置 .NET Web 應用程式
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C 可讓您將強大的自助式身分識別管理功能加入至 Web 應用程式，只要幾個簡短步驟即可完成。本文將討論如何建立 .NET Model-View-Controller (MVC) Web 應用程式，其中包含使用者註冊、登入和設定檔管理。此應用程式將支援以使用者名稱或電子郵件及社交帳戶 (例如 Facebook 和 Google) 來註冊和登入。

## 取得 Azure AD B2C 目錄
您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄是適用於所有使用者、app、群組等項目的容器。如果您還沒有此資源，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續進行本指南。

## 建立應用程式
接著，您必須在 B2C 目錄中建立應用程式。這會提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必：

* 在應用程式中加入 **Web 應用程式/Web API**。
* 輸入 `https://localhost:44316/` 做為 [重新導向 URI]。這是此程式碼範例的預設 URL。
* 複製指派給您應用程式的**應用程式識別碼**。稍後您將會用到此資訊。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 建立您的原則
在 Azure AD B2C 中，每個使用者經驗皆由[原則](active-directory-b2c-reference-policies.md)所定義。此程式碼範例包含三種身分識別體驗：註冊、登入和編輯設定檔。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。

> [!NOTE]
> Azure AD B2C 也支援合併的註冊或登入原則 (此教學課程中未說明)。註冊或登入原則是在[此對等教學課程](active-directory-b2c-devquickstarts-web-dotnet-susi.md)中說明。
> 
> 

建立這三個原則時，請務必：

* 在身分識別提供者刀鋒視窗中，選擇 [使用者識別碼註冊] 或 [電子郵件註冊]。
* 在註冊原則中，選擇 [顯示名稱] 和其他註冊屬性。
* 在每個原則中，選擇 [顯示名稱] 宣告做為應用程式宣告。您也可以選擇其他宣告。
* 建立每個原則之後，請複製原則的**名稱**。稍後您將需要這些原則名稱。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則後，就可以開始建置您的應用程式。

## 下載程式碼和設定驗證
此範例的程式碼[保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet)。若要遵循指示建立範例，您可以[下載基本架構專案的 .zip 檔案](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)。您也可以複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

完整的範例也[以 .zip 檔案格式提供](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip)，或從相同存放庫的 `complete` 分支取得。

下載範例程式碼後，請開啟 Visual Studio .sln 檔案開始進行。

您的應用程式與 Azure AD B2C 通訊時會傳送驗證訊息，其中指定它們想要在 HTTP 要求中執行的原則。若是 .NET Web 應用程式，您可以使用 Microsoft 的 OWIN 中介軟體來傳送 OpenID Connect 驗證要求、執行原則、管理使用者工作階段等等。

首先，請使用 Visual Studio Package Manager Console，將 OWIN 中介軟體 NuGet 封裝加入至專案。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

接下來，請開啟專案根目錄中的 `web.config` 檔案，並在 `<appSettings>` 區段中輸入您應用程式的設定值，以取代現有值。

```
<configuration>
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
  </appSettings>
...
```

[!INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

接下來，將 OWIN 啟動類別加入名為 `Startup.cs` 的專案。以滑鼠右鍵按一下專案、依序選取 [新增] 和 [新增項目]，然後搜尋「OWIN」。 **請務必將類別宣告變更為 `public partial class Startup`**。我們已為您在另一個檔案中實作了此類別的一部分。OWIN 中介軟體將會在應用程式啟動時叫用 `Configuration(...)` 方法。在此方法中，請呼叫 `ConfigureAuth(...)`，為您的應用程式設定驗證。

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

開啟檔案 `App_Start\Startup.Auth.cs` 並實作 `ConfigureAuth(...)` 方法。您在 `OpenIdConnectAuthenticationOptions` 中所提供的參數會做為座標，讓您的應用程式與 Azure AD 進行通訊。您還必須設定 Cookie 驗證。OpenID Connect 中介軟體會使用 Cookie 維護使用者工作階段，此外還有其他用途。

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
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
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## 將驗證要求傳送至 Azure AD
您的應用程式現在已正確設定，將使用 OpenID Connect 驗證通訊協定與 Azure AD B2C 進行通訊。OWIN 已經處理有關製作驗證訊息、驗證 Azure AD 的權杖和維護使用者工作階段的一切細節。剩下的就是啟動每個使用者的流程。

當使用者在 Web 應用程式中選取 [註冊]、[登入] 或 [編輯設定檔] 時，`Controllers\AccountController.cs` 中會叫用相關聯的動作。在每個案例中，您可以使用內建的 OWIN 方法來觸發正確的原則：

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

您也可以在控制器中使用 `[Authorize]` 標籤，要求如果使用者未登入，則執行特定的原則。開啟 `Controllers\HomeController.cs`，將 `[Authorize]` 標籤加入宣告控制器。在叫用授權標籤時，OWIN 將會選取所設定的最後一個要執行的原則。

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

您也可以使用 OWIN 將使用者登出應用程式。在 `Controllers\AccountController.cs` 中：

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## 顯示使用者資訊
當您使用 OpenID Connect 驗證使用者時，Azure AD 會將包含**宣告**的 ID 權杖傳回給應用程式。這些是有關使用者的判斷提示。您可以使用宣告來個人化應用程式。

開啟 `Controllers\HomeController.cs` 檔案。您可以透過 `ClaimsPrincipal.Current` 安全性主體物件來存取控制器中的使用者宣告。

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

您可以用相同方式存取您的應用程式所接收的任何宣告。您可以在 [宣告] 頁面看到應用程式收到的所有宣告的清單。

## 執行範例應用程式
最後，您可以建置並執行您的應用程式。使用電子郵件地址或使用者名稱來註冊應用程式。登出，再以相同的使用者身分重新登入。編輯該使用者的設定檔。登出，再以不同的使用者身分登入。請注意，[宣告] 索引標籤上顯示的資訊對應到您在原則上設定的資訊。

## 新增社交 IDP
目前，應用程式僅支援使用者以**本機帳戶**來註冊和登入。這些是儲存在 B2C 目錄中使用使用者名稱和密碼的帳戶。藉由使用 Azure AD B2C，您可以新增對其他**身分識別提供者** (IDP) 的支援，但不需變更任何程式碼。

若要將社交 IDP 加入至應用程式，請依照下列文章中的詳細指示開始。針對您想要支援的每個 IDP，您必須在該系統中註冊應用程式並取得用戶端識別碼。

* [將 Facebook 設定為 IDP](active-directory-b2c-setup-fb-app.md)
* [將 Google 設定為 IDP](active-directory-b2c-setup-goog-app.md)
* [將 Amazon 設定為 IDP](active-directory-b2c-setup-amzn-app.md)
* [將 LinkedIn 設定為 IDP](active-directory-b2c-setup-li-app.md)

當您將身分識別提供者新增到 B2C 目錄之後，就必須分別編輯您的三個原則來包含新的 IDP，如[原則參考文章](active-directory-b2c-reference-policies.md)中所述。儲存您的原則之後，重新執行應用程式。在每一次的身分識別體驗中，您應該會看到新的 IDP 已加入成為登入和註冊選項。

您可以在範例應用程式上試驗原則並觀察其效果。新增或移除 IDP、管理應用程式宣告，或變更註冊屬性。試驗到您能夠了解原則、驗證要求和 OWIN 如何結合在一起為止。

[這裡以 .zip 檔案提供](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip)完整範例 (不含您的設定值) 供您參考。您也可以從 Github 複製它：

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->