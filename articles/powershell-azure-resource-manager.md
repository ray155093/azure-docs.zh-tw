<properties 
	pageTitle="Azure PowerShell 搭配資源管理員 | Microsoft Azure" 
	description="使用 Azure PowerShell 將多個資源做為資源群組部署至 Azure 的簡介。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/11/2016" 
	ms.author="tomfitz"/>

# 搭配使用 Azure PowerShell 與 Azure 資源管理員

> [AZURE.SELECTOR]
- [入口網站](azure-portal/resource-group-portal.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [節點](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


Azure 資源管理員介紹一種看待 Azure 資源的嶄新方式。與其建立並管理個別資源，您首先想像整個解決方案，例如，部落格、相片庫、SharePoint 入口網站或 Wiki。使用範本 (解決方案的宣告式呈現) 建立包含支援解決方案所需所有資源的資源群組。然後，以邏輯單元的方式來管理與部署該資源群組。

在本教學課程中，您將了解如何搭配使用 Azure PowerShell 與 Azure 資源管理員。它會逐步引導您完成部署解決方案，以及使用該解決方案的程序。您將使用 Azure PowerShell 和資源管理員範本來部署︰

- SQL Server - 主控資料庫
- SQL Database - 儲存資料
- 防火牆規則 - 允許 Web 應用程式連接到資料庫
- App Service 計劃 - 定義 Web 應用程式的功能和成本
- 網站 - 執行 Web 應用程式
- Web 組態 - 將連接字串儲存到資料庫
- 警示規則 - 用於監視效能和錯誤
- App Insights - 用於自動調整設定

## 必要條件

若要完成本教學課程，您需要：

- 一個 Azure 帳戶
  + 您可以[免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F) - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。除非您明確變更您的設定且同意付費，否則我們將不會從您的信用卡收取任何費用。
  
  + 您可以[啟用 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - 您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。
- Azure PowerShell 1.0。如需有關此版本以及如何安裝的資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

本教學課程是專為 PowerShell 初學者所設計的，但它會假設您已了解基本概念，例如模組、Cmdlet 和工作階段。

## 取得 Cmdlet 的說明

若要取得您在本教學課程中任何所見 Cmdlet 的詳細說明，請使用 Get-Help Cmdlet。

    Get-Help <cmdlet-name> -Detailed

例如，如需取得 Get-AzureRmResource Cmdlet 的說明，請輸入：

    Get-Help Get-AzureRmResource -Detailed

若要取得資源模組中的 Cmdlet 清單及說明概要，請輸入：

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

輸出類似如下摘錄：

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

若要取得完整的 cmdlet，請輸入以下格式的命令：

    Get-Help <cmdlet-name> -Full
  
## 登入您的 Azure 帳戶

在使用您的解決方案之前，您必須登入您的帳戶。

若要登入您的 Azure 帳戶，請使用 **Add-AzureRmAccount** Cmdlet。

    Add-AzureRmAccount

cmdlet 會提示您 Azure 帳戶的登入認證。登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。

帳戶設定已過期，因此您必須偶爾重新整理這些設定。若要重新整理帳戶設定，請再次執行 **Add-AzureRmAccount**。

>[AZURE.NOTE] 資源管理員模組需要 Add-AzureRmAccount。發佈設定檔案不符合需求。

如果您有多個訂用帳戶，請使用 **Set-AzureRmContext** Cmdlet 提供您想要用於部署的訂用帳戶識別碼。

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## 建立資源群組

將任何資源部署至訂用帳戶之前，您必須建立將包含資源的資源群組。

若要建立資源群組，請使用 **New-AzureRmResourceGroup** Cmdlet。

此命令會使用 **Name** 參數來指定資源群組的名稱，並使用 **Location** 參數來指定其位置。根據我們在上一節中的發現，我們將使用「美國西部」做為位置。

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
輸出將類似於：

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

已成功建立您的資源群組。

## 部署您的解決方案

本主題不會顯示如何建立您的範本或討論範本的結構。如需該資訊，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)和 [Resource Manager 範本逐步解說](resource-manager-template-walkthrough.md)。您會從 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)部署預先定義的[佈建 Web 應用程式與 SQL Database](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)範本。

您具有資源群組和範本，因此現在您已經準備好要將在您的範本中定義的基礎結構部署至資源群組。使用 **New-AzureRmResourceGroupDeployment** Cmdlet 部署資源。範本會指定許多預設值，我們將使用這些預設值，所以您不需要提供這些參數的值。基本語法如下所示：

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

您指定資源群組和範本的位置。如果您的範本是本機檔案，可以使用 **-TemplateFile** 參數並指定範本的路徑。您可以將 **-Mode** 參數設為 **Incremental** 或 **Complete**。資源管理員預設會在部署期間執行累加式更新；因此，當您想要使用 **Incremental** 時不需要設定 **-Mode**。若要了解這些部署模式的差異，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

###動態範本參數

如果您熟悉 PowerShell，您知道您可以輸入減號 (-) 然後按下 TAB 鍵，循環 Cmdlet 的可用參數。相同的功能也適用於您在範本中定義的參數。在您輸入範本名稱之後，Cmdlet 便會立即擷取範本、剖析範本，並將範本參數動態新增至命令。這會讓指定範本參數值變得再容易不過了。

當您輸入命令時，系統會提示您輸入遺漏的必要參數 **administratorLoginPassword**。並在您輸入密碼時，此安全字串值便會被遮住。如此，就不會有以純文字方式提供密碼的風險。

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

如果範本中有一個參數的名稱符合範本部署命令的其中一個參數 (例如範本中包含名為 **ResourceGroupName** 的參數，而該名稱與 [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) Cmdlet 中的 **ResourceGroupName** 參數相同)，系統會提示您為後置詞是 **FromTemplate** 的參數 (例如 **ResourceGroupNameFromTemplate**) 提供一個值。一般而言，請不要將參數命名為與部署作業所用的參數同名，以避免這種混淆情形。

資源建立時，命令會執行並且傳回訊息。最後，您會看到您的部署結果。

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

只需幾個步驟，即可建立與部署複雜網站所需的資源。

### 記錄偵錯資訊

在部署範本時，藉由指定執行 **New-AzureRmResourceGroupDeployment** 時的 **-DeploymentDebugLogLevel** 參數，即可記錄有關要求和回應的其他資訊。此資訊可協助您疑難排解部署錯誤。預設值為 **None**，意指不會記錄任何要求或回應內容。您可以指定從要求、回應或兩者記錄內容。如需有關針對部署進行疑難排解和記錄偵錯資訊的詳細資訊，請參閱[透過 Azure PowerShell 針對資源群組部署進行疑難排解](resource-manager-troubleshoot-deployments-powershell.md)。下列範例會記錄部署的要求內容和回應內容。

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] 設定 DeploymentDebugLogLevel 參數時，請仔細考慮您在部署期間傳入的資訊類型。透過記錄要求或回應的相關資訊，您可能會公開透過部署作業擷取的機密資料。


## 取得資源群組的相關資訊

建立資源群組之後，您可以使用資源管理員模組中的 Cmdlet，來管理您的資源群組。

- 若要取得您訂用帳戶中的資源群組，請使用 **Get-AzureRmResourceGroup** Cmdlet：

		Get-AzureRmResourceGroup -ResourceGroupName TestRG1
	
	它會傳回下列資訊︰

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

	如果您未指定資源群組名稱，此 Cmdlet 會傳回您的訂用帳戶中的所有資源群組。

- 若要取得您資源群組中的資源，請使用 **Find-AzureRmResource** Cmdlet 及其 **ResourceGroupNameContains** 參數。在沒有使用參數的情況下，Find-AzureRmResource 可取得您 Azure 訂用帳戶中的所有資源。

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
	
     它會傳回格式如下的資源清單︰
		
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
	        
- 您可以使用標記以邏輯方式組織您的訂用帳戶中的資源，並利用 **Find-AzureRmResource** 和 **Find-AzureRmResourceGroup** Cmdlet 擷取資源。

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      還有更多功能可搭配標記使用。如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](resource-group-using-tags.md)。

## 新增至資源群組

若要將資源新增至資源群組，可以使用 **New-AzureRmResource** Cmdlet。不過，以這種方式新增資源可能會造成未來的混淆，因為新的資源不存在於您的範本。如果您重新部署舊範本，則會部署不完整的解決方案。如果您經常部署，您會發現將新的資源新增至您的範本並重新部署它更方便且更可靠。

## 移動資源

您可以將現有的資源移動到新的資源群組。如需範例，請參閱[將資源移至新的資源群組或訂用帳戶](resource-group-move-resources.md)。

## 匯出範本

對於現有的資源群組 (透過 PowerShell 或 入口網站等其他方法部署)，您可以檢視資源群組的資源管理員範本。匯出此範本有兩個優點︰

1. 因為所有基礎結構都已定義於範本中，所以您可以輕鬆地自動進行解決方案的未來部署。

2. 您可以查看代表您的解決方案的 JavaScript 物件標記法 (JSON)，藉此熟悉範本語法。

透過 PowerShell，您可以產生代表資源群組目前狀態的範本，或擷取特定部署所用的範本。

您已變更資源群組，而且需要擷取其目前狀態的 JSON 表示法時，匯出資源群組的範本很有用。不過，產生的範本只包含最少的參數數目，但不包含任何變數。範本中大部分的值為硬式編碼。在部署所產生的範本之前，您可能想要將更多的值轉換成參數，以便針對不同的環境自訂部署。

當您需要檢視用來部署資源的實際範本時，針對特定部署匯出範本很有用。範本會包含針對原始部署定義的所有參數和變數。不過，如果您組織中有人已變更非此範本中定義的資源群組，此範本並不會代表資源群組的目前狀態。

> [AZURE.NOTE] 匯出範本功能處於預覽狀態，並非所有的資源類型目前都支援匯出範本。嘗試匯出範本時，您可能會看到一個錯誤，表示未匯出某些資源。如有需要，您可以在下載範本之後，在範本中手動定義這些資源。

###從資源群組匯出範本

若要檢視資源群組的範本，請執行 **Export-AzureRmResourceGroup** Cmdlet。

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###從部署下載範本

若要下載特定部署所用的範本，請執行 **Save-AzureRmResourceGroupDeploymentTemplate** Cmdlet。

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## 刪除資源或資源群組

- 若要從資源群組中刪除資源，請使用 **Remove-AzureRmResource** Cmdlet。此 Cmdlet 會刪除資源，但不會刪除資源群組。

	此命令會將 TestSite 網站從 TestRG1 資源群組中移除。

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- 若要刪除資源群組，請使用 **Remove-AzureRmResourceGroup** Cmdlet。此 Cmdlet 會刪除資源群組及其資源。

		Remove-AzureRmResourceGroup -Name TestRG1
		
	系統會要求您確認刪除。
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## 部署指令碼

本主題中先前的部署範例僅顯示了將資源部署到 Azure 所需的個別 Cmdlet。下列範例會顯示可建立資源群組以及部署資源的部署指令碼。

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## 後續步驟

- 若要了解如何建立資源管理員範本，請參閱[撰寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)。
- 若要了解部署範本的相關資訊，請參閱[使用 Azure 資源管理員範本部署應用程式](./resource-group-template-deploy.md)。
- 如需部署專案的詳細範例，請參閱[透過可預測方式在 Azure 中部署微服務](app-service-web/app-service-deploy-complex-application-predictably.md)。
- 若要了解如何疑難排解失敗的部署，請參閱[在 Azure 中疑難排解資源群組部署](./resource-manager-troubleshoot-deployments-powershell.md)。

<!---HONumber=AcomDC_0706_2016-->