---
title: "Azure Resource Manager 範本函式 - 資源 | Microsoft Docs"
description: "描述 Azure Resource Manager 範本中用來擷取資源相關值的函式。"
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
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 66c71906614e5d0c8e8531271444fc59a5cb779f
ms.contentlocale: zh-tw
ms.lasthandoff: 04/28/2017


---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 範本的資源函式

資源管理員提供下列函式以取得資源值：

* [listKeys 和 list{Value}](#listkeys)
* [提供者](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [訂用帳戶](#subscription)

若要從參數、變數或目前的部署中取得值，請參閱 [部署值函式](resource-group-template-functions-deployment.md)。

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-and-listvalue"></a>listKeys 和 list{Value}
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

對支援 list 作業的任何資源類型傳回值。 最常見的用法是 `listKeys`。 

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| resourceName 或 resourceIdentifier |是 |字串 |資源的唯一識別碼。 |
| apiVersion |是 |字串 |資源執行階段狀態的 API 版本。 一般而言，格式為 **yyyy-mm-dd**。 |

### <a name="remarks"></a>備註

開頭為 **list** 的任何作業都可在您的範本中用為函式。 可用作業不只包含 listKeys，還包含像 `list`、`listAdminKeys` 和 `listStatus` 等作業。 為判斷哪一個資源類型具有 list 作業，您可以使用下列選項：

* 檢視資源提供者的 [REST API 作業](/rest/api/)，並尋找 list 作業。 例如，儲存體帳戶具有 [listKeys 作業](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys)。
* 使用 [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) PowerShell Cmdlet。 下列範例會取得儲存體帳戶的所有 list 作業︰

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* 使用下列 Azure CLI 命令，以及 JSON 公用程式 [jq (英文)](http://stedolan.github.io/jq/download/) 來只篩選出 list 作業：

  ```azurecli
  azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
  ```

使用 [resourceId 函式](#resourceid)或 `{providerNamespace}/{resourceType}/{resourceName}` 格式來指定資源。

### <a name="examples"></a>範例

下列範例顯示如何在 outputs 區段中從儲存體帳戶傳回主要和次要金鑰。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountId": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "storageKeysOutput": {
            "value": "[listKeys(parameters('storageAccountId'), '2016-01-01')]",
            "type" : "object"
        }
    }
}
``` 

### <a name="return-value"></a>傳回值

從 listKeys 傳回的物件具有下列格式︰

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

其他 list 函式具有不同的傳回格式。 若要查看函式的格式，請將函式放入 [輸出] 區段，如範本範例所示。 

<a id="providers" />

## <a name="providers"></a>提供者
`providers(providerNamespace, [resourceType])`

傳回資源提供者和其所支援資源類型的相關資訊。 如果未提供資源類型，則函式會傳回資源提供者所有的支援類型。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| providerNamespace |是 |字串 |提供者的命名空間 |
| resourceType |否 |字串 |所指定命名空間內的資源類型。 |

### <a name="examples"></a>範例

下列範例顯示如何使用 provider 函數：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>傳回值

每個支援類型都會以下列格式傳回： 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

不保證所傳回的值會有陣列順序。

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion])`

傳回代表資源執行階段狀態的物件。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| resourceName 或 resourceIdentifier |是 |string |資源的名稱或唯一識別碼。 |
| apiVersion |否 |字串 |指定的資源的 API 版本。 如果在相同的範本內未供應資源，則請包含此參數。 一般而言，格式為 **yyyy-mm-dd**。 |

### <a name="remarks"></a>備註

reference 函數會從執行階段狀態衍生其值，因此不能用在 variables 區段中。 它可以用於範本的 outputs 區段中。 

如果在相同的範本內佈建所參考的資源，則可使用 reference 函式來隱含宣告一個資源相依於另一個資源。 您不需要同時使用 dependsOn 屬性。 所參考的資源完成部署之前不會評估函式。

若要查看資源類型的屬性名稱和值，請建立一個會在 outputs 區段中傳回物件的範本。 如果您有一個該類型的現有資源，您的範本就會傳回物件，而不會部署任何新資源。 

### <a name="examples"></a>範例

下列範例會參考未部署在此範本中，但是存在於相同資源群組內的儲存體帳戶。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

或者，您可以部署並參考相同範本中的資源。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      }
    }
}
``` 

一般而言，您可以使用 reference 函式從物件傳回特定值，例如 Blob 端點 URI 或完整網域名稱。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

### <a name="return-value"></a>傳回值

每種資源類型都會針對 reference 函式傳回不同屬性。 此函式不會傳回單一的預先定義格式。 若要查看資源類型的屬性，請在輸出區段中傳回物件，如範例所示。

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

傳回代表目前資源群組的物件。 

### <a name="examples"></a>範例

下列範本會傳回資源群組的屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

resourceGroup 函式的常見用法是在和資源群組相同的位置中建立資源。 下列範例使用資源群組位置來指派網站的位置。

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

傳回資源的唯一識別碼。 如果資源名稱不確定或未佈建在相同的範本內，請使用此函數。 

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |否 |字串 (GUID 格式) |預設值為目前的訂用帳戶。 需要擷取另一個訂用帳戶中的資源群組時，請指定此值。 |
| resourceGroupName |否 |字串 |預設值為目前資源群組。 需要擷取另一個訂用帳戶中的資源群組時，請指定此值。 |
| resourceType |是 |字串 |資源的類型 (包括資源提供者命名空間)。 |
| resourceName1 |是 |string |資源的名稱。 |
| resourceName2 |否 |string |如果是巢狀資源，則為下一個資源名稱區段。 |

### <a name="examples"></a>範例

下列範例會傳回資源群組中儲存體帳戶的資源識別碼：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceIdOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        }
    }
}
```

下列範例顯示如何擷取不同資源群組網站的資源識別碼，以及不同資源群組的資料庫資源識別碼：

```json
[resourceId('otherResourceGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

通常，在替代資源群組中使用儲存體帳戶或虛擬網路時，需要使用此函數。 儲存體帳戶或虛擬網路可能用於多個資源群組中；因此，您不想要在刪除單一資源群組時刪除它們。 下列範例顯示如何輕鬆地使用外部資源群組中的資源：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="return-value"></a>傳回值

識別碼會以下列格式傳回：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

<a id="subscription" />

## <a name="subscription"></a>訂用帳戶
`subscription()`

傳回目前部署的訂用帳戶詳細資料。 

### <a name="examples"></a>範例

下列範例顯示在 outputs 區段中所呼叫的 subscription 函式。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>傳回值

此函式會傳回下列格式︰

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>後續步驟
* 如需有關 Azure Resource Manager 範本中各區段的說明，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要合併多個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
* 若要依指定的次數重複建立資源類型，請參閱 [在 Azure 資源管理員中建立資源的多個執行個體](resource-group-create-multiple.md)。
* 若要了解如何部署已建立的範本，請參閱[使用 Azure Resource Manager 範本部署應用程式](resource-group-template-deploy.md)。


