---
title: "使用 PowerShell 與範本部署資源 | Microsoft Docs"
description: "使用 Azure Resource Manager 和 Azure PowerShell，將資源部署至 Azure。 資源會定義在 Resource Manager 範本中。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f5119fef1fb0a316b4109ccbc4433f8c9a18d128
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>使用 Resource Manager 範本與 Azure PowerShell 來部署資源

本主題說明如何使用 Azure PowerShell 與 Resource Manager 範本，將您的資源部署至 Azure。 您的範本可以是本機檔案，或者是透過 URI 提供使用的外部檔案。

你可以從[建立第一個 Azure Resource Manager 範本](resource-manager-create-first-template.md#final-template)一文中取得這些範例中使用的範本 (storage.json)。 若要搭配這些範例使用範本，請建立 JSON 檔案並加入複製的內容。

## <a name="deploy-local-template"></a>部署本機範本
若要快速地開始進行部署，請使用下列 Cmdlet 來部署具有內嵌參數的本機範本。 

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

部署需要幾分鐘的時間才能完成。 完成後，您會看到包含結果的訊息：

```powershell
ProvisioningState       : Succeeded
```

上述範例已在您的預設訂用帳戶中建立資源群組。 若要使用不同的訂用帳戶，請在登入後加入 [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) Cmdlet。

## <a name="deploy-external-template"></a>部署外部範本

若要部署外部範本，請使用 **TemplateUri** 參數。 範本可位於任何可公開存取的 URI (例如儲存體帳戶中的檔案)。

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -storageNamePrefix contoso -storageSKU Standard_GRS
```

範本可設定為需要共用存取簽章 (SAS) 權杖才能存取，以藉此提供保護。 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-powershell-sas-token.md)。

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

若要傳遞本機參數檔案，請使用 **TemplateParameterFile** 參數︰

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

若要傳遞外部參數檔案，請使用 **TemplateParameterUri** 參數︰

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.parameters.json
```

您可以在相同的部署作業中使用內嵌參數和本機參數檔案。 例如，您可以在部署期間指定本機參數檔案中的某些值，並新增其他內嵌值。 如果您同時為本機檔案中和內嵌的參數提供值，內嵌值的優先順序較高。

不過，當使用外部參數檔案時，您無法傳遞內嵌或本機檔案的其他值。 當您指定 **TemplateParameterUri** 參數中的參數檔案時，所有內嵌參數都會被忽略。 請提供外部檔案中的所有參數值。 如果您的範本包含不能包含在參數檔案中的機密值，請將該值新增至金鑰保存庫，或以動態方式提供所有內嵌參數值。

如果您範本所含的參數名稱與 PowerShell 命令中的其中一個參數一樣，PowerShell 會以加上後置 **FromTemplate** 的方式呈現您範本中的參數。 例如，範本中名為 **ResourceGroupName** 的參數會與 [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。 一般而言，在為參數命名時，請勿使用與部署作業所用參數相同的名稱，以避免發生這種混淆的情形。

## <a name="test-a-deployment"></a>測試部署

若要測試您的範本和參數值而不實際部署任何資源，請使用 [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment)。 使用本機或遠端檔案的選項均相同。

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

## <a name="log-deployment-content-for-debugging"></a>記錄部署內容以用於偵錯

與部署作業相關的資訊會自動記錄於活動記錄中。 不過，若要記錄部署的其他相關資訊以協助針對任何部署錯誤進行疑難排解，請使用 **DeploymentDebugLogLevel** 參數。 您可以指定在記錄部署作業時，一併記錄要求內容及/或回應內容。
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All `
  -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

如需檢視記錄的詳細資訊，請參閱[檢視活動記錄以稽核對資源的動作](resource-group-audit.md)。

## <a name="export-resource-manager-template"></a>匯出 Resource Manager 範本
對於現有的資源群組 (透過 PowerShell 或 入口網站等其他方法部署)，您可以檢視資源群組的資源管理員範本。 匯出此範本有兩個優點︰

1. 因為所有基礎結構都已定義於範本中，所以您可以輕鬆地自動進行解決方案的未來部署。
2. 您可以查看代表您的解決方案的 JavaScript 物件標記法 (JSON)，藉此熟悉範本語法。

若要檢視資源群組的範本，請執行 [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) Cmdlet。

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleResourceGroup
```

如需詳細資訊，請參閱[從現有資源匯出 Azure Resource Manager 範本](resource-manager-export-template.md)。


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

若要使用完整模式，請使用 `Mode` 參數︰

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```


## <a name="next-steps"></a>後續步驟
* 如需部署範本的完整範例指令碼，請參閱 [Resource Manager 範本部署指令碼](resource-manager-samples-powershell-deploy.md)。
* 若要在範本中定義參數，請參閱 [編寫範本](resource-group-authoring-templates.md#parameters)。
* 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
* 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-powershell-sas-token.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。


