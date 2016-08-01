<properties
   pageTitle="使用 PowerShell 建立 Azure 服務主體 | Microsoft Azure"
   description="描述如何使用 Azure PowerShell 建立 Active Directory 應用程式和服務主體，並透過角色型存取控制將存取權授與資源。它示範如何使用密碼或憑證來驗證應用程式。"
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
   ms.date="07/18/2016"
   ms.author="tomfitz"/>

# 使用 Azure PowerShell 建立用來存取資源的服務主體

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)

當您有需要存取資源的應用程式或指令碼時，您很可能不會想要以使用者的認證執行此程序。該使用者可能有您想要指派給此程序的不同權限，而且使用者的工作職責可能會變更。相反地，您可以為應用程式建立身分識別，在其中包含驗證認證和角色指派。您的應用程式每次執行時，都會以此身分識別登入。本主題說明如何使用 [Azure PowerShell](powershell-install-configure.md) 來設定所需的一切項目，以便讓應用程式利用自己的認證和身分識別來執行。

在本文中，您將建立 Active Directory (AD) 應用程式和服務主體這兩個物件。AD 應用程式包含認證 (應用程式識別碼以及密碼或憑證)。服務主體則包含角色指派。從 AD 應用程式中，您可以建立許多服務主體。本主題著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。您通常會將單一租用戶應用程式用在組織內執行的企業營運系統應用程式。若您的應用程式需要在許多組織內執行，您也可以建立多租用戶應用程式。您通常會將多租用戶應用程式用在軟體即服務 (SaaS) 應用程式。若要設定多租用戶應用程式，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

在使用 Active Directory 時，有許多概念需要了解。如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](./active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](./active-directory/active-directory-authentication-scenarios.md)。

在 PowerShell 中，您有 2 個選項可以驗證您的 AD 應用程式︰

 - password
 - 憑證

如果在設定 AD 應用程式之後，您想要從另一個程式設計架構 (例如 Python、Ruby 或 Node.js) 登入 Azure，密碼驗證可能是您的最佳選項。在決定是要使用密碼還是憑證時，請參閱[範例應用程式](#sample-applications)一節，以查看在不同架構進行驗證的範例。

## 取得租用戶識別碼

每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。租用戶是 Active Directory 的執行個體。因為您將需要該密碼或憑證驗證的值，現在讓我們來取得該值。

1. 登入您的帳戶。

        Add-AzureRmAccount

2. 如果您只有一個訂用帳戶，您可以使用：

        $tenant = (Get-AzureRmSubscription).TenantId
    
     如果您有多個訂用帳戶，請指定您要用於 AD 應用程式的訂用帳戶。選取您的 Active Directory 所在的訂用帳戶。如需詳細資訊，請參閱[管理您的 Azure AD 目錄](./active-directory/active-directory-administer.md)。

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

現在，前進到下面關於[密碼](#create-service-principal-with-password)或[憑證](#create-service-principal-with-certificate)驗證的章節。

## 使用密碼建立服務主體

在本節中，您將執行步驟來以密碼建立 AD 應用程式和服務主體。

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


     您必須從 AD 應用程式建立服務主體，並將角色指派給它。

2. 傳入 Active Directory 應用程式的應用程式識別碼來建立應用程式的服務主體。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。若要指派角色，您必須擁有 `Microsoft.Authorization/*/Write` 存取權，這是透過[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色或[使用者存取系統管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色來授與。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

就這麼簡單！ 您的 AD 應用程式和服務主體已設定好。下一節會說明如何透過 PowerShell 以認證來登入；不過，如果您想要在您的程式碼應用程式中使用認證，則不需要繼續進行本主題。您可以跳到[範例應用程式](#sample-applications)，以查看使用應用程式識別碼和密碼來登入的範例。

### 透過 PowerShell 提供認證

現在，您需要登入為應用程式以執行作業。

1. 執行 **Get-Credential** 命令，以建立含有您認證的新 **PSCredential** 物件。您必須先擁有 **ApplicationId** 或 **IdentifierUris** 才能執行此命令，因此請確定您已有上述項目可供貼上。

        $creds = Get-Credential

2. 系統會提示您輸入認證。針對使用者名稱，使用您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。針對密碼，使用您在建立帳戶時所指定的密碼。

     ![輸入認證](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

4. 登入為服務主體，方法是指定此帳戶為服務主體，以及提供認證物件。您將需要您在[取得租用戶識別碼](#get-tenant-id)擷取的租用戶識別碼。

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

5. 若要在稍後的工作階段中使用目前的存取權杖，您可以儲存設定檔。

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     您可以開啟設定檔，並檢查其內容。請注意其中包含存取權杖。
        
6. 只要載入設定檔即可，不用在下次您想要將程式碼執行為服務主體時手動重新登入。

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] 存取權杖會過期，因此，只要權杖有效，就只使用可使用的已儲存設定檔。
        
## 使用憑證建立服務主體

在本節中，您將執行步驟來以憑證建立 AD 應用程式和服務主體。

1. 建立自我簽署憑證。如果您是執行 **Windows 10 或 Windows Server 2016 Technical Preview**，請執行下列命令：

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     您的變數包含憑證的相關資訊，包含指紋。
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     如果您**不是**執行 Windows 10 或 Windows Server 2016 Technical Preview，則不會有 **New-SelfSignedCertificate** Cmdlet.相反地，請下載[自我簽署憑證產生器](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) PowerShell 指令碼，然後執行下列命令來產生憑證。如果您已在上一個範例中建立憑證，則此步驟並非必要。
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. 從憑證擷取金鑰值。

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

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


5. 傳入 Active Directory 應用程式的應用程式識別碼來建立應用程式的服務主體。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

6. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。若要指派角色，您必須擁有 `Microsoft.Authorization/*/Write` 存取權，這是透過[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色或[使用者存取系統管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色來授與。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

就這麼簡單！ 您的 AD 應用程式和服務主體已設定好。下一節會說明如何透過 PowerShell 以憑證來登入。

### 透過自動化的 PowerShell 指令碼提供憑證

若要在指令碼中進行驗證，請指定帳戶為服務主體，並且提供憑證指紋、應用程式識別碼和租用戶識別碼。您已在變數 **$azureAdApplication.ApplicationId**、**$cert.Thumbprint** 和 **$tenant** 中擁有這些值。若要讓指令碼自動執行，您可以將這些值儲存為環境變數，並在執行期間擷取它們，或者您可以將它們包含在指令碼中。

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint 000000 -ApplicationId 000000 -TenantId 0000000

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

如果您稍後需要擷取應用程式識別碼，請使用︰

    (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
如果您稍後需要擷取憑證指紋，請使用︰

    (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

如果您需要稍後再擷取租用戶識別碼，請參閱[取得租用戶識別碼](#get-tenant-id)。

## 範例應用程式

下列範例應用程式會顯示如何登入為服務主體。

**.NET**

- [使用 .NET 以範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [使用 .NET 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [開始使用資源 - 在 Java 中使用 Azure Resource Manager 範本進行部署](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [開始使用資源 - 在 Java 中管理資源群組](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [使用 Python 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [使用 Python 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [使用 Node.js 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [使用 Node.js 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [使用 Ruby 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [使用 Ruby 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## 後續步驟
  
- 如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

<!---HONumber=AcomDC_0720_2016-->