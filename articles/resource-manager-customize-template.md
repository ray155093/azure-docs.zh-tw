<properties 
	pageTitle="自訂匯出 Resource Manager 範本 | Microsoft Azure" 
	description="將參數新增至匯出的 Azure Resource Manager 範本，並透過 Azure PowerShell 或 Azure CLI 重新部署。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/10/2016" 
	ms.author="tomfitz"/>

# 自訂匯出 Resource Manager 範本

本主題說明如何修改匯出的範本，以允許傳遞其他的值做為參數。這是根據[匯出 Resource Manager 範本](resource-manager-export-template.md)主題中執行的步驟所建置，但是它不是首先完成該主題的必要項目。您可以在本主題中找到必要的範本和指令碼。

## 檢視匯出的範本

如果您已完成[匯出 Resource Manager 範本](resource-manager-export-template.md)，請開啟您所下載的範本。範本名為 **template.json**。如果您有 Visual Studio 或 Visual Code，您可以使用其中一個來編輯範本。否則，您可以使用任何 json 編輯器或文字編輯器。

如果您尚未完成上一個逐步解說，匯出的範本如下所示。建立名為 **template.json** 的檔案，並將以下內容新增至檔案。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

如果您想要在與虛擬網路 (針對每個部署使用相同位址前置詞和相同子網路前置詞) 相同的區域中，建立相同類型的儲存體帳戶，則上述範例可以良好運作。但是，Resource Manager 可以讓您部署比上述範例更有彈性的範本。例如，在部署期間，您可能想要指定要建立的儲存體帳戶類型，或者要對虛擬網路位址前置詞和子網路前置詞使用什麼值。

## 自訂範本

在本節中，您將參數新增至匯出的範本，以便在將這些資源部署至其他環境時，可以重複使用範本。您也會將一些功能新增至範本，降低部署範本時發生錯誤的可能性。您不用再猜測儲存體帳戶的唯一名稱。而是範本會建立一個唯一的名稱。您會將針對儲存體帳戶類型指定的值限制為有效的選項。

1. 為了能夠傳遞您可能想要在部署期間指定的值，將**參數**區段取代為下列參數定義。請注意 **storageAccount\_accountType** 的允許值。如果您不小心提供了無效的值，會在部署開始之前辨識該錯誤。另外請注意，您只提供儲存體帳戶名稱前置詞，前置詞限制為 11 個字元。藉由將前置詞限制為 11 個字元，您可以確定完整名稱不會超過儲存體帳戶字元數上限。前置詞可讓您將命名慣例套用至儲存體帳戶。您將在下一個步驟中了解如何建立唯一的名稱。

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },
       
2. 範本的**變數**區段目前是空的。使用下列程式碼來取代此區段。**變數**區段可讓您成為範本作者以建立值，簡化範本其他部分的語法。**StorageAccount\_name** 變數會將參數的前置詞串連至唯一的字串，該字串是根據資源群組的識別碼產生。

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. 若要在資源定義中使用參數和變數，請使用下列定義取代**資源**區段。請注意，除了指派至資源屬性的值以外，資源定義中實際的變更極為少數。屬性與匯出的範本的屬性完全相同。您只要將屬性指派至參數值，而不是硬式編碼值。透過 **resourceGroup().location** 運算式，將資源的位置設為使用與資源群組相同的位置。您為儲存體帳戶名稱建立的變數是透過 **variables** 運算式參考。

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## 修正參數檔案

下載的參數檔案不再符合您的範本中的參數。您不需要使用參數檔案，但是它可以簡化重新部署環境。您將使用範本中針對許多參數定義的預設值，因此您的參數檔案只需要兩個值。

以下列程式碼取代 parameters.json 檔案的內容：

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

上述的參數檔案只會針對沒有預設值的參數提供值。當您想要不同於預設值的值時，您可以為其他參數提供值。

## 部署您的範本

您可以使用 Azure PowerShell 或 Azure CLI 來部署自訂範本和參數檔案。如有需要，安裝 [Azure PowerShell](powershell-install-configure.md) 或 [Azure CLI](xplat-cli-install.md)。您可以使用在匯出原始範本時，與範本一起下載的指令碼，或者您可以撰寫自己的指令碼，以部署範本。這兩個選項都會顯示在本主題中。

2. 若要使用自己的指令碼部署，請使用下列其中一項。

     對於 PowerShell，執行：
   
        # login
        Add-AzureRmAccount
        
        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json
        
     對於 Azure CLI，執行：
   
        azure login
        
        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. 如果您已下載匯出的範本和指令碼，請尋找 **deploy.ps1** 檔案 (適用於 PowerShell) 或 **deploy.sh** 檔案 (適用於 Azure CLI)。

     對於 PowerShell，執行：

        Get-Item deploy.ps1 | Unblock-File
        
        .\deploy.ps1

     對於 Azure CLI，執行：
     
        .\deploy.sh

## 後續步驟

- [Resource Manager 範本逐步解說](resource-manager-template-walkthrough.md)建立在您已了解本主題中針對更複雜的解決方案建立範本的基礎上。它可協助您進一步了解可用的資源，以及如何判斷可以提供哪些值。
- 若要查看如何透過 PowerShell 匯出範本，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](powershell-azure-resource-manager.md)。
- 若要查看如何透過 Azure CLI 匯出範本，請參閱[搭配使用 Mac、Linux 和 Windows 適用的 Azure CLI 與 Azure Resource Manager](xplat-cli-azure-resource-manager.md)。 
- 若要了解範本的結構，請參閱[製作 Azure Resource Manager 範本](resource-group-authoring-templates.md)。

<!---HONumber=AcomDC_0511_2016-->