---
title: 角色指派的資源管理員範本 | Microsoft Docs
description: 說明可透過範本部署角色指派的資源管理員結構描述。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz

---
# <a name="role-assignments-template-schema"></a>角色指派範本結構描述
在指定的範圍內為使用者、群組，或服務主體指派角色。

## <a name="resource-format"></a>資源格式
若要建立角色指派，請在範本的資源區段中加入下列結構描述。

    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>值
下表描述您在結構描述中必須設定的值。

| 名稱 | 必要 | 說明 |
| --- | --- | --- |
| 類型 |是 |要建立的資源類型。<br /><br /> 對於資源群組：<br />**Microsoft.Authorization/roleAssignments**<br /><br />對於資源：<br />**{provider-namespace}/{resource-type}/providers/roleAssignments** |
| apiVersion |是 |要用來建立資源的應用程式開發介面 (API) 版本。<br /><br /> 使用 **2015-07-01**。 |
| 名稱 |是 |新角色指派的全域唯一識別碼。 |
| dependsOn |否 |以逗號分隔的資源名稱或資源唯一識別碼陣列。<br /><br />此角色指派所依存的資源集合。 如果指派角色的範圍為資源，且該資源部署在相同的範本中，則將該資源的名稱包含在此元素中，以確保會優先部署資源。 |
| properties |是 |屬性物件，識別角色定義、主體和範圍。 |

### <a name="properties-object"></a>屬性物件
| 名稱 | 必要 | 說明 |
| --- | --- | --- |
| roleDefinitionId |是 |在角色指派中會使用現有之角色定義的識別碼。<br /><br /> 請使用下列格式：<br /> **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId |是 |現有主體的全域唯一識別碼。 此值會對應到目錄中的識別碼，並且可指向使用者、服務主體，或安全性群組。 |
| scope |否 |範圍為此角色指派套用的範圍。<br /><br />對於資源群組，使用：<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**  <br /><br />對於資源，使用：<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |

## <a name="how-to-use-the-role-assignment-resource"></a>如何使用角色指派資源
當您在部署期間需要將使用者、群組或服務主體新增至角色時，請將角色指派新增到您的範本中。 角色指派繼承自更高層級的範圍，因此若您在訂用帳戶層級已經新增主體到角色，您不需要為資源群組或資源重新指派它。

使用角色指派時，您需要提供許多識別碼值。 您可以透過 PowerShell 或 Azure CLI 擷取值。

### <a name="powershell"></a>PowerShell
角色指派的名稱需要全域唯一識別碼。 您可以使用以下方式針對 **name** 產生新的識別碼：

    $name = [System.Guid]::NewGuid().toString()

您可以使用以下方式針對角色定義擷取識別碼：

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

您可以使用下列其中一個命令擷取主體的識別碼。

針對名為 **Auditors**的群組：

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

針對名為 **exampleperson**的使用者：

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

針對名為 **exampleapp**的服務主體：

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Azure CLI
您可以使用以下方式針對角色定義擷取識別碼：

    azure role show Reader --json | jq .[].Id -r

您可以使用下列其中一個命令擷取主體的識別碼。

針對名為 **Auditors**的群組：

    azure ad group show --search Auditors --json | jq .[].objectId -r

針對名為 **exampleperson**的使用者：

    azure ad user show --search exampleperson --json | jq .[].objectId -r

針對名為 **exampleapp**的服務主體：

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>範例
下列範本會接收角色識別碼和使用者、群組或服務主體的識別碼。 在資源群組層級指派角色。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



下一個範本建立儲存體帳戶，並將讀取器角色指派給儲存體帳戶。 兩個群組和讀取器角色的識別碼已包含在範本中以簡化部署。 這些值可以於部署時在指令碼中擷取，並且傳遞為參數。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>快速入門範本
下列範本會示範如何使用角色指派資源：

* [將內建角色指派給資源群組](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
* [將內建角色指派給現有 VM](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
* [將內建角色指派給多個現有 VM](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>後續步驟
* 如需範本結構的相關資訊，請參閱 [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
* 如需角色型存取控制的詳細資訊，請參閱 [Azure Active Directory 角色型存取控制](active-directory/role-based-access-control-configure.md)。

<!--HONumber=Oct16_HO2-->


