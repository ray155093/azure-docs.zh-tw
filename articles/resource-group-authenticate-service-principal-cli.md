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
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# 使用 Azure CLI 建立用來存取資源的服務主體

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)


當您有需要存取資源的應用程式或指令碼時，您很可能不會想要以您擁有的認證執行此程序。您可能具有應用程式的不同權限，而如果您的職責變更，您就不希望應用程式繼續使用您的認證。相反地，您可以為應用程式建立身分識別，在其中包含驗證認證和角色指派。每次應用程式執行時，它會以這些認證進行自我驗證。本主題說明如何使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](xplat-cli-install.md) 來設定應用程式，讓它利用自己的認證和身分識別來執行。

在 Azure CLI 中，您有 2 個選項可以驗證您的 AD 應用程式︰

 - password
 - 憑證

本主題說明如何在 Azure CLI 中使用這兩個選項。如果您想要從程式設計架構 (例如 Python、Ruby 或 Node.js) 登入 Azure，密碼驗證可能是您的最佳選項。在決定是要使用密碼還是憑證時，請參閱[範例應用程式](#sample-applications)一節，以查看在不同架構進行驗證的範例。

## Active Directory 概念

在本文中，您會建立 Active Directory (AD) 應用程式和服務主體這兩個物件。AD 應用程式是您的應用程式的全域表示法。其中包含認證 (應用程式識別碼以及密碼或憑證)。服務主體是您的應用程式在 Active Directory 中的本機表示法。其中包含角色指派。本主題著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。您通常會將單一租用戶應用程式用在組織內執行的企業營運系統應用程式。在單一租用戶應用程式中，您有一個 AD 應用程式和一個服務主體。

您可能想知道 - 為什麼我需要這兩個物件？ 當您考慮多租用戶應用程式時，這個方法比較合適。您通常會將多租用戶應用程式用於軟體即服務 (SaaS) 應用程式，以便您的應用程式在許多不同的訂用帳戶中執行。對於多租用戶應用程式，您有一個 AD 應用程式和多個服務主體 (每個 Active Directory 中有一個服務主體可授與應用程式的存取權)。若要設定多租用戶應用程式，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

## 所需的權限

若要完成本主題，您必須在 Azure Active Directory 和您的 Azure 訂用帳戶中有足夠的權限。具體來說，您必須能夠在 Active Directory 中建立應用程式，並將服務主體指派給角色。

在您的 Active Directory 中，您的帳戶必須是系統管理員 (例如 [全域管理員]或 [使用者管理員])。如果您的帳戶已指派給 [使用者] 角色，您需要讓系統管理員提高您的權限。

在您的訂用帳戶中，您的帳戶必須擁有 `Microsoft.Authorization/*/Write` 存取權，這是透過[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色或[使用者存取管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色來授與。如果您的帳戶已指派給 [參與者] 角色，您會在嘗試將服務主體指派給角色時收到錯誤。同樣地，您的訂用帳戶管理員必須授與您足夠的存取權。

現在，繼續進行[密碼](#create-service-principal-with-password)或[憑證](#create-service-principal-with-certificate)驗證的章節。

## 使用密碼建立服務主體

在本節中，您會執行下列步驟：

- 建立 AD 應用程式 (包含密碼) 和服務主體
- 將 [讀取者角色] 指派給服務主體

若要快速開始這些步驟，請使用下列命令。

    azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
    azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

我們會更仔細解說這些步驟，確保您了解此程序。

1. 登入您的帳戶。

        azure config mode arm
        azure login

1. 建立應用程式的服務主體。提供顯示名稱、可描述應用程式之頁面的 URI、可識別應用程式的 URI，以及應用程式身分識別的密碼。此命令會同時建立 AD 應用程式和服務主體。

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p {your-password}
        
     對於單一租用戶應用程式，不會驗證 URI。
     
     如果您的帳戶沒有 Active Directory 的[必要權限](#required-permissions)，您會看到一則錯誤訊息，指出 "Authentication\_Unauthorized" 或「在內容中找不到任何訂用帳戶」。
    
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

2. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將服務主體新增至 [讀取者] 角色，以授與讀取訂用帳戶中所有資源的權限。若為其他角色，請參閱 [RBAC︰內建角色](./active-directory/role-based-access-built-in-roles.md)。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ObjectId**。

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     如果您的帳戶沒有足夠的權限可指派角色，您會看到一則錯誤訊息。此訊息說明您的帳戶**沒有權限來對 '/subscriptions/{guid}' 範圍執行 'Microsoft.Authorization/roleAssignments/write' 動作**。

就這麼簡單！ 您的 AD 應用程式和服務主體已設定好。下一節會說明如何透過 Azure CLI 以認證來登入。如果您想要在您的程式碼應用程式中使用認證，則不需要繼續進行本主題。您可以跳到[範例應用程式](#sample-applications)，以查看使用應用程式識別碼和密碼來登入的範例。

### 透過 Azure CLI 提供認證

現在，您需要以應用程式的形式登入以執行作業。

1. 每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。租用戶是 Active Directory 的執行個體。若要擷取目前已驗證訂用帳戶的租用戶識別碼，請使用︰

        azure account show

     它會傳回：

        info:    Executing command account show
        data:    Name                        : Microsoft Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     如果您需要取得其他訂用帳戶的租用戶識別碼，請使用下列命令︰

        azure account show -s {subscription-id}

3. 以服務主體的形式登入。

        azure login -u https://www.contoso.org/example --service-principal --tenant {tenant-id}

    系統會提示您輸入密碼。提供您建立 AD 應用程式時指定的密碼。

        info:    Executing command login
        Password: ********

您現在已驗證為您所建立之服務主體的服務主體。

## 使用憑證建立服務主體

在本節中，您會執行下列步驟：

- 建立自我簽署憑證
- 建立 AD 應用程式 (包含憑證) 和服務主體
- 將 [讀取者角色] 指派給服務主體

若要完成這些步驟，您必須安裝 [OpenSSL](http://www.openssl.org/)。

1. 建立自我簽署憑證。

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. 結合公開和私密金鑰。

        cat privkey.pem cert.pem > examplecert.pem

3. 開啟 **examplecert.pem** 檔案並尋找在 **-----BEGIN CERTIFICATE-----** 與 **-----END CERTIFICATE-----** 之間的一長串字元。複製憑證資料。您會在建立服務主體時將此資料當作參數傳遞。

1. 登入您的帳戶。

        azure config mode arm
        azure login

1. 建立應用程式的服務主體。提供顯示名稱、可描述應用程式之頁面的 URI、可識別應用程式的 URI，以及您複製的憑證資料。此命令會同時建立 AD 應用程式和服務主體。

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
     對於單一租用戶應用程式，不會驗證 URI。
     
     如果您的帳戶沒有 Active Directory 的[必要權限](#required-permissions)，您會看到一則錯誤訊息，指出 "Authentication\_Unauthorized" 或「在內容中找不到任何訂用帳戶」。
    
     傳回新的服務主體。授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將服務主體新增至 [讀取者] 角色，以授與讀取訂用帳戶中所有資源的權限。若為其他角色，請參閱 [RBAC︰內建角色](./active-directory/role-based-access-built-in-roles.md)。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ObjectId**。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     如果您的帳戶沒有足夠的權限可指派角色，您會看到一則錯誤訊息。此訊息說明您的帳戶**沒有權限來對 '/subscriptions/{guid}' 範圍執行 'Microsoft.Authorization/roleAssignments/write' 動作**。

### 透過自動化的 Azure CLI 指令碼提供憑證

現在，您需要以應用程式的形式登入以執行作業。

1. 每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。租用戶是 Active Directory 的執行個體。若要擷取目前已驗證訂用帳戶的租用戶識別碼，請使用︰

        azure account show

     它會傳回：

        info:    Executing command account show
        data:    Name                        : Microsoft Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     如果您需要取得其他訂用帳戶的租用戶識別碼，請使用下列命令︰

        azure account show -s {subscription-id}

1. 若要擷取憑證指紋，然後移除不必要的字元，請使用：

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     這會傳回指紋值，類似：

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

1. 以服務主體的形式登入。

        azure login --service-principal --tenant {tenant-id} -u https://www.contoso.org/example --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

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

<!---HONumber=AcomDC_0914_2016-->