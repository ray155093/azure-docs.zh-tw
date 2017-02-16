---
title: "Azure AD .NET 入門 | Microsoft Docs"
description: "如何建立可整合 Azure AD 以進行驗證和授權的 .NET MVC Web API。"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: c401d473d3951ed5853170e8761a09a915329164


---
# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>使用 Azure AD 的持有者權杖來保護 Web API
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

如果您要建置可讓您存取受保護資源的應用程式，您必須知道如何保護這些資源以防止遭受不當存取。
只需幾行的程式碼，Azure AD 便可讓使用 OAuth Bearer 2.0 存取權杖來保護 Web API 變得既簡單又直接。

在 Asp.NET Web 應用程式中，您可以使用 Microsoft 的社群導向 OWIN 中介軟體 (隨附於 .NET Framework 4.5) 實作來完成這個作業。  這裡會使用 OWIN 建置「待辦事項清單」Web API：

* 指定要保護哪一個 API。
* 驗證 Web API 呼叫是否包含有效的存取權杖。

若要執行此作業，您需要執行下列動作：

1. 向 Azure AD 註冊應用程式
2. 設定您的應用程式使用 OWIN 驗證管線。
3. 設定用戶端應用程式呼叫待辦事項清單 Web API

若要開始使用，請[下載應用程式基本架構](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)或[下載完整的範例](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)。  每一個都是 Visual Studio 2013 解決方案。  您還需要一個可以註冊應用程式的 Azure AD 租用戶。  如果您還沒有租用戶， [了解如何取得租用戶](active-directory-howto-tenant.md)。

## <a name="1----register-an-application-with-azure-ad"></a>1.  向 Azure AD 註冊應用程式
若要保護您的應用程式，您首先必須在您的租用戶中建立應用程式，並提供 Azure AD 幾個重要的關鍵資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端列上按一下您的帳戶，然後在 [目錄] 清單底下，選擇您要註冊應用程式的 Active Directory 租用戶。
3. 按一下左側瀏覽區中的 [更多服務]，然後選擇 [Azure Active Directory]。
4. 按一下 [應用程式註冊]，然後選擇 [新增]。
5. 遵照提示進行，並建立新的 **Web 應用程式和/或 WebAPI**。
  * 應用程式的 [ **名稱** ] 將對使用者說明您的應用程式。  輸入「待辦事項清單服務」。
  * 「 **重新導向 Uri** 」是配置和字串的組合，Azure AD 可用它來傳回應用程式要求的任何權杖。 請為此值輸入 `https://localhost:44321/` 。
  * 針對 [AppID URI] 欄位，輸入租用戶特定的識別碼，例如 `https://contoso.onmicrosoft.com/TodoListService`
6. 儲存組態。  讓入口網站保持在開啟狀態，您也必須在短時間內註冊用戶端應用程式。

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>2.將您的應用程式設定為使用 OWIN 驗證管道
既然您已經向 Azure AD 註冊應用程式，您必須設定應用程式與 Azure AD 進行通訊以便驗證連入要求和權杖。

* 若要開始，請開啟解決方案，並將 OWIN 中介軟體 NuGet 封裝加入至使用封裝管理員主控台的 TodoListService 專案。

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

* 將 OWIN 啟動類別加入至名為 `Startup.cs`的 TodoListService 專案。  以滑鼠右鍵按一下專案 --> [新增]  -->  [新增項目] --> 搜尋 "OWIN"。  OWIN 中介軟體將會在應用程式啟動時叫用 `Configuration(…)` 方法。
* 將類別宣告變更為 `public partial class Startup`，我們已為您在另一個檔案中實作了此類別的一部分。  在 `Configuration(…)` 方法中，請呼叫 ConfgureAuth(...) 以設定您的 Web 應用程式驗證。

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* 開啟檔案 `App_Start\Startup.Auth.cs` 並實作 `ConfigureAuth(…)` 方法。  您在 `WindowsAzureActiveDirectoryBearerAuthenticationOptions` 中所提供的參數將會做為您的應用程式與 Azure AD 進行通訊的座標使用。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

* 現在，您可以使用 `[Authorize]` 屬性並搭配 JWT 承載驗證來保護您的控制器和動作。  使用授權標記裝飾 `Controllers\TodoListController.cs` 類別。  這樣會強制使用者在存取該頁面之前必須先登入。

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* 當授權的呼叫者成功叫用其中一個 `TodoListController` API 時，此動作可能需要存取呼叫者的相關資訊。  OWIN 可讓您透過 `ClaimsPrincpal` 物件存取持有人權杖內部的宣告。  
* Web API 的一個常見需求是驗證權杖中是否有「範圍」存在，這可確保使用者已經取得存取 Todo 清單服務所需權限的同意：

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

* 最後，請開啟 TodoListService 專案根目錄中的 `web.config` 檔案，並在 [`<appSettings>`] 區段中輸入您的設定值。
  * `ida:Tenant` 是指您的 Azure AD 租用戶名稱，例如 "contoso.onmicrosoft.com"。
  * 您的 `ida:Audience` 是指您在 Azure 入口網站中為應用程式輸入的應用程式識別碼 URI。

## <a name="3-configure-a-client-application--run-the-service"></a>3.設定用戶端應用程式並執行服務
在看到 Todo 清單服務運作之前，您需要設定 Todo 清單用戶端，以便它可以取得 AAD 的權杖，並對服務進行呼叫。

* 瀏覽回 [Azure 入口網站](https://portal.azure.com)
* 在 Azure AD 租用戶中建立新的應用程式，然後在產生的提示中選取 [ **原生用戶端應用程式** ]。
  * 應用程式的 [ **名稱** ] 將對使用者說明您的應用程式
  * 請輸入 `http://TodoListClient/` 作為 [重新導向 Uri] 的值。
* 完成註冊後，AAD 會為您的應用程式指派一個唯一的「應用程式識別碼」。 您會在後續小節中用到這個值，所以請從應用程式頁面中複製此值。
* 從 [設定] 頁面，選擇 [必要權限]，然後選擇 [加入]。  找出並選取 [TodoListService]，然後在 [委派的權限] 底下新增 [存取 TodoListService] 權限，接著選擇 [完成]。

* 在 Visual Studio 中，請開啟 TodoListClient 專案中的 `App.config`，並在 [`<appSettings>`] 區段中輸入您的設定值。
  
  * `ida:Tenant` 是指您的 Azure AD 租用戶名稱，例如 "contoso.onmicrosoft.com"。
  * 您從 Azure 入口網站複製的 `ida:ClientId` 應用程式識別碼。
  * 您的 `todo:TodoListResourceId` 是指您在 Azure 入口網站中為待辦事項清單服務應用程式輸入的應用程式識別碼 URI。

最後，清除、建置並執行每個專案！  如果您還沒有這麼做，現在正是使用 *.onmicrosoft.com 網域在租用戶中建立新使用者的好時機。  使用該名使用者登入待辦事項清單用戶端，並在使用者的待辦清單中加入一些工作。

[這裡](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)提供完成的範例供您參考 (不含您的設定值)。  您現在可以繼續更多的其他身分識別案例。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


