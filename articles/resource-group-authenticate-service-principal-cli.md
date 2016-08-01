<properties
   pageTitle="使用 Azure CLI 建立服務主體 | Microsoft Azure"
   description="描述如何使用 Azure CLI 建立 Active Directory 應用程式和服務主體，並透過角色型存取控制將存取權授與資源。它示範如何使用密碼或憑證來驗證應用程式。"
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
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# 使用 Azure CLI 建立用來存取資源的服務主體

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)

當您有需要存取資源的應用程式或指令碼時，您很可能不會想要以使用者的認證執行此程序。該使用者可能有您想要指派給此程序的不同權限，而且使用者的工作職責可能會變更。相反地，您可以為應用程式建立身分識別，在其中包含驗證認證和角色指派。您的應用程式每次執行時，都會以此身分識別登入。本主題說明如何使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](xplat-cli-install.md)，來設定所需的一切項目，以便讓應用程式利用自己的認證和身分識別來執行。

在本文中，您將建立 Active Directory (AD) 應用程式和服務主體這兩個物件。AD 應用程式包含認證 (應用程式識別碼以及密碼或憑證)。服務主體則包含角色指派。從 AD 應用程式中，您可以建立許多服務主體。本主題著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。您通常會將單一租用戶應用程式用在組織內執行的企業營運系統應用程式。若您的應用程式需要在許多組織內執行，您也可以建立多租用戶應用程式。您通常會將多租用戶應用程式用在軟體即服務 (SaaS) 應用程式。若要設定多租用戶應用程式，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

在使用 Active Directory 時，有許多概念需要了解。如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](./active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](./active-directory/active-directory-authentication-scenarios.md)。

在 Azure CLI 中，您有 2 個選項可以驗證您的 AD 應用程式︰

 - password
 - 憑證

如果在設定 AD 應用程式之後，您想要從另一個程式設計架構 (例如 Python、Ruby 或 Node.js) 登入 Azure，密碼驗證可能是您的最佳選項。在決定是要使用密碼還是憑證時，請參閱[範例應用程式](#sample-applications)一節，以查看在不同架構進行驗證的範例。

## 取得租用戶識別碼

每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。租用戶是 Active Directory 的執行個體。因為您將需要該密碼或憑證驗證的值，現在讓我們來取得該值。

1. 登入您的帳戶。

        azure config mode arm
        azure login

1. 如果您正在擷取目前已驗證之訂用帳戶的租用戶識別碼，就不需要提供訂用帳戶 ID 做為參數。**-r** 參數會擷取不加引號的值。

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

現在，前進到下面關於[密碼](#create-service-principal-with-password)或[憑證](#create-service-principal-with-certificate)驗證的章節。


## 使用密碼建立服務主體

在本節中，您將執行步驟來以密碼建立服務主體，並將它指派給某個角色。

1. 建立應用程式的服務主體。提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。此命令會同時建立 AD 應用程式和服務主體。

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
        
    傳回新的服務主體。授與權限時，需要物件識別碼。在登入時，則需要服務主體名稱。
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。若要指派角色，您必須擁有 `Microsoft.Authorization/*/Write` 存取權，這是透過[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色或[使用者存取系統管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色來授與。

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

就這麼簡單！ 您的 AD 應用程式和服務主體已設定好。下一節會說明如何透過 Azure CLI 以認證來登入；不過，如果您想要在您的程式碼應用程式中使用認證，則不需要繼續進行本主題。您可以跳到[範例應用程式](#sample-applications)，以查看使用應用程式識別碼和密碼來登入的範例。

### 透過 Azure CLI 提供認證

1. 針對使用者名稱，請使用建立服務主體時所傳回的服務主體名稱。如果您要擷取應用程式識別碼，請使用下列命令。在 **search** 參數中提供 Active Directory 應用程式的名稱。

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. 登入為服務主體。

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant "$tenantId"

    系統會提示您輸入密碼。提供您建立 AD 應用程式時指定的密碼。

        info:    Executing command login
        Password: ********

您現在已驗證為您所建立之服務主體的服務主體。

## 使用憑證建立服務主體

在本節中，您要執行的步驟是為服務主體建立可使用憑證進行驗證的服務主體。若要完成這些步驟，您必須安裝 [OpenSSL](http://www.openssl.org/)。

1. 建立自我簽署憑證。

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. 結合公開和私密金鑰。

        cat privkey.pem cert.pem > examplecert.pem

3. 開啟 **examplecert.pem** 檔案並複製憑證資料。尋找在 **-----BEGIN CERTIFICATE-----** 與 **-----END CERTIFICATE-----** 之間的一長串字元。

1. 建立應用程式的服務主體。提供在上一個步驟中傳回的應用程式識別碼。

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
    傳回新的服務主體。授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。若要指派角色，您必須擁有 `Microsoft.Authorization/*/Write` 存取權，這是透過[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色或[使用者存取系統管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色來授與。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### 透過自動化的 Azure CLI 指令碼提供憑證

若要使用 Azure CLI 進行驗證，請提供憑證指紋、憑證檔案、應用程式識別碼和租用戶識別碼。

    azure login --service-principal --tenant 00000 -u 000000 --certificate-file C:\certificates\examplecert.pem --thumbprint 000000

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

如果您需要擷取憑證指紋，然後移除不需要的字元，請使用：

    openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
這會傳回指紋值，類似：

    30996D9CE48A0B6E0CD49DBB9A48059BF9355851

如果您需要擷取應用程式識別碼，請使用︰

    azure ad app show --search exampleapp --json | jq -r '.[0].appId'

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
- 如需使用憑證和 Azure CLI 的詳細資訊，請參閱[從 Linux 命令列以憑證方式驗證 Azure 服務主體](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)。

<!---HONumber=AcomDC_0720_2016-->