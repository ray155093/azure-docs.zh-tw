---
title: "使用 Azure CLI 與範本部署資源 | Microsoft Docs"
description: "使用 Azure Resource Manager 和 Azure CLI，將資源部署至 Azure。 資源會定義在 Resource Manager 範本中。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 4f1d5f4cc48470f8906edb28628006dd1996bd3a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/01/2017

---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>使用 Resource Manager 範本與 Azure CLI 部署資源

本主題說明如何使用 Azure CLI 2.0 與 Resource Manager 範本，將您的資源部署至 Azure。 如果您不熟悉部署和管理 Azure 解決方案的相關概念，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。  

您部署的 Resource Manager 範本可以是電腦上的本機檔案，或是位於類似 GitHub 等存放庫中的外部檔案。 您會在本文中部署的範本，可在[範例範本](#sample-template)一節中取得，也會以 [GitHub 中儲存體帳戶範本 (英文)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json) 的形式提供。

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

如果您沒有安裝 Azure CLI，可以使用 [Cloud Shell](#deploy-template-from-cloud-shell)。

## <a name="deploy-local-template"></a>部署本機範本

將資源部署至 Azure 時，您應該：

1. 登入您的 Azure 帳戶
2. 建立資源群組，作為已部署資源的容器。 資源群組的名稱只能包含英數字元、句點 (.)、底線、連字號及括弧。 最多可有 90 個字元。 不能以句點結束。
3. 將範本部署至資源群組，範本中定義要建立的資源

範本可以包含讓您自訂部署的參數。 例如，您可以提供針對特定環境 (例如開發、測試和生產) 量身訂做的值。 範例範本會定義儲存體帳戶 SKU 的參數。 

下列範例會建立資源群組，並從您的本機電腦部署範本：

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

部署需要幾分鐘的時間才能完成。 完成後，您會看到包含結果的訊息：

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>部署外部範本

您可能希望將 Resource Manager 範本儲存在外部位置，而不是儲存在您的本機電腦。 您可以將範本儲存在原始檔控制存放庫 (例如 GitHub) 中。 或者，您可以將它們儲存在 Azure 儲存體帳戶中，以在組織內共用存取。

若要部署外部範本，請使用 **template-uri** 參數。 在範例中使用 URI 以部署來自 GitHub 的範例範本。
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

上述範例針對範本需要可公開存取 URI，這適用於大部分的案例，因為您的範本不應該包含機密資料。 如果您需要指定機密資料 (例如系統管理員密碼)，請將該值以安全參數傳遞。 不過，如果不希望將範本公開存取，您可以將它儲存在私人儲存體容器中加以保護。 如需部署需要共用存取簽章 (SAS) 權杖之範本的相關資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-cli-sas-token.md)。

## <a name="deploy-template-from-cloud-shell"></a>從 Cloud Shell 部署範本

您可以使用 [Cloud Shell](../cloud-shell/overview.md) 執行 Azure CLI 命令，以便部署範本。 不過，您必須先將範本載入 Cloud Shell 的檔案共用中。 如果您尚未使用 Cloud Shell，請參閱 [Azure Cloud Shell 概觀](../cloud-shell/overview.md)以取得如何設定的相關資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。   

2. 選取您的 Cloud Shell 資源群組。 名稱模式為 `cloud-shell-storage-<region>`。

   ![選取資源群組](./media/resource-group-template-deploy-cli/select-cs-resource-group.png)

3. 選取 Cloud Shell 的儲存體帳戶。

   ![選取儲存體帳戶](./media/resource-group-template-deploy-cli/select-storage.png)

4. 選取 [檔案]。

   ![選取檔案](./media/resource-group-template-deploy-cli/select-files.png)

5. 選取 Cloud Shell 的檔案共用。 名稱模式為 `cs-<user>-<domain>-com-<uniqueGuid>`。

   ![選取檔案共用](./media/resource-group-template-deploy-cli/select-file-share.png)

6. 選取 [新增目錄]。

   ![新增目錄](./media/resource-group-template-deploy-cli/select-add-directory.png)

7. 將它命名為 **templates**，然後選取 [確定]。

   ![命名目錄](./media/resource-group-template-deploy-cli/name-templates.png)

8. 選取您的新目錄。

   ![選取目錄](./media/resource-group-template-deploy-cli/select-templates.png)

9. 選取 [上傳] 。

   ![選取上傳](./media/resource-group-template-deploy-cli/select-upload.png)

10. 尋找並上傳您的範本。

   ![上傳檔案](./media/resource-group-template-deploy-cli/upload-files.png)

11. 開啟提示字元。

   ![開啟 Cloud Shell](./media/resource-group-template-deploy-cli/start-cloud-shell.png)

12. 在 Cloud Shell 中輸入下列命令︰

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageAccountType=Standard_GRS
   ```

## <a name="parameter-files"></a>參數檔案

相對於在您的指令碼中將參數做為內嵌值傳遞，使用包含該參數值的 JSON 檔案可能較為容易。 參數檔必須是下列格式︰

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

請注意，參數區段包含符合於範本中所定義之參數 (storageAccountType) 的參數名稱。 參數檔案包含參數的值。 這個值會在部署期間自動傳遞至範本。 您可以針對不同的部署案例建立多個參數檔案，然後傳遞適當的參數檔案。 

複製上述的範例，然後將它另存為名叫 `storage.parameters.json` 的檔案。

若要傳遞本機參數檔案，請使用 `@` 來指定名為 storage.parameters.json 的本機檔案。

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>測試範本部署

若要測試您的範本和參數值而不實際部署任何資源，請使用 [az group deployment validate](/cli/azure/group/deployment#validate)。 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

如果沒有偵測到任何錯誤，命令會傳回有關測試部署的資訊。 請特別注意到 **error** 值為 null。

```azurecli
{
  "error": null,
  "properties": {
      ...
```

如果偵測到錯誤，命令會傳回錯誤訊息。 例如，嘗試針對儲存體帳戶 SKU 傳遞不正確的值，則會傳回下列錯誤：

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

如果您的範本有語法錯誤，命令會傳回錯誤，指出無法剖析範本。 訊息會指出剖析錯誤的行號和位置。

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

若要使用完整模式，請使用 `mode` 參數︰

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>範例範本

下列範本適用於本主題中的範例。 請複製它並另存為名叫 storage.json 的檔案。 若要了解如何建立此範本，請參閱[建立第一個 Azure Resource Manager 範本](resource-manager-create-first-template.md)。  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>後續步驟
* 本主題中的範例會將資源部署到您預設訂用帳戶中的資源群組。 若要使用不同的訂用帳戶，請參閱[管理多個 Azure 訂用帳戶](/cli/azure/manage-azure-subscriptions-azure-cli)。
* 如需部署範本的完整範例指令碼，請參閱 [Resource Manager 範本部署指令碼](resource-manager-samples-cli-deploy.md)。
* 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](resource-group-authoring-templates.md)。
* 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
* 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-cli-sas-token.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。

