<properties
   pageTitle="透過 Resource Manager 使用連結範本 | Microsoft Azure"
   description="描述如何在「Azure 資源管理員」範本中使用連結的範本，以建立模組化範本方案。示範如何傳遞參數值、指定參數檔案，以及動態建立 URL。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/08/2016"
   ms.author="tomfitz"/>

# 透過 Azure Resource Manager 使用連結的範本

您可以從某個「Azure 資源管理員」範本連結到另一個範本，這樣可讓您將部署分解成一組具有目標與特定目的的範本。就像將應用程式分解數個程式碼類別，分解有利於測試、重複使用和可讀性。

您可以從主要範本傳遞參數到連結的範本，且那些參數可以直接對應到由發出呼叫之範本所公開的參數或變數。連結的範本也可以將輸出變數傳遞回來源範本，讓範本之間可進行雙向資料交換。

## 連結至範本

您可以透過在主要範本中新增指向連結的範本之部署資源，以在兩個範本之間建立連結。將 **templateLink** 屬性設為連結的範本之 URI。您可以透過在範本中直接指定值，或透過連結到參數檔案，以為連結的範本提供參數值。以下範例會使用 **parameters** 屬性直接指定參數值。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

資源管理員服務必須能夠存取連結的範本，這表示您無法將本機檔案，或是將只能在本機網路上取得的檔案，指定為連結的範本。您可以只提供 URI 值，其中包含 **http** 或 **https**。有一個選擇是將連結的範本放在儲存體帳戶中，並將 URI 用於該項目，如底下所示。

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

雖然連結的範本必須可從外部存取，但它不必供大眾存取。您可以將您的範本新增到私人儲存體帳戶 (只有儲存體帳戶擁有者可以存取該帳戶)，然後建立共用存取簽章 (SAS) Token 來允許在部署期間存取。您會將該 SAS Token 加入連結範本的 URI。如需在儲存體帳戶中設定範本並產生 SAS Token 的步驟，請參閱[使用 Resource Manager 範本與 Azure PowerShell 部署資源](resource-group-template-deploy.md)或 [Deploy resources with Resource Manager templates and Azure CLI (使用 Resource Manager 範本和 Azure CLI 部署資源)](resource-group-template-deploy-cli.md)。

以下範例顯示連結到其他範本的父範本。巢狀範本是透過以參數傳遞的 SAS Token 來存取。

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

即使該 Token 是以安全字串來傳遞，連結範本的 URI (包括 SAS Token) 還是會記錄在該資源群組的部署作業中。為了限制公開的程度，請為該 Token 設定到期日。

## 連結到參數檔案

下一個範例會使用 **parametersLink** 屬性連接至參數檔案。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

連結參數檔案的 URI 值不能是本機檔案，而且必須包含 **http** 或 **https**。當然，參數檔案也可以限制為透過 SAS Token 存取。

## 使用變數來連結範本

先前的範例示範了範本連結的硬式編碼 URL 值。這種方法可能適用於簡單的範本，但不適合一組大型的模組化範本。不過，您可以建立一個儲存主要範本之基底 URL 的靜態變數，然後再從該基底 URL 連結動態建立連結的範本之 URL。這個方法的好處是您可以輕鬆地移動範本或建立分支範本，因為您只需要變更主要範本中的靜態變數。主要範本會於整個分解的範本傳遞正確的 URI。

下列範例示範如何使用基底 URL 來為連結的範本 (**sharedTemplateUrl** 和 **vmTemplate**) 建立兩個 URL。

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

您也可以使用 [deployment()](resource-group-template-functions.md#deployment) 取得目前範本的基底 URL，用來取得相同位置中其他範本的 URL。如果您的範本位置變更 (可能是版本不同所造成)，或您想要避免在範本檔案中使用硬式編碼 URL，這十分實用。

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## 完整範例

以下範例範本顯示連結範本的簡化設定，以說明本文章中的幾個概念。範例會假設已經將範本新增到儲存體帳戶中的同一個容器，且已經關閉公開存取。連結的範本會在 **outputs** 區段中將一個值傳遞給主範本。

**parent.json** 檔案的組成：

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('nestedTemplate').outputs.result]"
        }
      }
    }

**helloworld.json** 檔案的組成：

    {
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {},
	  "variables": {},
	  "resources": [],
	  "outputs": {
		"result": {
			"value": "Hello World",
			"type" : "string"
		}
	  }
    }
    
在 PowerShell 中，您會取得容器的 Token 並使用下列方式部署範本：

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

在 Azure CLI 中，您會取得容器的 Token 並使用下列指令碼部署範本。目前，您在使用包含 SAS Token 的範本 URI 時必須提供部署名稱。

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

系統會提示您以參數提供 SAS Token。您需要在 Token 前面加上 **?**。

## 後續步驟
- 若要了解如何定義您資源的部署順序，請參閱[定義 Azure Resource Manager 範本中的相依性](resource-group-define-dependencies.md)
- 若要了解如何定義一個資源，但建立它的多個執行個體，請參閱[在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)

<!---HONumber=AcomDC_0615_2016-->