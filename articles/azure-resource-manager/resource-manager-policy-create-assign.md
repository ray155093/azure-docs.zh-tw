---
title: "指派和管理 Azure 資源原則 | Microsoft Docs"
description: "描述如何將 Azure 資源原則套用至訂用帳戶和資源群組，以及如何檢視資源原則。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 5560b22f3f92a8e0a7cb8b973ef2e4c66bc32c06
ms.lasthandoff: 03/16/2017


---
# <a name="assign-and-manage-resource-policies"></a>指派和管理資源原則

若要實作原則，您必須執行三個步驟︰

1. 使用 JSON 定義原則規則。
2. 從您在上一個步驟中建立的 JSON 在您的訂用帳戶中建立原則定義。 這個步驟會使原則可供指派，但不會將規則套用至您的訂用帳戶。
3. 將原則指派給範圍 (例如訂用帳戶或資源群組)。 現在會強制執行原則的規則。

Azure 提供了一些預先定義的原則，可能會降低您需要定義的原則數目。 如果預先定義的原則適用於您的案例，請略過前兩個步驟，並將預先定義的原則指派給範圍。

本主題著重於建立原則定義，然後將該定義指派至範圍。 它不著重於建立原則定義的語法。 如需原則語法的詳細資訊，請參閱[資源原則概觀](resource-manager-policy.md)。

## <a name="rest-api"></a>REST API

### <a name="create-policy-definition"></a>建立原則定義

您可以使用 [適用於原則定義的 REST API](/rest/api/resources/policydefinitions)來建立原則。 REST API 可讓您建立和刪除原則定義，以及取得現有定義的相關資訊。

若要建立原則定義，請執行︰

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

納入如下範例的要求內文：

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>指派原則

您可以透過 [適用於原則指派的 REST API](/rest/api/resources/policyassignments)，在所需範圍內套用原則定義。 REST API 可讓您建立和刪除原則指派，以及取得現有指派的相關資訊。

若要建立原則指派，請執行：

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{policy-assignment} 是原則指派的名稱。

納入如下範例的要求內文：

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>檢視原則
若要取得原則，請使用[取得原則定義](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get)作業。

### <a name="get-aliases"></a>取得別名
您可以透過 REST API 擷取別名︰

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

下列範例顯示別名的定義。 如您所見，別名在不同 API 版本中均會定義路徑，無論屬性名稱是否變更。 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

### <a name="create-policy-definition"></a>建立原則定義
您可以使用 `New-AzureRmPolicyDefinition` cmdlet 建立原則定義。 以下範例會建立一個原則定義，只允許北歐和西歐中的資源。

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{
   "if": {
     "not": {
       "field": "location",
       "in": "[parameters(''allowedLocations'')]"
     }
   },
   "then": {
     "effect": "deny"
   }
 }' -Parameter '{
     "allowedLocations": {
       "type": "array",
       "metadata": {
         "description": "An array of permitted locations for resources.",
         "strongType": "location",
         "displayName": "List of locations"
       }
     }
 }'
```            

輸出會儲存在於原則指派期間使用的 `$policy` 物件中。 

您不需要指定 JSON 做為參數，而可以提供路徑給包含原則規則的 .json 檔案。

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>指派原則

使用 `New-AzureRmPolicyAssignment` cmdlet 將原則套用至所需範圍︰

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$array = @("West US", "West US 2")
$param = @{"allowedLocations"=$array}
New-AzureRMPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId -PolicyDefinition $policy -PolicyParameterObject $param
```

### <a name="view-policies"></a>檢視原則

若要取得所有的原則指派，請使用：

```powershell
Get-AzureRmPolicyAssignment
```

若要取得特定的原則，請使用：

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId
```

若要檢視原則定義的原則規則，請使用：

```powershell
(Get-AzureRmPolicyDefinition -Name regionPolicyDefinition).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>移除原則指派 

若要移除原則指派，請使用：

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20"></a>Azure CLI 2.0

### <a name="create-policy-definition"></a>建立原則定義

您可以使用 Azure CLI 2.0 搭配原則定義命令來建立原則定義。 以下範例會建立一個原則，只允許北歐和西歐中的資源。

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>指派原則

您可以使用原則指派命令將原則套用至所需的範圍：

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>檢視原則定義
若要取得原則定義，請使用下列命令：

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>移除原則指派 

若要移除原則指派，請使用：

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0

### <a name="create-policy-definition"></a>建立原則定義

您可以使用 Azure CLI 搭配原則定義命令來建立原則定義。 以下範例會建立一個原則，只允許北歐和西歐中的資源。

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

您可指定包含該原則的 .json 檔案路徑，而不以內嵌方式指定該原則。

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>指派原則

您可以使用原則指派命令將原則套用至所需的範圍：

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

此處的範圍是您指定之資源群組的名稱。 如果 policy-definition-id 參數的值為未知，您可以透過 Azure CLI 取得該值。 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>檢視原則
若要取得原則，請使用下列命令：

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>移除原則指派 

若要移除原則指派，請使用：

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>後續步驟
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。


