---
title: "將資源部署到 Azure | Microsoft Docs"
description: "使用 Azure PowerShell 或 Azure CLI 將資源部署到 Azure。 資源會定義在 Resource Manager 範本中。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 3b3162c335be43c9bf2a3d1c14b86cdd9b5d46b9
ms.lasthandoff: 04/27/2017


---
# <a name="deploy-resources-to-azure"></a>將資源部署到 Azure

本主題說明如何將資源部署到您的 Azure 訂用帳戶。 您可以使用 Azure PowerShell 或 Azure CLI 來部署定義解決方案基礎結構的 Resource Manager 範本。

如需 Resource Manager 的概念簡介，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。

## <a name="steps-for-deployment"></a>部署步驟

本主題假設您會在本主題中部署此[範例儲存體範本](#example-storage-template)。 您可以使用不同的範本，但所傳遞的參數會與本主題中所示的不同。

建立範本之後，部署範本的一般步驟包括：

1. 登入您的帳戶
2. 選取要使用的訂用帳戶 (只有當您有多個訂用帳戶，且您想要使用非預設訂用帳戶時，才需要執行此步驟)
3. 建立資源群組
4. 部署範本
5. 檢查部署狀態

下列各節說明如何使用 [PowerShell](#powershell) 或 [Azure CLI](#azure-cli) 來執行這些步驟。

## <a name="powershell"></a>PowerShell

1. 若要安裝 Azure PowerShell，請參閱[開始使用 Azure PowerShell Cmdlet](/powershell/azure/overview)。

2. 若要快速開始部署，請使用下列 Cmdlet：

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  只有當您想要使用預設訂用帳戶以外的訂用帳戶時，才需要使用 `Set-AzureRmContext` Cmdlet。 若要查看您的所有訂用帳戶及其 ID，請使用：

  ```powershell
  Get-AzureRmSubscription
  ```

3. 部署需要幾分鐘的時間才能完成。 部署完成時，您會看到類似以下的訊息：

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. 若要查看您的資源群組和儲存體帳戶是否已部署到您的訂用帳戶，請使用：

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. 部署範本時，您可以指定範本參數作為 PowerShell 參數。 先前的範例並未包含任何範本參數，因此會使用範本中的預設值。 若要部署另一個儲存體帳戶，並為儲存體名稱前置詞和儲存體帳戶 SKU 提供參數值，請使用：

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  現在，您的資源群組中有兩個儲存體帳戶。 

## <a name="azure-cli"></a>Azure CLI

1. 若要安裝 Azure CLI，請參閱[安裝 Azure CLI 2.0 (英文)](/cli/azure/install-az-cli2)。

2. 若要快速開始部署，請使用下列命令：

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  只有當您想要使用預設訂用帳戶以外的訂用帳戶時，才需要使用 `az account set` 命令。 若要查看您的所有訂用帳戶及其 ID，請使用：

  ```azurecli
  az account list
  ```

3. 部署需要幾分鐘的時間才能完成。 部署完成時，您會看到類似以下的訊息：

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. 若要查看您的資源群組和儲存體帳戶是否已部署到您的訂用帳戶，請使用：

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. 部署範本時，您可以指定範本參數作為 PowerShell 參數。 先前的範例並未包含任何範本參數，因此會使用範本中的預設值。 若要部署另一個儲存體帳戶，並為儲存體名稱前置詞和儲存體帳戶 SKU 提供參數值，請使用：

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  現在，您的資源群組中有兩個儲存體帳戶。 

## <a name="example-storage-template"></a>範例儲存體範本

請使用下列範例範本將儲存體帳戶部署到您的訂用帳戶：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>後續步驟

* 如需有關使用 PowerShell 來部署範本的詳細資訊，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](/azure/azure-resource-manager/resource-group-template-deploy)。
* 如需有關使用 Azure CLI 來部署範本的詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 部署資源](/azure/azure-resource-manager/resource-group-template-deploy-cli)。




