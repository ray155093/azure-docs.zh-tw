<properties
   pageTitle="使用 PowerShell 來建立 AD 應用程式 | Microsoft Azure"
   description="描述如何使用 Azure PowerShell 建立 Active Directory 應用程式，並透過角色型存取控制將存取權授與資源。它示範如何使用密碼或憑證來驗證應用程式。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# 使用 Azure PowerShell 來建立可存取資源的 Active Directory 應用程式

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)


本主題顯示如何使用 [Azure PowerShell](powershell-install-configure.md) 以建立 Active Directory (AD) 應用程式，例如自動化程序、應用程式或服務，可以存取您訂用帳戶中的其他資源。使用 Azure Resource Manager，您可以使用角色存取控制將允許的動作授與應用程式。

在本文中，您將建立兩個物件 - AD 應用程式和服務主體。AD 應用程式位於租用戶中，應用程式會在其中註冊，並且定義要執行的處理。服務主體包含 AD 應用程式的身分識別，並且用於指派權限。從 AD 應用程式中，您可以建立許多服務主體。如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](./active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](./active-directory/active-directory-authentication-scenarios.md)。

您有 2 個選項可以驗證您的應用程式︰

 - 密碼 - 適用於當使用者想要在執行期間以互動方式登入
 - 憑證 - 適用於不需要使用者互動而必須驗證的自動執行的指令碼

## 建立具有密碼的 AD 應用程式

在本節中，您將執行步驟來建立具有密碼的 AD 應用程式。

1. 登入您的帳戶。

        Add-AzureRmAccount

1. 建立新的 Active Directory 應用程式，方法是提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     檢查新的應用程式物件。

        $azureAdApplication
        
     請特別注意，需要有 **ApplicationId** 屬性，才能建立服務主體、角色指派以及取得存取權杖。

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


### 建立服務主體並且指派至角色

您必須從 AD 應用程式建立服務主體，並將角色指派給它。

1. 傳入 Active Directory 應用程式的應用程式識別碼來建立應用程式的服務主體。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### 透過 PowerShell 手動提供認證

您已經建立了 Active Directory 應用程式，和該應用程式的服務主體。您已經將服務主體指派給角色。現在，您需要登入為應用程式以執行作業。當執行隨選指令碼或命令時，您可以手動提供應用程式的認證。

1. 執行 **Get-Credential** 命令，以建立含有您認證的新 **PSCredential** 物件。

        $creds = Get-Credential

2. 系統會提示您輸入認證。針對使用者名稱，使用您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。針對密碼，使用您在建立帳戶時所指定的密碼。

     ![輸入認證](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

3. 擷取用來建立角色指派的訂用帳戶。此訂用帳戶將會用來取得服務主體角色指派所在租用戶的 **TenantId**。

        $subscription = Get-AzureRmSubscription

     如果您的帳戶連結到多個訂用帳戶，提供訂用帳戶名稱或識別碼來取得您想要使用的訂用帳戶。
     
        $subscription = Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate"

4. 登入為服務主體，方法是指定此帳戶為服務主體，以及提供認證物件。

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId
        
     您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

5. 若要在稍後的工作階段中使用目前的存取權杖，您可以儲存設定檔。

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     您可以開啟設定檔，並檢查其內容。請注意其中包含存取權杖。
        
6. 只要載入設定檔即可，不用在下次您想要將程式碼執行為服務主體時重新登入。

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] 存取權杖會過期，因此，只要權杖有效，就只使用可使用的已儲存設定檔。若要永久執行自動執行的指令碼，請使用憑證。
        
## 建立具有憑證的 AD 應用程式

在本節中，您將執行步驟來建立具有憑證的 AD 應用程式。

1. 建立自我簽署憑證。

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     您會收到憑證的相關資訊，包含指紋。
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

2. 從憑證擷取金鑰值。

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

3. 登入您的 Azure 帳戶。

        Add-AzureRmAccount

4. 在目錄中建立應用程式。

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    檢查新的應用程式物件。

        $azureAdApplication

    請注意，需要有 **ApplicationId** 屬性，才能建立服務主體、角色指派以及取得存取權杖。

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


### 建立服務主體並且指派至角色

1. 傳入 Active Directory 應用程式的應用程式識別碼來建立應用程式的服務主體。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### 準備您的指令碼的值

在您的指令碼中您將會傳入三個值，需要登入為服務主體。您將需要：

- 應用程式識別碼
- 租用戶識別碼 
- 憑證指紋

您已在先前步驟中看過應用程式識別碼和憑證指紋。不過，如果您稍後需要擷取這些值，命令如下所示，同時列出取得租用戶識別碼的命令。

1. 若要擷取租用戶識別碼，請使用︰

        (Get-AzureRmSubscription).TenantId 

    或者，如果您有多個訂用帳戶，請提供訂用帳戶的名稱：

        (Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate").TenantId
        
2. 若要擷取應用程式識別碼，請使用：

        (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
3. 若要擷取憑證指紋，請使用：

        (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

### 透過自動化的 PowerShell 指令碼提供憑證

您已經建立了 Active Directory 應用程式，和該應用程式的服務主體。您已經將服務主體指派給角色。現在，您必須登入為服務主體，來執行服務主體的作業。

若要在指令碼中進行驗證，請指定帳戶為服務主體，並且提供憑證指紋、應用程式識別碼和租用戶識別碼。

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint {thumbprint} -ApplicationId {applicationId} -TenantId {tenantid}

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

## 後續步驟
  
- 如需 .NET 驗證範例，請參閱 [Azure Resource Manager SDK for .NET](resource-manager-net-sdk.md)。
- 如需 Java 驗證範例，請參閱 [Azure Resource Manager SDK for Java](resource-manager-java-sdk.md)。 
- 如需 Python 驗證範例，請參閱[適用於 Python 的資源管理驗證](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)。
- 如需 REST 驗證範例，請參閱 [Resource Manager REST API](resource-manager-rest-api.md)。
- 如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

<!---HONumber=AcomDC_0601_2016-->