---
title: "金鑰保存庫密碼與 Resource Manager 範本 | Microsoft Docs"
description: "示範如何在部署期間從金鑰保存庫中傳遞密碼做為參數。"
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0bf872a44b8ed7cae53d2659aa7be878902130e9
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017


---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>在部署期間使用 Key Vault 以傳送安全的參數值

當您需要在部署期間，傳送安全值 (例如密碼) 做為參數時，您可以從 [Azure Key Vault](../key-vault/key-vault-whatis.md) 擷取值。 您可以藉由參考金鑰保存庫和參數檔案中的密碼來擷取值。 您只參考其金鑰保存庫識別碼，因此該值絕不會公開。 您不需要在每次部署資源時手動輸入密碼的值。 金鑰保存庫可以存在於與您部署的資源群組的不同訂用帳戶中。 當參考金鑰保存庫時，您納入訂用帳戶識別碼。

本主題說明如何建立金鑰保存庫和密碼、設定 Resource Manager 範本密碼的存取權，以及傳送密碼做為參數。 如果您已經具備金鑰保存庫和密碼，但需要檢查範本和使用者存取權，請移至[啟用密碼存取權](#enable-access-to-the-secret)一節。 如果您已具備金鑰保存庫和密碼，並確定它已針對範本和使用者存取權進行設定，請移至[使用靜態識別碼參考密碼](#reference-a-secret-with-static-id)一節。 

## <a name="deploy-a-key-vault-and-secret"></a>部署金鑰保存庫和密碼

您可以透過 Resource Manager 範本部署金鑰保存庫和密碼。 如需範例，請參閱[金鑰保存庫範本](resource-manager-template-keyvault.md)和[金鑰保存庫密碼範本](resource-manager-template-keyvault-secret.md)。 在建立金鑰保存庫時，請將 **enabledForTemplateDeployment** 屬性設定為 **true**，以便可從其他 Resource Manager 範本加以參考。 

或者，您可以透過 Azure 入口網站建立金鑰保存庫和密碼。 

1. 選取 [新增]  ->  [安全性 + 身分識別]  ->  [Key Vault]。

   ![建立新的金鑰保存庫](./media/resource-manager-keyvault-parameter/new-key-vault.png)

2. 提供金鑰保存庫的值。 您目前可以忽略**存取原則**和**進階存取原則**設定。 本章節涵蓋這些設定。 選取 [建立] 。

   ![設定金鑰保存庫](./media/resource-manager-keyvault-parameter/create-key-vault.png)

3. 現在您有金鑰保存庫。 選取該金鑰保存庫。

4. 在 [金鑰保存庫] 刀鋒視窗中，選取 [密碼]。

   ![選取密碼](./media/resource-manager-keyvault-parameter/select-secret.png)

5. 選取 [新增] 。

   ![選取 [新增]](./media/resource-manager-keyvault-parameter/add-secret.png)

6. 針對上傳選項選取 [手動]。 提供密碼的名稱和值。 選取 [建立] 。

   ![提供密碼](./media/resource-manager-keyvault-parameter/provide-secret.png)

您已建立您的金鑰保存庫與密碼。

## <a name="enable-access-to-the-secret"></a>啟用密碼的存取權

不論您使用新的金鑰保存庫或現有的金鑰保存庫，請確定使用者部署的範本可以存取密碼。 部署範本以參考密碼的使用者必須具有金鑰保存庫的 `Microsoft.KeyVault/vaults/deploy/action` 權限。 [擁有者](../active-directory/role-based-access-built-in-roles.md#owner)和[參與者](../active-directory/role-based-access-built-in-roles.md#contributor)角色皆可授與此權限。 您也可以建立會授與此權限的[自訂角色](../active-directory/role-based-access-control-custom-roles.md)，並將使用者新增至該角色。 此外，您必須授與 Resource Manager 在部署期間存取金鑰保存庫的能力。

您可以透過入口網站檢查或執行這些步驟。

1. 選取 [存取控制 (IAM)]。

   ![選取存取控制](./media/resource-manager-keyvault-parameter/select-access-control.png)

2. 如果您想要用於部署範本的帳戶還不是「擁有者」或「參與者」(或新增至授與 `Microsoft.KeyVault/vaults/deploy/action` 權限的自訂角色)，請選取 [新增]

   ![新增使用者](./media/resource-manager-keyvault-parameter/add-user.png)

3. 選取「參與者」或「擁有者」角色，並搜尋要指派給該角色的身分識別。 選取 [確定]以完成將身分識別新增至角色。

   ![新增使用者](./media/resource-manager-keyvault-parameter/search-user.png)

4. 若要在部署期間從範本啟用存取權，請選取 [進階存取控制]。 選取 [為範本部署啟用對 Azure Resource Manager 的存取] 選項。

   ![啟用範本存取權](./media/resource-manager-keyvault-parameter/select-template-access.png)

## <a name="reference-a-secret-with-static-id"></a>使用靜態識別碼參考密碼

您會從將值傳遞至範本的**參數檔案中 (非範本)** 參考密碼。 您可以藉由傳遞金鑰保存庫的資源識別碼和密碼的名稱來參考密碼。 在下列範例中，金鑰保存庫密碼必須已經存在，而且您要針對其資源識別碼提供靜態值。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "sqlsvrAdminLoginPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          },
          "secretName": "adminPassword"
        }
      },
      "sqlsvrAdminLogin": {
        "value": "exampleadmin"
      }
    }
}
```

在範本中，接受密碼的參數應該是 **securestring**。 下列範例顯示範本的相關區段，該範本會部署需要系統管理員密碼的 SQL Server。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlsvrAdminLogin": {
            "type": "string",
            "minLength": 4
        },
        "sqlsvrAdminLoginPassword": {
            "type": "securestring"
        },
        ...
    },
    "resources": [
        {
          "name": "[variables('sqlsvrName')]",
          "type": "Microsoft.Sql/servers",
          "location": "[resourceGroup().location]",
          "apiVersion": "2014-04-01-preview",
          "properties": {
              "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
              "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
          },
          ...
        }
    ],
    "variables": {
        "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
    },
    "outputs": { }
}
```



## <a name="reference-a-secret-with-dynamic-id"></a>使用動態識別碼參考密碼

上一節已說明如何傳遞金鑰保存庫密碼的靜態資源識別碼。 不過，在某些情況下，您需要參考會隨目前的部署而變化的金鑰保存庫密碼。 在該情況下，您將無法在參數檔中硬式編碼資源識別碼。 不幸的是，因為參數檔中不允許使用範本運算式，因此您無法在參數檔中以動態方式產生資源識別碼。

若要以動態方式產生金鑰保存庫密碼的資源識別碼，您必須將需要密碼的資源移動到巢狀範本。 在主要範本中，您必須新增巢狀範本，並傳入包含動態產生的資源識別碼的參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>後續步驟
* 如需金鑰保存庫的一般資訊，請參閱[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)。
* 如需參考金鑰密碼的完整範例，請參閱 [金鑰保存庫範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。


