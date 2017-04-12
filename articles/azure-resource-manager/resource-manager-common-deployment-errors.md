---
title: "針對常見的 Azure 部署錯誤進行疑難排解 | Microsoft Docs"
description: "說明如何解決使用 Azure Resource Manager 將資源部署至 Azure 時的常見錯誤。"
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "部署錯誤, azure 部署, 部署至 azure"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: bfbb3356454b9ef8b1834d03e7b76de9860a12c9
ms.lasthandoff: 04/03/2017


---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解
本主題說明如何解決可能會遇到的一些常見 Azure 部署錯誤。

## <a name="two-types-of-errors"></a>兩種錯誤類型
您可能會收到兩種錯誤類型︰

* 驗證錯誤
* 部署錯誤

下圖顯示訂用帳戶的活動記錄檔。 有三個作業在兩個部署中發生。 在第一個部署中，範本已通過驗證，但在建立資源時失敗 (**寫入部署**)。 在第二個部署中，範本驗證失敗，而且未進行到**寫入部署**。

![顯示錯誤碼](./media/resource-manager-common-deployment-errors/show-activity-log.png)

從預先判斷會造成問題的情況下會發生驗證錯誤。 驗證錯誤包含您範本中的語法錯誤，或者嘗試部署會超出您的訂用帳戶配額的資源。 部署程序期間出現的情況下會發生驗證錯誤。 例如，嘗試存取以平行方式部署的資源可能會發生部署錯誤。

這兩種錯誤類型都會傳回錯誤碼，以供您針對部署進行疑難排解。 這兩種錯誤類型都會出現在[活動記錄](resource-group-audit.md)中。 不過，驗證錯誤不會出現在部署歷程記錄中，因為部署永遠不會啟動。


## <a name="error-codes"></a>錯誤碼

本主題會說明下列錯誤碼︰

* [AccountNameInvalid](#accountnameinvalid)
* [授權失敗](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [DisallowedOperation](#disallowedoperation)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

### <a name="deploymentfailed"></a>DeploymentFailed

這個錯誤碼會指出一般部署錯誤，但不是您要開始進行疑難排解的錯誤碼。 實際上協助您解決問題的錯誤碼通常位於此錯誤的下一層。 例如，下圖顯示在部署錯誤底下的 **RequestDisallowedByPolicy** 錯誤碼。

![顯示錯誤碼](./media/resource-manager-common-deployment-errors/error-code.png)

### <a name="skunotavailable"></a>SkuNotAvailable

在部署資源 (通常是虛擬機器) 時，您可能會收到下列錯誤碼和錯誤訊息︰

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

當您選取的資源 SKU (例如 VM 大小) 不適用於您選取的位置時，您會收到這個錯誤。 若要解決此問題，您需要判斷區域中有哪些 SKU。 您可以使用入口網站或 REST 作業來尋找可用的 SKU。

- 若要使用[入口網站](https://portal.azure.com)，請登入入口網站並透過介面加入資源。 當您設定值時，您會看到該資源可用的 SKU。 您不需要完成部署。

    ![可用的 SKU](./media/resource-manager-common-deployment-errors/view-sku.png)

- 若要為虛擬機器使用 REST API，請傳送下列要求︰

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  它會以下列格式傳回可用的 SKU 和區域︰

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

如果您在該區域或符合您業務需求的替代區域中找不到適當的 SKU，請連絡 [Azure 支援服務](https://portal.azure.com/#create/Microsoft.Support)。

### <a name="disallowedoperation"></a>DisallowedOperation

```
Code: DisallowedOperation
Message: The current subscription type is not permitted to perform operations on any provider 
namespace. Please use a different subscription.
```

如果您收到此錯誤，表示您使用的訂用帳戶不被允許存取 Azure Active Directory 以外的任何 Azure 服務。 當您需要存取傳統入口網站，但不被允許部署資源時，就表示您擁有的訂用帳戶可能是這種類型。 若要解決此問題，您必須使用有權部署資源的訂用帳戶。  

若要使用 PowerShell 檢視您可用的訂用帳戶，請使用︰

```powershell
Get-AzureRmSubscription
```

若要設定目前的訂用帳戶，請使用︰

```powershell
Set-AzureRmContext -SubscriptionName {subscription-name}
```

若要使用 Azure CLI 2.0 檢視您可用的訂用帳戶，請使用︰

```azurecli
az account list
```

若要設定目前的訂用帳戶，請使用︰

```azurecli
az account set --subscription {subscription-name}
```

### <a name="invalidtemplate"></a>InvalidTemplate
此錯誤可能起因於數個不同類型的錯誤。

- 語法錯誤

   如果您收到錯誤訊息指出無法驗證範本，則可能是範本中發生語法問題。

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   此錯誤很容易發生，因為範本運算式可能很複雜。 例如，儲存體帳戶的下列名稱指派包含一組方括號、三個函式、三組圓括號、一組單引號和一個屬性︰

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   如果您未提供相符的語法，範本會產生與您所要的值截然不同的值。

   當您收到此類錯誤時，請仔細檢閱運算式語法。 請考慮使用 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 或 [Visual Studio Code](resource-manager-vs-code.md) 等 JSON 編輯器，它們可以警告您有語法錯誤。

- 不正確的區段長度

   資源名稱的格式不正確時，就會發生另一種無效範本錯誤。

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   根層級資源的名稱必須比資源類型少一個區段。 每個區段是以斜線區分。 在下列範例中，類型具有 2 個區段，而名稱則有 1 個區段，因此是**有效名稱**。

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   但下一個範例則 **不是有效名稱** ，因為它的區段數目和類型相同。

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   對於子資源來說，類型和名稱具有相同的區段數目。 此區段數目很合理，因為子資源的完整名稱和類型包含父資源的名稱和類型。 因此，完整名稱較完整類型仍少一個區段。

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   對於跨資源提供者套用的 Resource Manager 類型而言，要讓區段保持正確可能很棘手。 例如，將資源鎖定套用至網站需要具有四個區段的類型。 因此，名稱會是三個區段：

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- 不應該複製索引

   當您將 **copy** 元素套用到不支援此元素的範本時，您會遇到此 **InvalidTemplate** 錯誤。 您只可以將複製元素套用到資源類型。 您不能將複製套用到資源類型內的屬性。 例如，您將複製套用到虛擬機器，但無法將它套用到虛擬機器的作業系統磁碟。 在某些情況下，您可以將子資源轉換成父資源，以建立複製迴圈。 如需有關使用複製的詳細資訊，請參閱 [在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)。

- 參數無效

   如果範本會指定參數允許的值，而您提供的值不是其中一個值，則會收到類似下列錯誤的訊息：

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   在範本中重複檢查允許的值，並在部署期間提供一個值。

- 偵測到循環相依性

   當資源以防止部署啟動的方式互相相依，您會收到這個錯誤。 只要有相互相依性的組合，就會讓兩個或多個資源等候其他也正在等候的資源。 例如，resource1 相依於 resource3、resource2 相依於 resource1，而 resource3 相依於 resource2。 您通常可以移除不必要的相依性來解決此問題。 如需針對相依性錯誤進行疑難排解的建議，請參閱[檢查部署順序](#check-deployment-sequence)。

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound 和 ResourceNotFound
當您的範本包含的資源名稱無法解析時，您會收到類似以下的錯誤：

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

如果您嘗試在範本中部署遺漏的資源，請檢查是否需要新增相依性。 如果可能，Resource Manager 會以平行方式建立資源，將部署最佳化。 如果一個資源必須在另一個資源之後部署，您就必須在範本中使用 **dependsOn** 元素來建立與該另一個資源的相依性。 例如，在部署 Web 應用程式時，App Service 方案必須存在。 如果您沒有指定該 Web 應用程式相依於 App Service 方案，Resource Manager 會同時建立這兩個資源。 您收到錯誤訊息，指出找不到 App Service 方案的資源，因為嘗試在 Web 應用程式上設定屬性時它尚未存在。 您會設定 Web 應用程式的相依性，以避免此錯誤。

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

如需針對相依性錯誤進行疑難排解的建議，請參閱[檢查部署順序](#check-deployment-sequence)。

當資源存在於所要部署不同的資源群組中，則也會看到此錯誤。 在該情況下，請使用 [resourceId 函式](resource-group-template-functions.md#resourceid)來取得資源的完整名稱。

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

如果您嘗試對無法解析的資源使用 [reference](resource-group-template-functions.md#reference) 或 [listKeys](resource-group-template-functions.md#listkeys) 函式，您會收到下列錯誤：

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

尋找包含 **reference** 函式的運算式。 重複檢查確定參數值正確。

### <a name="parentresourcenotfound"></a>ParentResourceNotFound

當某資源為另一個資源的父資源時，父資源必須在建立子資源之前就存在。 如果尚未存在，您會收到下列錯誤︰

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

子資源的名稱包含父系名稱。 例如，SQL Database 可能定義如下︰

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

但是，如果您尚未於父資源上指定相依性，子資源可能會在父資源之前進行部署。 若要解決這個錯誤，請包含相依性。

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />
### <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists 和 StorageAccountAlreadyTaken
對於儲存體帳戶，您必須提供在 Azure 中是唯一的資源名稱。 如果您未提供唯一的名稱，您會收到類似下列的錯誤：

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

您可以將您的命名慣例與 [uniqueString](resource-group-template-functions.md#uniquestring) 函式的結果串連，以建立一個唯一名稱。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

如果您以與您的訂用帳戶中現有的儲存體帳戶相同的名稱部署儲存體帳戶，但提供不同的位置，您會遇到錯誤，指出儲存體帳戶已存在於不同的位置。 刪除現有的儲存體帳戶，或提供與現有儲存體帳戶相同的位置。

### <a name="accountnameinvalid"></a>AccountNameInvalid
嘗試提供的儲存體帳戶名稱中包含禁止的字元時，您會看到 **AccountNameInvalid** 錯誤。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能使用數字和小寫字母。 [uniqueString](resource-group-template-functions.md#uniquestring) 函式會傳回 13 個字元。 如果您要對 **uniqueString** 結果串連前置詞，請提供 11 個字元以下的前置詞。

### <a name="badrequest"></a>BadRequest

提供的屬性值無效時，您可能會碰到 BadRequest 狀態。 例如，如果您針對儲存體帳戶提供不正確的 SKU 值，則部署會失敗。 若要判斷屬性的有效值，請查看 [REST API](/rest/api) 了解您要部署的資源類型。

<a id="noregisteredproviderfound" />
### <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound 和 MissingSubscriptionRegistration
在部署資源時，您可能會收到下列錯誤代碼和訊息︰

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

或者，您可能會收到類似訊息，指出︰

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

您會因為下列三個原因其中一個而收到此錯誤︰

1. 尚未向您的訂用帳戶註冊此資源提供者
2. 此資源類型不支援 API 版本
3. 此資源類型不支援位置

錯誤訊息應可提供給您支援的位置和 API 版本建議。 您可以將您的範本變更為其中一個建議的值。 Azure 入口網站或正在使用的命令列介面會自動註冊大部分的提供者；但並非全部。 如果您未曾使用特定的資源提供者，您可能需要註冊該提供者。 您可以透過 PowerShell 或 Azure CLI，深入探索資源提供者。

**入口網站**

您可以看到註冊狀態，並透過入口網站註冊資源提供者命名空間。

1. 對於您的訂用帳戶，選取**資源提供者**。

   ![選取資源提供者](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. 查看資源提供者的清單，並視需要選取 [註冊] 連結以註冊您嘗試部署的資源提供者類型。

   ![列出資源提供者](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

若要查看您的註冊狀態，請使用 **Get-AzureRmResourceProvider**。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

若要註冊提供者，請使用 **Register-AzureRmResourceProvider** ，並提供您想要註冊的資源提供者名稱。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

若要取得支援特定資源類型的位置，請使用︰

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

若要取得支援特定資源類型的 API 版本，請使用︰

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

**Azure CLI**

若要查看是否已註冊該提供者，請使用 `azure provider list` 命令。

```azurecli
az provider list
```

若要註冊資源提供者，請使用 `azure provider register` 命令，然後指定要註冊的*命名空間*。

```azurecli
az provider register --namespace Microsoft.Cdn
```

若要查看資源類型所支援的位置和 API 版本，請使用︰

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

<a id="quotaexceeded" />
### <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded 和 OperationNotAllowed
當部署超過配額時 (也許是每個資源群組、訂用帳戶、帳戶及其他範圍的配額)，可能會發生問題。 例如，您的訂用帳戶可能設定為要限制區域的核心數目。 如果您嘗試部署超過允許核心數目的虛擬機器，您會收到錯誤訊息指出已超過配額。
如需完整的配額資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-subscription-service-limits.md)。

若要檢查您訂用帳戶的核心配額，可以在 Azure CLI 中使用 `azure vm list-usage` 命令。 以下範例示範核心配額為 4 的免費試用帳戶：

```azurecli
az vm list-usage --location "South Central US"
```

它會傳回：

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

如果您部署的範本會在美國西部區域中建立四個以上的核心，您會看到類似以下的部署錯誤：

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

或者，您可以在 PowerShell 中使用 **Get-AzureRmVMUsage** Cmdlet。

```powershell
Get-AzureRmVMUsage
```

它會傳回：

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

在這些情況下，您應該移至入口網站，並提出支援問題，以針對您想要部署的區域提高配額。

> [!NOTE]
> 請記住，對於資源群組，配額適用於每個個別區域，而不是整個訂用帳戶。 如果您需要在美國西部部署 30 個核心，就必須要求在美國西部擁有 30 個資源管理員核心。 如果您需要在任何具有存取權限的區域中部署 30 個核心，就應該要求在所有區域中擁有 30 個 Resource Manager 核心。
>
>

### <a name="invalidcontentlink"></a>InvalidContentLink
當您收到錯誤訊息：

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

您最有可能嘗試連結至無法使用的巢狀範本。 再次確認您為巢狀範本提供的 URI。 如果儲存體帳戶中已有範本，請確定 URI 可存取。 您可能需要傳送 SAS 權杖。 如需詳細資訊，請參閱 [透過 Azure 資源管理員使用連結的範本](resource-group-linked-templates.md)。

### <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
當訂用帳戶包含會讓您無法在部署期間嘗試執行某個動作的資源原則時，您就會收到這個錯誤。 請在錯誤訊息中尋找原則識別碼。

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

在 **PowerShell** 中，提供該原則識別碼做為 **Id** 參數，以擷取有關封鎖了部署之原則的詳細資料。

```powershell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

在 **Azure CLI 2.0** 中，提供原則定義的名稱︰

```azurecli
az policy definition show --name regionPolicyAssignment
```

如需有關原則的詳細資訊，請參閱 [使用原則來管理資源和控制存取](resource-manager-policy.md)。

### <a name="authorization-failed"></a>授權失敗
您可能會在部署期間收到錯誤，因為嘗試部署資源的帳戶或服務主體並有執行這些動作的存取權。 Azure Active Directory 可讓您或您的系統管理員最精確地控制哪些身分識別可以存取哪些資源。 例如，如果您的帳戶被指派「讀取者」角色，您將無法建立資源。 在此情況下，您會看到錯誤訊息，指出授權失敗。

如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

## <a name="troubleshooting-tricks-and-tips"></a>疑難排解秘訣和提示

### <a name="enable-debug-logging"></a>啟用偵錯記錄
您可以記錄要求和 (或) 回應，以探索關於部署處理情形的寶貴資訊。

- PowerShell

   在 PowerShell 中，將 **DeploymentDebugLogLevel** 參數設定為 [All]、[ResponseContent] 或 [RequestContent]。

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   檢查具有下列 Cmdlet 的要求內容︰

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   或者，具有下列項目的回應內容︰

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   此資訊可協助您判斷範本中的值是否會正確設定。

- Azure CLI 2.0

   使用下列命令檢查部署作業︰

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- 巢狀範本

   若要記錄巢狀範本的偵錯資訊，請使用 **debugSetting** 項目。

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


### <a name="create-a-troubleshooting-template"></a>建立疑難排解範本
在某些情況下，針對範本進行疑難排解的最簡單方式是測試其組件。 您可以建立簡化範本，以便專注於您認為是錯誤成因的組件。 例如，假設您在參考資源時收到錯誤。 不需要處理整個範本，而是建立一個會傳回可能造成問題之組件的範本。 它可協助您判斷是否傳入正確的參數、正確地使用範本函式，以及取得所預期的資源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

或者，假設您遇到您認為與未正確設定之相依性有關的部署錯誤。 將其細分為簡化範本以測試您的範本。 首先，建立可部署單一資源 (例如 SQL Server) 的範本。 當您確定已正確定義該資源時，加入依存該資源的資源 (例如 SQL Database)。 當您正確定義這兩個資源時，加入其他相依的資源 (例如稽核原則)。 在每個測試部署之間，刪除資源群組以確保您充分測試相依性。 

### <a name="check-deployment-sequence"></a>檢查部署順序

當資源是以非預期的順序部署時，會發生許多部署錯誤。 相依性未正確設定時，就會發生這些錯誤。 當您遺漏必要的相依性時，一個資源會嘗試使用另一個資源的值，但另一個資源還不存在。 您會收到錯誤表示找不到資源。 您可能會不斷遇到這種錯誤類型，因為每個資源的部署時間可能有所不同。 例如，您第一次部署資源成功，因為必要的資源恰好即時完成。 不過，您第二次嘗試失敗，因為必要的資源沒有即時完成。 

但是，您要避免設定不需要的相依性。 當您有不必要的相依性時，您會阻止不互相相依的資源以平行方式部署，而延長部署的時間。 此外，您可以建立封鎖部署的循環相依性。 [reference](resource-group-template-functions.md#reference) 函式會對您在函式中指定為參數的資源建立隱含的相依性 (如果在同一個範本中部署該資源)。 因此，您的相依性可能會比 **dependsOn** 屬性中指定的相依性還多。 [resourceId](resource-group-template-functions.md#resourceid) 函式不會建立隱含的相依性或驗證資源存在。

當您遇到相依性問題時，您需要深入了解資源部署的順序。 若要檢視部署作業的順序︰

1. 選取資源群組的部署歷程記錄。

   ![選取部署歷程記錄](./media/resource-manager-common-deployment-errors/select-deployment.png)

2. 從歷程記錄中選取部署，然後選取 [事件]。

   ![選取部署事件](./media/resource-manager-common-deployment-errors/select-deployment-events.png)

3. 檢查每個資源的事件順序。 注意每個作業的狀態。 例如，下列映像顯示平行部署的三個儲存體帳戶。 請注意，三個儲存體帳戶會同時啟動。

   ![平行部署](./media/resource-manager-common-deployment-errors/deployment-events-parallel.png)

   下一個映像顯示非平行部署的三個儲存體帳戶。 第二個儲存體帳戶相依於第一個儲存體帳戶，而第三個儲存體帳戶相依於第二個儲存體帳戶。 因此，第一個儲存體帳戶會在下一個儲存體帳戶啟動之前啟動、接受及完成。

   ![連續部署](./media/resource-manager-common-deployment-errors/deployment-events-sequence.png)

真實世界的案例可能會相當複雜，但您可以使用相同的技巧來探索每個資源的部署何時啟動及完成。 請查閱您的部署事件以查看順序是否與您預期的不同。 如果是，請重新評估此資源的相依性。

Resource Manager 範本會在驗證期間識別循環相依性。 它會傳回錯誤訊息，特別指出循環相依性存在。 解決循環相依性︰

1. 在範本中，找出循環相依性中所識別的資源。 
2. 針對該資源，檢查 **dependsOn** 屬性和任何使用的 **reference** 函式，查看相依於哪些資源。 
3. 檢查這些資源，查看所相依的資源。 跟隨相依性，直到您找出相依於原始資源的資源。
5. 針對參與循環相依性的資源，仔細檢查所有使用的 **dependsOn** 屬性，以識別不需要的任何相依性。 移除這些相依性。 如果您不確定是否需要某個相依性，請嘗試移除它。 
6. 重新部署範本。

移除 **dependsOn** 屬性的值可能會在您部署範本時導致錯誤。 如果您遇到錯誤，請將相依性新增回範本。 

如果該方法無法解決循環相依性，請考慮將一部分部署邏輯移到子資源 (例如擴充或設定值)。 設定這些子資源在參與循環相依性的資源之後才進行部署。 例如，假設您要部署兩部虛擬機器，但是您必須分別在上面設定互相參考的屬性。 您可以採取下列順序部署︰

1. vm1
2. vm2
3. vm1 的擴充相依於 vm1 和 vm2。 擴充在 vm1 上設定從 vm2 取得的值。
4. vm2 的擴充相依於 vm1 和 vm2。 擴充在 vm2 上設定從 vm1 取得的值。

相同的方法也適用於 App Service 應用程式。 請考慮將設定值移入應用程式資源的子資源。 您可以採取下列順序部署兩個 Web 應用程式︰

1. webapp1
2. webapp2
3. webapp1 的設定相依於 webapp1 和 webapp2。 它包含使用 webapp2 的值的應用程式設定。
4. webapp2 的設定相依於 webapp1 和 webapp2。 它包含使用 webapp1 的值的應用程式設定。

## <a name="troubleshooting-other-services"></a>對其他服務進行疑難排解
如果先前的部署錯誤碼未能協助您疑難排解問題，您可以針對每個 Azure 服務尋找更詳細的疑難排解指南。

下表列出虛擬機器的疑難排解主題。

| Error | 文章 |
| --- | --- |
| 自訂指令碼擴充功能錯誤 |[Windows VM 擴充失敗](../virtual-machines/windows/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />或<br />[Linux VM 擴充失敗](../virtual-machines/linux/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| OS 映像佈建錯誤 |[新的 Windows VM 錯誤](../virtual-machines/windows/troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />或<br />[新的 Linux VM 錯誤](../virtual-machines/linux/troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| 配置失敗 |[Windows VM 配置失敗](../virtual-machines/windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />或<br />[Linux VM 配置失敗](../virtual-machines/linux/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| 嘗試連線時發生安全殼層 (SSH) 錯誤 |[Linux VM 的安全殼層連線](../virtual-machines/linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| 連接至在 VM 上執行的應用程式時發生錯誤 |[在 Windows VM 上執行的應用程式](../virtual-machines/windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />或<br />[在 Linux VM 上執行的應用程式](../virtual-machines/linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| 遠端桌面連線錯誤 |[Windows VM 的遠端桌面連線](../virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 藉由重新部署解決的連線錯誤 |[將虛擬機器重新部署至新的 Azure 節點](../virtual-machines/windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 雲端服務錯誤 |[雲端服務部署問題](../cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

下表列出其他 Azure 服務的疑難排解主題。 它將重點放在部署或設定資源的相關問題。 如果您需要對資源執行階段問題進行疑難排解的說明，請參閱該 Azure 服務的文件。

| 服務 | 文章 |
| --- | --- |
| 自動化 |[Azure 自動化中常見錯誤的疑難排解秘訣](../automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack |[Microsoft Azure Stack 疑難排解](../azure-stack/azure-stack-troubleshooting.md) |
| Data Factory |[對 Data Factory 問題進行疑難排解](../data-factory/data-factory-troubleshoot.md) |
| Service Fabric |[監視和診斷 Azure Service Fabric 應用程式](../service-fabric/service-fabric-diagnostics-overview.md) |
| 站台復原 |[監視和疑難排解虛擬機器與實體伺服器的保護](../site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| 儲存體 |[監視、診斷與疑難排解 Microsoft Azure 儲存體](../storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple |[StorSimple 裝置部署問題的疑難排解](../storsimple/storsimple-troubleshoot-deployment.md) |
| SQL Database |[針對 Azure SQL Database 連線問題進行疑難排解](../sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL 資料倉儲 |[針對 Azure SQL 資料倉儲問題進行疑難排解](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="next-steps"></a>後續步驟
* 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](resource-group-audit.md)。
* 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](resource-manager-deployment-operations.md)。

