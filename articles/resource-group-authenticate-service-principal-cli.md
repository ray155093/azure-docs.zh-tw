<properties
   pageTitle="使用 Azure CLI 來建立 AD 應用程式 | Microsoft Azure"
   description="描述如何使用 Azure CLI 建立 Active Directory 應用程式，並透過角色型存取控制將存取權授與資源。它示範如何使用密碼或憑證來驗證應用程式。"
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

# 使用 Azure CLI 來建立可存取資源的 Active Directory 應用程式

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)

本主題顯示如何使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](xplat-cli-install.md) 以建立 Active Directory (AD) 應用程式，例如自動化程序、應用程式或服務，可以存取您訂用帳戶中的其他資源。使用 Azure Resource Manager，您可以使用角色存取控制將允許的動作授與應用程式。

在本文中，您將建立兩個物件 - AD 應用程式和服務主體。AD 應用程式位於租用戶中，應用程式會在其中註冊，並且定義要執行的處理。服務主體包含 AD 應用程式的身分識別，並且用於指派權限。從 AD 應用程式中，您可以建立許多服務主體。如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](./active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](./active-directory/active-directory-authentication-scenarios.md)。

您有 2 個選項可以驗證您的應用程式︰

 - 密碼 - 適用於當使用者想要在執行期間以互動方式登入
 - 憑證 - 適用於不需要使用者互動而必須驗證的自動執行的指令碼
 
## 建立具有密碼的 AD 應用程式

在本節中，您將執行步驟來建立具有密碼的 AD 應用程式。

1. 切換至 Azure Resource Manager 模式，並登入您的帳戶。

        azure config mode arm
        azure login

2. 執行 **azure ad app create** 命令，以建立新的 AD 應用程式。提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    傳回 AD 應用程式。需要有 AppId 屬性，才能建立服務主體以及取得存取權杖。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### 建立服務主體並且指派至角色

1. 建立應用程式的服務主體。提供在上一個步驟中傳回的應用程式識別碼。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    傳回新的服務主體。授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

2. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### 透過 Azure CLI 手動提供認證

您已經建立了 AD 應用程式，和該應用程式的服務主體。您已經將服務主體指派給角色。現在，您需要登入為應用程式以執行作業。當執行隨選指令碼或命令時，您可以手動提供應用程式的認證。

1. 找出包含服務主體之訂用帳戶的 **TenantId**。如果您正在擷取目前已驗證之訂用帳戶的租用戶識別碼，就不需要提供訂用帳戶 ID 做為參數。**-r** 參數會擷取不加引號的值。

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. 針對使用者名稱，使用您在建立服務主體時所使用的 **AppId**。如果您要擷取應用程式識別碼，請使用下列命令。在 **search** 參數中提供 Active Directory 應用程式的名稱。

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. 登入為服務主體。

        azure login -u "$appId" --service-principal --tenant "$tenantId"

    系統會提示您輸入密碼。提供您建立 AD 應用程式時指定的密碼。

        info:    Executing command login
        Password: ********

您現在驗證為您所建立之 AD 應用程式的服務主體。

## 建立具有憑證的 AD 應用程式

在本節中，您要執行的步驟是為 AD 應用程式建立可使用憑證進行驗證的服務主體。若要完成這些步驟，您必須安裝 [OpenSSL](http://www.openssl.org/)。

1. 建立自我簽署憑證。

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. 結合公開和私密金鑰。

        cat privkey.pem cert.pem > examplecert.pem

3. 開啟 **examplecert.pem** 檔案並複製憑證資料。尋找在 **-----BEGIN CERTIFICATE-----** 與 **-----END CERTIFICATE-----** 之間的一長串字元。

4. 執行 **azure ad app create** 命令，以建立新的 Active Directory 應用程式，並提供您在上一個步驟中複製的憑證資料做為金鑰值。

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    傳回 Active Directory 應用程式。需要有 AppId 屬性，才能建立服務主體以及取得存取權杖。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### 建立服務主體並且指派至角色

1. 建立應用程式的服務主體。提供在上一個步驟中傳回的應用程式識別碼。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    傳回新的服務主體。授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### 準備您的指令碼的值

在您的指令碼中您將會傳入三個值，需要登入為服務主體。您將需要：

- 應用程式識別碼
- 租用戶識別碼 
- 憑證指紋

您已在先前步驟中看過應用程式識別碼和憑證指紋。不過，如果您稍後需要擷取這些值，命令如下所示，同時列出取得租用戶識別碼的命令。

1. 若要擷取憑證指紋，然後移除不必要的字元，請使用：

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     這會傳回指紋值，類似：

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. 若要擷取租用戶識別碼，請使用︰

        azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId'

3. 若要擷取應用程式識別碼，請使用：

        azure ad app show --search exampleapp --json | jq -r '.[0].appId'

### 透過自動化的 Azure CLI 指令碼提供憑證

您已經建立了 Active Directory 應用程式，和該應用程式的服務主體。您已經將服務主體指派給角色。現在，您必須登入為服務主體，來執行服務主體的作業。

若要使用 Azure CLI 進行驗證，請提供憑證指紋、憑證檔案、應用程式識別碼和租用戶識別碼。

        azure login --service-principal --tenant {tenant-id} -u {app-id} --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

## 後續步驟
  
- 如需 .NET 驗證範例，請參閱 [Azure Resource Manager SDK for .NET](resource-manager-net-sdk.md)。
- 如需 Java 驗證範例，請參閱 [Azure Resource Manager SDK for Java](resource-manager-java-sdk.md)。 
- 如需 Python 驗證範例，請參閱[適用於 Python 的資源管理驗證](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)。
- 如需 REST 驗證範例，請參閱 [Resource Manager REST API](resource-manager-rest-api.md)。
- 如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。
- 如需使用憑證和 Azure CLI 的詳細資訊，請參閱 [從 Linux 命令列以憑證方式驗證 Azure 服務主體](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) 

<!---HONumber=AcomDC_0601_2016-->