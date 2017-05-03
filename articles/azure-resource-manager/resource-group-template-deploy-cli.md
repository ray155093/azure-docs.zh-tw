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
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c889a609b8d49474216fe1dcfba69a881edb4133
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>使用 Resource Manager 範本與 Azure CLI 部署資源

本主題說明如何使用 [Azure CLI 2.0](/cli/azure/install-az-cli2) 與 Resource Manager 範本，將您的資源部署至 Azure。  您的範本可以是本機檔案，或者是透過 URI 提供使用的外部檔案。

你可以從[建立第一個 Azure Resource Manager 範本](resource-manager-create-first-template.md#final-template)一文中取得這些範例中使用的範本 (storage.json)。 若要搭配這些範例使用範本，請建立 JSON 檔案並加入複製的內容。

## <a name="deploy-local-template"></a>部署本機範本

若要快速地開始進行部署，請使用下列命令來部署具有內嵌參數的本機範本︰

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

部署需要幾分鐘的時間才能完成。 完成後，您會看到包含結果的訊息：

```azurecli
"provisioningState": "Succeeded",
```

上述範例已在您的預設訂用帳戶中建立資源群組。 若要使用不同的訂用帳戶，請在登入後加入 [az account set](/cli/azure/account#set) 命令。

## <a name="deploy-external-template"></a>部署外部範本

若要部署外部範本，請使用 **template-uri** 參數。 範本可位於任何可公開存取的 URI (例如儲存體帳戶中的檔案)。
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

範本可設定為需要共用存取簽章 (SAS) 權杖才能存取，以藉此提供保護。 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-cli-sas-token.md)。

## <a name="parameter-files"></a>參數檔案

上述範例示範如何將參數做為內嵌值傳遞。 您可以在一個檔案中指定參數值，並在部署期間傳遞該檔案。 

參數檔必須是下列格式︰

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

若要傳遞本機參數檔案，請使用︰

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-deployment"></a>測試部署

若要測試您的範本和參數值而不實際部署任何資源，請使用 [az group deployment validate](/cli/azure/group/deployment#validate)。 使用本機或遠端檔案的選項均相同。

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="debug"></a>偵錯

若要查看失敗部署之作業的相關資訊，請使用︰
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。


## <a name="export-resource-manager-template"></a>匯出 Resource Manager 範本
對於現有的資源群組 (透過 Azure CLI 或入口網站等其他方法部署)，您可以檢視資源群組的 Resource Manager 範本。 匯出此範本有兩個優點︰

1. 因為所有基礎結構都已定義於範本中，所以您可以輕鬆地自動進行解決方案的未來部署。
2. 您可以查看代表您的解決方案的 JavaScript 物件標記法 (JSON)，藉此熟悉範本語法。

若要檢視資源群組的範本，請執行 [az group export](/cli/azure/group#export) 命令。

```azurecli
az group export --name ExampleGroup
```

如需詳細資訊，請參閱[從現有資源匯出 Azure Resource Manager 範本](resource-manager-export-template.md)。


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

若要使用完整模式，請使用 `mode` 參數︰

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## <a name="next-steps"></a>後續步驟
* 如需部署範本的完整範例指令碼，請參閱 [Resource Manager 範本部署指令碼](resource-manager-samples-cli-deploy.md)。
* 若要在範本中定義參數，請參閱 [編寫範本](resource-group-authoring-templates.md#parameters)。
* 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
* 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-cli-sas-token.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。

