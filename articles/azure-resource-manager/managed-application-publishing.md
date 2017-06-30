---
title: "建立及發佈 Azure 受管理的應用程式 | Microsoft Docs"
description: "示範 ISV 或合作夥伴如何建立 Azure 受管理的應用程式"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/08/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 56dd68e328abd6c1dacdf7a8e051ca6b3cd07083
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# <a name="create-and-publish-an-azure-managed-application"></a>建立及發佈 Azure 受管理的應用程式 

如[受管理的應用程式概觀](managed-application-overview.md)文章中所述，端對端體驗中有兩種案例。 其中一個是發行者 (或稱為 ISV)，是要建立受管理的應用程式供客戶使用。 第二個是受管理應用程式的客戶或取用者。 本文著重於第一個案例，並說明 ISV 要如何建立及發佈受管理的應用程式。 

若要建立受管理的應用程式，您必須建立︰

* 包含範本檔案的套件
* 可存取客戶訂用帳戶中的資源群組之使用者、群組或應用程式
* 設備定義

如需檔案的範例，請參閱[受管理的應用程式範例](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)。

## <a name="create-managed-application-package"></a>建立受管理的應用程式套件

第一個步驟是建立包含主要範本檔案的受管理應用程式套件。 發行者或 ISV 會建立三個檔案。 

* 第一個檔案稱為 **applianceMainTemplate.json**。 此範本檔案會將佈建作為受管理應用程式一部分的實際資源進行定義。 例如，若要使用受管理的應用程式來建立儲存體帳戶，applianceMainTemplate.json 會包含︰ 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {       
          }
      }],
      "outputs": {      
      }
  }
  ```

* 發行者需要建立的第二個檔案是 **mainTemplate.json**。 範本檔案僅包含設備資源 (Microsoft.Solutions/appliances)。 它還包含 applianceMainTemplate.json 中的資源所需之所有參數。 

  在建立受管理的應用程式期間，需要兩個重要的屬性作為輸入。 **managedResourceGroupId** 屬性是資源群組的識別碼，當中會建立 applianceMainTemplate.json 中所定義的資源。 識別碼的格式為：`/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`

  **applianceDefinitionId** 屬性是受管理應用程式定義資源的識別碼。 識別碼的格式︰`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}`

  提供這兩個值的參數，讓取用者在建立受管理的應用程式時可以指定它們。 在下列範例中，對應至這些屬性的兩個參數是 managedByResourceGroup 和 applianceDefinitonId。

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }             
              }
          }
      }]
  }
  ```

* 套件中所需的第三個檔案是 **createUiDefinition.json**。 Azure 入口網站會使用這個檔案，為要建立受管理應用程式的取用者產生使用者介面。 您要定義受管理應用程式的參數，以及取用者要如何取得每個參數的輸入。 您可以使用諸如下拉式清單選擇器、文字方塊、密碼方塊和其他輸入工具等選項。 若要了解如何建立受管理應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。

所有必要檔案就緒後，您要將套件上傳至可從中將它取用的可存取位置。


## <a name="create-azure-ad-user-group-or-application"></a>建立 Azure AD 使用者群組或應用程式
接下來，建立您想要用來代表客戶管理資源的使用者群組或應用程式。 此使用者群組或應用程式具有受管理資源群組的權限，如角色所述。 角色可以是任何內建的 RBAC 角色，例如**擁有者**或**參與者**。 也可以將管理資源的權限提供給個別使用者，但您通常要指派此權限才可使用使用者群組。 若要建立新的 Active Directory 使用者群組，請使用：

```azurecli
az ad group create --display-name "name" --mail-nickname "nickname"
```

您也可以使用現有的群組。 您需要新建或現有使用者群組的物件識別碼。 下列範例會示範如何從用來建立群組的顯示名稱取得物件識別碼。

```azurecli
az ad group show --group "groupName"
```

範例：

```azurecli
az ad group show --group ravAppliancetestADgroup
```

它會傳回下列輸出︰

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
您需要上述的 objectId 值。 

## <a name="get-the-role-definition-id"></a>取得角色定義識別碼

接下來，您需要的角色定義識別碼，是您想要授與使用者、使用者群組或應用程式存取權的 RBAC 內建角色。 通常您會需要使用「擁有者」或「參與者」或「讀取者」角色。 下列命令會顯示如何取得「擁有者」角色的角色定義識別碼：

```azurecli
az role definition list --name owner
```

它會傳回下列輸出︰

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

您需要上述範例中的「名稱」屬性值。


## <a name="create-the-managed-application-definition"></a>建立受管理的應用程式定義

最後一個步驟是建立受管理的應用程式定義資源。 一旦您建立設備套件和授權後，請使用下列命令來建立設備定義︰ 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

上述範例中使用的參數：

- resource-group - 資源群組的名稱，當中會建立設備定義。
- lock-level - 放在受管理資源群組的鎖定類型。 它可以避免客戶在此資源群組上執行非預期的作業。 目前，唯一的支援鎖定等級是 **ReadOnly**。 指定 ReadOnly 時，客戶只能讀取受管理資源群組中存在的資源。
- authorizations - 描述用來授與權限給受管理資源群組的主體識別碼及角色定義識別碼。 它是以 `<principalId>:<roleDefinitionId>` 格式來指定。 這個屬性也可以指定多個值。 如果需要多個值，應該以 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 這種形式來指定。 多個值之間以空格分隔。
- package-file-uri - 包含範本檔案之設備套件的位置，它可以是 Azure 儲存體 Blob。 


## <a name="next-steps"></a>後續步驟

* 如需受管理應用程式的簡介，請參閱 [Azure 受管理的應用程式概觀](managed-application-overview.md)。
* 如需檔案的範例，請參閱[受管理的應用程式範例](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)。
* 若要了解取用者體驗，請參閱[使用 Azure 受管理的應用程式](managed-application-consumption.md)。
* 若要了解如何建立受管理應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。

