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
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5b8b293b5b37365088a3df55581be7b7bf76691c
ms.openlocfilehash: 7bc3421e00215ca4629ea11811c98e581377b24a


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>使用 Resource Manager 範本與 Azure PowerShell 來部署資源
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [入口網站](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

本主題說明如何使用 Azure PowerShell 與 Resource Manager 範本，將您的資源部署至 Azure。 您的範本可以是本機檔案，或者是透過 URI 提供使用的外部檔案。 當您的範本位於儲存體帳戶中時，您可以限制範本的存取權，並在部署期間提供共用存取簽章 (SAS) Token

> [!TIP]
> 如需部署期間偵錯錯誤的說明，請參閱︰
> 
> * [檢視部署作業](resource-manager-deployment-operations.md)，以了解有關取得可協助您針對錯誤進行疑難排解的資訊
> * [針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解](resource-manager-common-deployment-errors.md) ，以了解如何解決常見的部署錯誤
> 
> 

## <a name="quick-steps-to-deployment"></a>部署的快速步驟
若要快速開始部署，請使用下列命令：

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json -TemplateParameterFile c:\MyTemplates\example.params.json
```

這些命令會建立資源群組，並為該資源群組部署範本。 範本檔案和參數檔案皆為本機檔案。 如果您認為沒問題，便已具備部署資源所需的一切項目。 不過，您還有其他選項可用來指定要部署的資源。 本文其他部分會說明部署期間提供給您使用的所有選項。 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>部署
1. 登入您的 Azure 帳戶。

   ```powershell
   Add-AzureRmAccount
   ```

    系統會傳回您帳戶的摘要。
    
   ```powershell
   Environment           : AzureCloud
   Account               : someone@example.com
   TenantId              : {guid}
   SubscriptionId        : {guid}
   SubscriptionName      : Example Subscription
   CurrentStorageAccount :
   ```

2. 如果您有多個訂用帳戶，請使用 **Set-AzureRmContext** 命令提供您想要用於部署的訂用帳戶識別碼。 
   
   ```powershell
   Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
   ```
3. 在部署範本時，您必須指定將會包含已部署之資源的資源群組。 如果您想要部署到現有的資源群組，則您可以略過此步驟並使用該資源群組。 
   
     若要建立新的資源群組，請提供資源群組的名稱和位置。 您需要提供資源群組的位置，因為資源群組會儲存資源的相關中繼資料。 為了符合法規，您可能會想要指定中繼資料的儲存位置。 一般情況下，我們建議您指定大部分資源所在的位置。 使用相同位置可簡化範本。
   
   ```powershell
   New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   ```
   
     隨即傳回新資源群組的摘要。
4. 在執行部署之前，您可以先驗證部署設定。 **Test-AzureRmResourceGroupDeployment** Cmdlet 可讓您在實際建立資源之前發現問題。 下列範例示範如何驗證部署。
   
   ```powershell
   Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>
   ```
5. 若要將資源部署至資源群組，請執行 **New-AzureRmResourceGroupDeployment** 命令，並提供必要的參數。 參數會包含您部署的名稱、資源群組的名稱、範本的路徑或 URL，以及您案例所需的任何其他參數。 如未指定 **Mode** 參數，即會使用預設值 **Incremental**。 若要執行完整部署，請將 **Mode** 設為 **Complete**。 使用完整模式時請務必謹慎，因為您可能會不小心刪除不在範本中的資源。
   
     若要部署本機範本，請使用 **TemplateFile** 參數︰
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json
   ```
   
     若要部署外部範本，請使用 **TemplateUri** 參數︰
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json
   ```
   
     上述兩個範例未包含參數值。 您可在[參數](#parameters)一節了解用來傳遞參數值的選項。 現在，系統會提示您使用下列語法提供參數值︰

   ```powershell  
   cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
   Supply values for the following parameters:
   (Type !? for Help.)
   firstParameter: <type here>
   ```
     
     部署資源之後，您會看到部署的摘要。 此摘要包括 **ProvisioningState**，它會指出部署是否成功。

   ```powershell   
   DeploymentName    : ExampleDeployment
   ResourceGroupName : ExampleResourceGroup
   ProvisioningState : Succeeded
   Timestamp         : 4/14/2015 7:00:27 PM
   Mode              : Incremental
   ```
      
6. 如果您想要記錄部署的其他相關資訊，以助於針對任何部署錯誤進行疑難排解，請使用 **DeploymentDebugLogLevel** 參數。 您可以指定在記錄部署作業時，一併記錄要求內容及/或回應內容。
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
   ```
   
     如需有關使用此偵錯內容針對部署問題進行疑難排解的詳細資訊，請參閱[檢視部署作業](resource-manager-deployment-operations.md)。

## <a name="deploy-private-template-with-sas-token"></a>使用 SAS 權杖部署私人範本
您可以將範本加入儲存體帳戶，並在部署期間使用 SAS Token 連結它們。

> [!IMPORTANT]
> 遵循下列步驟，則僅有帳戶擁有者可以存取包含範本的 Blob。 不過，當您建立 Blob 的 SAS Token 時，具備該 URI 的任何人都可以存取該 Blob。 如果另一位使用者攔截了 URI，該使用者也能存取範本。 使用 SAS Token 是限制存取您的範本的好方法，但您不應該將機密資料 (如密碼) 直接包含在範本中。
> 
> 

### <a name="add-private-template-to-storage-account"></a>將私人範本新增至儲存體帳戶
下列步驟設定範本的儲存體帳戶：

1. 建立資源群組。
   
   ```powershell
   New-AzureRmResourceGroup -Name ManageGroup -Location "West US"
   ```
2. 建立儲存體帳戶。 儲存體帳戶的名稱在整個 Azure 中必須是唯一的，因此請為帳戶提供您自己的名稱。
   
   ```powershell
   New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"
   ```
3. 設定目前的儲存體帳戶。
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
4. 建立容器。 權限設為 [關閉]  表示容器僅限擁有者存取。
   
   ```powershell
   New-AzureStorageContainer -Name templates -Permission Off
   ```
5. 將您的範本新增至容器。
   
   ```powershell
   Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>在部署期間提供 SAS Token
若要在儲存體帳戶中部署私人範本，請擷取 SAS Token 並將它包含在範本的 URI 中。

1. 如果您有變更目前的儲存體帳戶，請將目前的儲存體帳戶設為包含範本的帳戶。
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
2. 建立具有讀取權限與到期時間的 SAS Token，以限制存取權限。 擷取包括 SAS Token 的完整範本 URI。
   
   ```powershell
   $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
   ```
3. 提供包含 SAS Token 的 URI 來部署範本。
   
   ```powershell
   New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri
   ```

如需使用包含已連結範本的 SAS Token 範例，請參閱 [透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。

## <a name="parameters"></a>parameters

您有下列選項可以用來提供參數值： 
   
- 內嵌參數。 在 Cmdlet 中包含個別參數名稱 (例如，**-myParameterName**。)

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"
   ```
- 參數物件。 包含 **-TemplateParameterObject** 參數。

   ```powershell   
   $parameters = @{"<ParameterName>"="<Parameter Value>"}
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters
   ```
- 本機參數檔案。 包含 **-TemplateParameterFile** 參數。

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile c:\MyTemplates\example.params.json
   ```
- 外部參數檔案。 包含 **-TemplateParameterUri** 參數。

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.params.json
   ```
      
[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)] 

如果您範本所含的參數名稱與 PowerShell 命令中的其中一個參數一樣，系統會提示您提供該參數的值。 Azure PowerShell 會對來自您範本的參數提供後置 **FromTemplate**。 例如，範本中名為 **ResourceGroupName** 的參數會與 [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。 一般而言，在為參數命名時，請勿使用與部署作業所用參數相同的名稱，以避免發生這種混淆的情形。

## <a name="parameter-precedence"></a>參數優先順序

您可以在相同的部署作業中使用內嵌參數和本機參數檔案。 例如，您可以在部署期間指定本機參數檔案中的某些值，並新增其他內嵌值。 如果您同時為本機檔案中和內嵌的參數提供值，內嵌值的優先順序較高。

不過，當使用外部參數檔案時，您無法傳遞內嵌或本機檔案的其他值。 當您指定 **TemplateParameterUri** 參數中的參數檔案時，所有內嵌參數都會被忽略。 請提供外部檔案中的所有參數值。 如果您的範本包含不能包含在參數檔案中的機密值，請將該值新增至金鑰保存庫，或以動態方式提供所有內嵌參數值。

## <a name="next-steps"></a>後續步驟
* 如需透過 .NET 用戶端程式庫部署資源的範例，請參閱 [使用 .NET 程式庫與範本部署資源](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 若要在範本中定義參數，請參閱 [編寫範本](resource-group-authoring-templates.md#parameters)。
* 如需將您的方案部署到不同環境的指引，請參閱 [Microsoft Azure 中的開發和測試環境](solution-dev-test-environments.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。
* 如需關於自動化部署的四部分系列，請參閱[自動化應用程式部署至 Azure 虛擬機器](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 這個系列涵蓋應用程式架構、存取與安全性、可用性與級別，以及應用程式部署。




<!--HONumber=Jan17_HO3-->


