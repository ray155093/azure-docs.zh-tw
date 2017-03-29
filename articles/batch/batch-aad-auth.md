---
title: "使用 Azure Active Directory 從 Azure Batch 進行驗證 | Microsoft Docs"
description: "Batch 支援 Azure AD 從 Batch 服務和 Batch 資源提供者進行驗證。"
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/16/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 28665625cde9c7b164986f7b2d9c845136543aba
ms.lasthandoff: 03/18/2017

---

# <a name="authenticate-from-batch-solutions-with-active-directory"></a>使用 Active Directory 從 Batch 解決方案進行驗證

Azure Batch 針對 Batch 服務和 Batch 管理服務使用 [Azure Active Directory][aad_about] (Azure AD) 支援驗證。 Azure AD 是 Microsoft 的多租用戶雲端型目錄和身分識別管理服務。 Azure 本身會使用 Azure AD 來驗證其客戶、服務管理員和組織的使用者。

在本文中，我們將探討從使用 Batch 管理 .NET 程式庫或 Batch .NET 程式庫的應用程式使用 Azure AD 來進行驗證。 在 Batch .NET API 的情況中，我們會說明如何利用整合式驗證，使用 Azure AD 訂用帳戶管理員或共同管理員進行驗證。 已驗證的使用者接著可以發出要求至 Azure Batch。

此外，也可以使用 Azure AD 驗證自動執行應用程式的存取。 這裡我們將重點放在使用 Azure AD 整合式驗證並介紹其他資源，以了解驗證自動執行應用程式。

## <a name="use-azure-ad-with-batch-management-solutions"></a>搭配 Batch 管理解決方案使用 Azure AD

Batch 管理 .NET 程式庫會公開使用 Batch 帳戶、帳戶金鑰、應用程式和應用程式封裝的類型。 Batch Management .NET 程式庫是 Azure 資源提供者用戶端，並與 [Azure Resource Manager][resman_overview] 搭配使用，以程式設計方式管理這些資源。 

驗證透過任何 Azure 資源提供者用戶端 (包括 Batch Management .NET 程式庫)，以及透過 [Azure Resource Manager][resman_overview] 所提出的要求時，都需要 Azure AD。

在本節中，我們使用 GitHub 提供的 [AccountManagment][acct_mgmt_sample] 範例專案來逐步解說搭配使用 Azure AD 與 Batch 管理 .NET 程式庫。 AccountManagement 範例是主控台應用程式，會以程式設計方式存取訂用帳戶、建立資源群組和新的 Batch 帳戶，並執行帳戶上的某些作業。 

若要深入了解使用 Batch 管理 .NET 程式庫和 AccountManagement 範例，請參閱[使用適用於 .NET 的 Batch 管理用戶端程式庫來管理 Batch 帳戶和配額](batch-management-dotnet.md)。

### <a name="register-your-application-with-azure-ad"></a>向 Azure AD 註冊您的應用程式

Azure [Active Directory Authentication Library][aad_adal] (ADAL) 為 Azure AD 提供程式設計介面以供您的應用程式使用。 若要從您的應用程式呼叫 ADAL，您必須在 Azure AD 租用戶中註冊您的應用程式。 當您註冊應用程式時，要提供 Azure AD 有關您應用程式的資訊，包括 Azure AD 租用戶內的名稱。 Azure AD 接著會提供您在執行階段用來將應用程式與 Azure AD 產生關聯的應用程式識別碼。 若要深入了解應用程式識別碼，請參閱[Azure Active Directory 中的應用程式物件和服務主體物件之間的關聯性討論](../active-directory/develop/active-directory-application-objects.md)。

若要註冊 AccountManagement 範例應用程式，遵循[整合應用程式與 Azure Active Directory][aad_integrate] 之[新增應用程式](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application)一節中的步驟。 指定 [原生用戶端應用程式] 作為應用程式類型。 針對**重新導向 URI**，您可以指定任何有效的 URI (例如 `http://myaccountmanagementsample`)，因為它並不需要是實際的端點︰

![](./media/batch-aad-auth/app-registration-management-plane.png)

完成註冊程序後，您會看到應用程式識別碼以及針對應用程式列出的物件 (服務主體) 識別碼。  

![](./media/batch-aad-auth/app-registration-client-id.png)

### <a name="update-your-code-to-reference-your-application-id"></a>更新您的程式碼以參考您的應用程式識別碼 

用戶端應用程式使用應用程式識別碼 (也稱為用戶端識別碼) 在執行階段存取 Azure AD。 在 Azure 入口網站中註冊您的應用程式後，更新程式碼以使用 Azure AD 針對已註冊應用程式所提供的應用程式識別碼。 在 AccountManagement 範例應用程式中，從 Azure 入口網站將應用程式識別碼複製到適當的常數︰

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
同時，複製您在註冊程序期間指定的重新導向 URI。

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

### <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>授與 Azure Resource Manager API 存取給您的應用程式

接下來，您必須將應用程式的存取委派給 Azure Resource Manager API。 Resource Manager API 的 Azure AD 識別項是 **Windows Azure 服務管理 API**。

在 Azure 入口網站中遵循下列步驟：

1. 在 Azure 入口網站的左側導覽窗格中，選擇 [更多服務]，按一下 [應用程式註冊]，然後按一下 [新增]。
2. 在應用程式註冊清單中搜尋您應用程式的名稱︰

    ![搜尋您的應用程式名稱](./media/batch-aad-auth/search-app-registration.png)

3. 顯示 [設定] 刀鋒視窗。 在 [API 存取] 區段中，選取 [必要權限]。
4. 按一下 [新增] 以新增必要權限。 
5. 在步驟 1 中，輸入 **Windows Azure 服務管理 API**，從結果清單選取該 API，然後按一下 [選取] 按鈕。
6. 在步驟 2 中，選取**存取 Azure 傳統部署模型做為組織使用者**旁的核取方塊，然後按一下 [選取] 按鈕。
7. 按一下 [完成] 按鈕。

[必要權限] 刀鋒視窗現在會顯示已向 ADAL 和 Resource Manager API 授與您應用程式的權限。 當您第一次向 Azure AD 註冊您的應用程式時，權限依預設會授與給 ADAL。

![委派權限給 Azure Resource Manager API](./media/batch-aad-auth/required-permissions-management-plane.png)


### <a name="acquire-an-azure-ad-authentication-token"></a>取得 Azure AD 驗證權杖

AccountManagement 範例應用程式會定義常數，為 Azure AD 和 Azure Resource Manager 提供端點。 範例應用程式會使用這些常數來查詢訂用帳戶資訊的 Azure AD。 將這些常數保留不變︰

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure service management resource 
private const string ResourceUri = "https://management.core.windows.net/";
```

在 Azure AD 租用戶中註冊 AccountManagement 範例，並在範例原始程式碼中提供必要值之後，範例準備好使用 Azure AD 進行驗證。 當您執行範例時，ADAL 會嘗試取得驗證權杖。 在此步驟中，它會提示您輸入您的 Microsoft 認證︰ 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

您提供認證之後，範例應用程式可以繼續發出已驗證要求給 Batch 管理服務。 

## <a name="use-azure-ad-with-batch-service-solutions"></a>搭配 Batch 服務解決方案使用 Azure AD

Batch .NET 程式庫提供使用 Batch 服務建置平行處理工作流程的類型。 Batch 服務同時支援[共用金鑰](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service)驗證和透過 Azure AD 的驗證。 在本節中，我們會討論透過 Azure AD 進行驗證。

>[!NOTE]
>當您建立 Batch 帳戶時，可以指定集區是否會配置在 Batch 管理的訂用帳戶中，或是在使用者訂用帳戶中。 如果您的帳戶在使用者訂用帳戶中配置集區，則您必須使用 Azure AD 驗證該帳戶中資源的要求。
>
>

透過 Azure AD 驗證 Batch .NET 應用程式很類似驗證 Batch 管理 .NET 應用程式。 有一些差異，如這一節所述。

### <a name="batch-service-endpoints"></a>Batch 服務端點

不同於您與 Batch 管理 .NET 搭配使用的 Batch 服務端點。

Batch 服務的 Azure AD 端點是︰

`https://login.microsoftonline.com/common`

Batch 服務的資源端點是︰

`https://batch.core.windows.net/`

### <a name="grant-the-batch-service-api-access-to-your-application"></a>授與您的應用程式 Batch 服務 API 存取

在您從 Batch 應用程式透過 Azure AD 進行驗證之前，需要向 Azure AD 註冊您的應用程式，並授與 Batch 服務 API 存取。 Batch 服務 API 的 Azure AD 識別項是 **Microsoft Azure Batch (MicrosoftAzureBatch)**。

1. 若要註冊 Batch 應用程式，遵循[整合應用程式與 Azure Active Directory][aad_integrate] 之[新增應用程式](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application)一節中的步驟。 如需**重新導向 URI**，您可以指定任何有效的 URI。 它不需要是實際的端點。

    註冊您的應用程式之後，您會看到應用程式識別碼和物件識別碼︰

    ![向 Azure AD 註冊您的 Batch 應用程式](./media/batch-aad-auth/app-registration-data-plane.png)

2. 接下來，顯示 [設定] 刀鋒視窗。 在 [API 存取] 區段中，選取 [必要權限]。
3. 在 [必要權限] 刀鋒視窗中，按一下 [新增] 按鈕。
4. 在步驟 1 中，搜尋 **MicrosoftAzureBatch**選取 **Microsoft Azure Batch (MicrosoftAzureBatch)**，然後按一下 [選取] 按鈕。
5. 在步驟 2 中，選取 [存取 Azure Batch 服務] 旁的核取方塊，然後按一下 [選取] 按鈕。
6. 按一下 [完成] 按鈕。

[必要權限] 刀鋒視窗現在會顯示您的 Azure AD 應用程式授與 Azure AD 與 Azure Batch API 存取。 

![API 權限](./media/batch-aad-auth/required-permissions-data-plane.png)

### <a name="authentication-for-batch-accounts-in-a-user-subscription"></a>使用者訂用帳戶中的 Batch 帳戶驗證

當您建立新的 Batch 帳戶時，可以選擇已配置集區的訂用帳戶。 您的選擇會影響該帳戶中對資源提出要求的驗證方式

根據預設，Batch 集區會在 Batch 服務訂用帳戶中配置。 如果您選擇此選項，就可以使用共用金鑰或使用 Azure AD 來驗證該帳戶中的資源要求。

您也可以指定 Batch 集區會在指定的使用者訂用帳戶中配置。 如果您選擇此選項，必須向 Azure AD 進行驗證。

### <a name="best-practices-for-using-azure-ad-with-batch"></a>搭配使用 Azure AD 與 Batch 的最佳作法

Azure AD 驗證權杖會在一小時後過期。 使用長時間執行 **BatchClient** 物件時，我們建議您在每個要求從 ADAL 擷取權杖，以確保您一律擁有有效的權杖。 

若要在 .NET 中達到此目的，撰寫可從 Azure AD 中擷取權杖的方法，並傳遞該方法至 **BatchTokenCredentials** 物件做為委派。 每個要求都會對 Batch 服務呼叫委派方法，以確保已提供有效的權杖。 根據預設，ADAL 會快取權杖，因此只在必要時才會從 Azure AD 擷取新的權杖。 如需範例，請參閱下一節中的[程式碼範例︰搭配使用 Azure AD 與 Batch .NET](#code-example-using-azure-ad-with-batch-net)。 如需關於 Azure AD 中的權杖資訊，請參閱 [Azure AD 的驗證案例][aad_auth_scenarios]。

### <a name="code-example-using-azure-ad-with-batch-net"></a>程式碼範例︰搭配使用 Azure AD 與 Batch .NET

若要撰寫使用 Azure AD 進行驗證的 Batch .NET 程式碼，請參考 [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) 封裝和 [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 封裝。

在您的程式碼中，包括下列 `using` 陳述式︰

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

參考 Azure AD 常見端點與您程式碼中 Batch 服務的 Azure AD 端點︰  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

請參考您的 Batch 帳戶端點︰

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

指定您應用程式的應用程式識別碼 (用戶端識別碼)。 可在 Azure 入口網站中從您的應用程式註冊取得應用程式識別碼；請參閱[授與您的應用程式 Batch 服務 API 存取](#grant-the-batch-service-api-access-to-your-application)來擷取它。 

```csharp
private const string ClientId = "<application-id>";
```

並且指定重新導向 URI，它可以是任何有效的 URI。

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

撰寫回呼方法，以從 Azure AD 取得驗證權杖。 **AcquireTokenAsync**方法提示使用者輸入其認證，並使用這些認證來取得新權杖。

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

建構接受委派做為參數的 **BatchTokenCredentials** 物件。 使用這些認證來開啟 **BatchClient** 物件。 然後您可以使用對 Batch 服務的後續作業的 **BatchClient** 物件。

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

如上所示的 **GetAuthenticationTokenAsync** 回呼方法會使用 Azure AD 對與應用程式互動的使用者進行整合式驗證。 呼叫 **AcquireTokenAsync** 方法會提示使用者輸入其認證，和一旦使用者提供這些方法後會繼續進行的應用程式。 您也可以使用 Azure AD，利用 Azure AD 服務主體來驗證自動應用程式。 如需詳細資訊，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/active-directory-application-objects.md)和[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../resource-group-create-service-principal-portal.md)。  
 

## <a name="next-steps"></a>後續步驟

如需執行 [AccountManagement 範例應用程式][acct_mgmt_sample]的相關資訊，請參閱[使用適用於 .NET 的 Batch 管理用戶端程式庫來管理 Batch 帳戶和配額](batch-management-dotnet.md)。

若要深入了解 Azure AD，請參閱 [Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory/)。 [Azure 程式碼範例](https://azure.microsoft.com/resources/samples/?service=active-directory)程式庫中有深入的範例示範如何使用 ADAL。


[aad_about]: ../active-directory/active-directory-whatis.md "什麼是 Azure Active Directory？"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD 的驗證案例"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "整合應用程式與 Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

