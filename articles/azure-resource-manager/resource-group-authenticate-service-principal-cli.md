---
title: "使用 Azure CLI 建立 Azure App 的身分識別 | Microsoft Docs"
description: "描述如何使用 Azure CLI 建立 Active Directory 應用程式和服務主體，並透過角色型存取控制將存取權授與資源。 它示範如何使用密碼或憑證來驗證應用程式。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 76c5bdeb2a27b733d8566c7a19f9457feebdc273


---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>使用 Azure CLI 建立用來存取資源的服務主體
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Azure CLI](resource-group-authenticate-service-principal-cli.md)
> * [入口網站](resource-group-create-service-principal-portal.md)
> 
> 

當您有 App 或指令碼需要存取資源時，可以設定 App 的身分識別並使用它自己的認證進行驗證。 以您自己的認證執行 App 是比較好的作法，因為︰

* 您可以對 App 身分識別指派不同於您自己權限的權限。 一般而言，這些權限只會限制為 App 必須執行的確切權限。
* 如果您的職責變更，就不需要變更 App 的認證。 
* 您可以使用憑證在執行自動指令碼時自動進行驗證。

本主題說明如何使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](../xplat-cli-install.md) 來設定應用程式，讓它利用自己的認證和身分識別來執行。

在 Azure CLI 中，您有&2; 個選項可以驗證您的 AD 應用程式︰

* password
* 憑證

本主題說明如何在 Azure CLI 中使用這兩個選項。 如果您想要從程式設計架構 (例如 Python、Ruby 或 Node.js) 登入 Azure，密碼驗證可能是您的最佳選項。 在決定是要使用密碼還是憑證時，請參閱 [範例應用程式](#sample-applications) 一節，以查看在不同架構進行驗證的範例。

## <a name="active-directory-concepts"></a>Active Directory 概念
在本文中，您會建立 Active Directory (AD) 應用程式和服務主體這兩個物件。 AD 應用程式是您的應用程式的全域表示法。 其中包含認證 (應用程式識別碼以及密碼或憑證)。 服務主體是您的應用程式在 Active Directory 中的本機表示法。 其中包含角色指派。 本主題著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。 您通常會將單一租用戶應用程式用在組織內執行的企業營運系統應用程式。 在單一租用戶應用程式中，您有一個 AD 應用程式和一個服務主體。

您可能想知道 - 為什麼我需要這兩個物件？ 當您考慮多租用戶應用程式時，這個方法比較合適。 您通常會將多租用戶應用程式用於軟體即服務 (SaaS) 應用程式，以便您的應用程式在許多不同的訂用帳戶中執行。 對於多租用戶應用程式，您有一個 AD 應用程式和多個服務主體 (每個 Active Directory 中有一個服務主體可授與應用程式的存取權)。 若要設定多租用戶應用程式，請參閱 [利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

## <a name="required-permissions"></a>所需的權限
若要完成本主題，您必須在 Azure Active Directory 和您的 Azure 訂用帳戶中有足夠的權限。 具體來說，您必須能夠在 Active Directory 中建立應用程式，並將服務主體指派給角色。 

檢查您的帳戶是否具有足夠的權限，最簡單的方式是透過入口網站。 請參閱[在入口網站中檢查必要的權限](resource-group-create-service-principal-portal.md#required-permissions)。

現在，繼續進行[密碼](#create-service-principal-with-password)或[憑證](#create-service-principal-with-certificate)驗證的章節。

## <a name="create-service-principal-with-password"></a>使用密碼建立服務主體
在本節中，您可以執行步驟來使用密碼建立 AD 應用程式，並將讀取者角色指派給服務主體。

讓我們逐步進行這些步驟。

1. 登入您的帳戶。
   
   ```azurecli
   azure login
   ```
2. 您有兩個建立 AD 應用程式的選項。 您可以在一個步驟中一起建立 AD 應用程式和服務主體，或將它們分開建立。 如果您不需要指定應用程式的首頁和識別碼 URI，請在一個步驟中一起建立。 如果您需要為 Web 應用程式設定這些值，請將它們分開建立。 此步驟會同時顯示這兩個選項。
   
   * 若要在一個步驟中建立 AD 應用程式和服務主體，請提供應用程式名稱和密碼，如下列命令所示︰
     
     ```azurecli
     azure ad sp create -n exampleapp -p {your-password}
     ```
   * 若要分別建立 AD 應用程式，請提供︰

      * App 的名稱
      * App 首頁的 URL
      * 識別 App 的逗號分隔 URI 清單
      * password

      如下列命令所示：
     
     ```azurecli
     azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p {Your_Password}
     ```

       上述命令會傳回 AppId 值。 若要建立服務主體，請提供該值做為下列命令的參數︰
     
     ```azurecli
     azure ad sp create -a {AppId}
     ```
     
     如果您的帳戶沒有 Active Directory 的[必要權限](#required-permissions)，您會看到一則錯誤訊息，指出 "Authentication_Unauthorized" 或「在內容中找不到任何訂用帳戶」。
     
     兩個選項都會傳回新的服務主體。 授與權限時，需要 `Object Id`。 在登入時，則需要隨 `Service Principal Names` 列出的 GUID。 此 GUID 是和應用程式識別碼相同的值。 在範例應用程式中，這個值稱為 `Client ID`。 
     
     ```azurecli
     info:    Executing command ad sp create
     
     Creating application exampleapp
       / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
       data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
       data:    Display Name:            exampleapp
       data:    Service Principal Names:
       data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
       data:                             https://www.contoso.org/example
       info:    ad sp create command OK
      ```

3. 授與服務主體對您訂用帳戶的權限。 在此範例中，您會將服務主體新增至「讀取者」角色，以授與讀取訂用帳戶中所有資源的權限。 若為其他角色，請參閱 [RBAC︰內建角色](../active-directory/role-based-access-built-in-roles.md)。 針對 `objectid` 參數，提供您在建立應用程式時所使用的 `Object Id`。 執行此命令之前，您必須允許一些時間讓新的服務主體在整個 Active Directory 中傳播。 當您手動執行這些命令時，通常工作與工作之間經過的時間已足夠。 您應該在指令碼中的命令之間加入睡眠步驟 (例如`sleep 15`)。 如果您看到主體不存在於目錄中的錯誤訊息，請重新執行命令。
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
     如果您的帳戶沒有足夠的權限可指派角色，您會看到一則錯誤訊息。 此訊息說明您的帳戶「沒有權限來對 '/subscriptions/{guid}' 範圍執行 'Microsoft.Authorization/roleAssignments/write' 動作」。

就這麼簡單！ 您的 AD 應用程式和服務主體已設定好。 下一節會說明如何透過 Azure CLI 以認證來登入。 如果您想要在您的程式碼應用程式中使用認證，則不需要繼續進行本主題。 您可以跳到 [範例應用程式](#sample-applications) ，以查看使用應用程式識別碼和密碼來登入的範例。 

### <a name="provide-credentials-through-azure-cli"></a>透過 Azure CLI 提供認證
現在，您需要以應用程式的形式登入以執行作業。

1. 每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Active Directory 的執行個體。 若要擷取目前已驗證訂用帳戶的租用戶識別碼，請使用︰
   
   ```azurecli
   azure account show
   ```
   
     它會傳回：
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     如果您需要取得其他訂用帳戶的租用戶識別碼，請使用下列命令︰
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 如果您需要擷取用戶端識別碼以便用於登入，請使用︰
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     要用於登入的值是服務主體名稱中所列出的 GUID。
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. 以服務主體的形式登入。
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    系統會提示您輸入密碼。 提供您建立 AD 應用程式時指定的密碼。
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

您現在已驗證為您所建立之服務主體的服務主體。

或者，您可以從命令列叫用 REST 作業來登入。 從驗證回應，您可以擷取存取權杖以用於其他作業。 如需叫用 REST 作業擷取存取權杖的範例，請參閱[產生存取權杖](resource-manager-rest-api.md#generating-an-access-token)。

## <a name="create-service-principal-with-certificate"></a>使用憑證建立服務主體
在本節中，您會執行下列步驟：

* 建立自我簽署憑證
* 建立 AD 應用程式 (包含憑證) 和服務主體
* 將 [讀取者角色] 指派給服務主體

若要完成這些步驟，您必須安裝 [OpenSSL](http://www.openssl.org/) 。

1. 建立自我簽署憑證。
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```
2. 結合公開和私密金鑰。
   
   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```
3. 開啟 **examplecert.pem** 檔案並尋找在 **-----BEGIN CERTIFICATE-----** 與 **-----END CERTIFICATE-----** 之間的一長串字元。 複製憑證資料。 您會在建立服務主體時將此資料當作參數傳遞。
4. 登入您的帳戶。
   
   ```azurecli
   azure login
   ```
5. 您有兩個建立 AD 應用程式的選項。 您可以在一個步驟中一起建立 AD 應用程式和服務主體，或將它們分開建立。 如果您不需要指定應用程式的首頁和識別碼 URI，請在一個步驟中一起建立。 如果您需要為 Web 應用程式設定這些值，請將它們分開建立。 此步驟會同時顯示這兩個選項。
   
   * 若要在一個步驟中建立 AD 應用程式和服務主體，請提供應用程式名稱和憑證資料，如下列命令所示︰
     
     ```azurecli
     azure ad sp create -n exampleapp --cert-value {certificate data}
     ```
   * 若要分別建立 AD 應用程式，請提供︰
      
      * App 的名稱
      * App 首頁的 URL
      * 識別 App 的逗號分隔 URI 清單
      * 憑證資料

      如下列命令所示：

     ```azurecli
     azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value {certificate data}
     ```
     
       上述命令會傳回 AppId 值。 若要建立服務主體，請提供該值做為下列命令的參數︰
     
     ```azurecli
     azure ad sp create -a {AppId}
     ```
     
     如果您的帳戶沒有 Active Directory 的[必要權限](#required-permissions)，您會看到一則錯誤訊息，指出 "Authentication_Unauthorized" 或「在內容中找不到任何訂用帳戶」。
     
     兩個選項都會傳回新的服務主體。 授與權限時，需要物件識別碼。 在登入時，則需要隨 `Service Principal Names` 列出的 GUID。 此 GUID 是和應用程式識別碼相同的值。 在範例應用程式中，這個值稱為 `Client ID`。 
     
     ```azurecli
     info:    Executing command ad sp create
     
     Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
       data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
       data:    Display Name:     exampleapp
       data:    Service Principal Names:
       data:                      4fd39843-c338-417d-b549-a545f584a745
       data:                      https://www.contoso.org/example
       info:    ad sp create command OK
     ```
6. 授與服務主體對您訂用帳戶的權限。 在此範例中，您會將服務主體新增至「讀取者」角色，以授與讀取訂用帳戶中所有資源的權限。 若為其他角色，請參閱 [RBAC︰內建角色](../active-directory/role-based-access-built-in-roles.md)。 針對 `objectid` 參數，提供您在建立應用程式時所使用的 `Object Id`。 執行此命令之前，您必須允許一些時間讓新的服務主體在整個 Active Directory 中傳播。 當您手動執行這些命令時，通常工作與工作之間經過的時間已足夠。 您應該在指令碼中的命令之間加入睡眠步驟 (例如`sleep 15`)。 如果您看到主體不存在於目錄中的錯誤訊息，請重新執行命令。
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
     如果您的帳戶沒有足夠的權限可指派角色，您會看到一則錯誤訊息。 此訊息說明您的帳戶「沒有權限來對 '/subscriptions/{guid}' 範圍執行 'Microsoft.Authorization/roleAssignments/write' 動作」。

### <a name="provide-certificate-through-automated-azure-cli-script"></a>透過自動化的 Azure CLI 指令碼提供憑證
現在，您需要以應用程式的形式登入以執行作業。

1. 每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Active Directory 的執行個體。 若要擷取目前已驗證訂用帳戶的租用戶識別碼，請使用︰
   
   ```azurecli
   azure account show
   ```
   
     它會傳回：
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     如果您需要取得其他訂用帳戶的租用戶識別碼，請使用下列命令︰
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 若要擷取憑證指紋，然後移除不必要的字元，請使用：
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
     這會傳回指紋值，類似：
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. 如果您需要擷取用戶端識別碼以便用於登入，請使用︰
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
     要用於登入的值是服務主體名稱中所列出的 GUID。
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. 以服務主體的形式登入。
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

## <a name="change-credentials"></a>管理認證

若要變更 AD 應用程式認證，不管是因為安全性危害或認證過期，請使用`azure ad app set`。

若要變更密碼，使用：

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

若要變更憑證值，使用︰

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```


## <a name="sample-applications"></a>範例應用程式
下列範例應用程式會顯示如何登入為服務主體。

**.NET**

* [使用 .NET 以範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [使用 .NET 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [開始使用資源 - 在 Java 中使用 Azure Resource Manager 範本進行部署](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [開始使用資源 - 在 Java 中管理資源群組](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [使用 Python 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [使用 Python 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [使用 Node.js 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [使用 Node.js 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [使用 Ruby 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [使用 Ruby 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>後續步驟
* 如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱 [利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。
* 如需使用憑證和 Azure CLI 的詳細資訊，請參閱 [從 Linux 命令列以憑證方式驗證 Azure 服務主體](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)。 




<!--HONumber=Jan17_HO4-->


