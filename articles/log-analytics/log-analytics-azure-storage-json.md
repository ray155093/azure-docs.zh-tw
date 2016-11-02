<properties
    pageTitle="使用 Log Analytics 分析 Azure 診斷記錄 | Microsoft Azure"
    description="Log Analytics 可以從 Azure 服務讀取記錄，這些服務以 JSON 格式將 Azure 診斷記錄寫入 Blob 儲存體。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>



# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>使用 Log Analytics 分析 Azure 診斷記錄

Log Analytics 可以從下列 Azure 服務收集記錄，這些服務以 JSON 格式將 [Azure 診斷記錄](../azure-portal/monitoring-overview-of-diagnostic-logs.md)寫入 Blob 儲存體：

+ 自動化 (預覽)
+ 金鑰保存庫 (預覽)
+ 應用程式閘道 (預覽)
+ 網路安全性群組 (預覽)

下列各節逐步引導您使用 PowerShell 來執行下列動作：

+ 設定 Log Analytics，以針對每個資源收集來自儲存體的記錄檔  
+ 啟用適用於 Azure 服務的 Log Analytics 解決方案

您必須先啟用 Azure 診斷，Log Analytics 才能收集這些資源的資料。 使用 `Set-AzureRmDiagnosticSetting` 指令程式來啟用記錄。

如需有關如何啟用診斷記錄的詳細資訊，請參閱下列文章：

+ [金鑰保存庫](../key-vault/key-vault-logging.md)
+ [應用程式閘道](../application-gateway/application-gateway-diagnostics.md)
+ [網路安全性群組](../virtual-network/virtual-network-nsg-manage-log.md)

這份文件也包含這幾方面的詳細資料︰

+ 針對資料收集進行疑難排解
+ 停止收集資料

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>設定 Log Analytics 來收集 Azure 診斷記錄

使用 PowerShell 指令碼收集這些服務的記錄，並讓解決方案將記錄視覺化。

下列範例會對所有支援的資源啟用記錄

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


對於某些資源，您可以從 Azure 入口網站使用 [Azure 診斷記錄概觀](../azure-portal/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)中所述的步驟執行上述設定。

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>設定 Log Analytics 來收集 Azure 診斷記錄

我們提供一個 PowerShell 指令碼模組，匯出兩個 Cmdlet 以協助設定 Log Analytics：

1. `Add-AzureDiagnosticsToLogAnalyticsUI` 會提示您輸入，而且能夠設定簡單的組態
2. `Add-AzureDiagnosticsToLogAnalytics` 會取得要監視的資源做為輸入，然後設定 Log Analytics  

### <a name="pre-requisites"></a>必要條件

1. Azure PowerShell 以及 Operational Insights Cmdlet 版本 1.0.8 或更新版本。
  - [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)
  - 請確認您的版本： `Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. 診斷記錄是針對您想要監視的 Azure 資源所設定。 使用 `Set-AzureRmDiagnosticSetting`，或請參閱[使用 Log Analytics 收集來自 Azure 儲存體帳戶的資料](log-analytics-azure-storage.md)，以了解如何啟用診斷。
3. [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS) 工作區  
4. AzureDiagnosticsAndLogAnalytics PowerShell 模組
  - 從 PowerShell 資源庫下載 [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) 模組

### <a name="option-1:-run-the-interactive-configuration-scripts"></a>選項 1︰執行互動式組態指令碼

開啟 PowerShell 並執行︰

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

您會看見可用選項清單，或者系統會提示您進行選取。
系統會要求您針對下列各項進行選擇︰

+ 要從中收集記錄檔的資源類型 (Azure 服務)
+ 要從中收集記錄檔的資源執行個體
+ 要收集資料的 Log Analytics 工作區

執行這個指令碼之後，您應該可以在將診斷資料寫入儲存體之後約 30 分鐘，於 Log Analytics 中看見記錄。 如果您無法在這個時間之後取得記錄，請參閱下列的疑難排解小節。

### <a name="option-2:-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>選項 2︰建置資源清單，並將其傳遞到組態 Cmdlet

您可以建置已啟用 Azure 診斷的資源清單，然後將資源傳遞到組態 Cmdlet。

您可以執行 `Get-Help Add-AzureDiagnosticsToLogAnalytics` 來查看有關此 Cmdlet 的其他資訊。


深入了解 OMS [Log Analytics PowerShell Cmdlet](https://msdn.microsoft.com/library/mt188224.aspx)

>[AZURE.NOTE] 如果資源和工作區位於不同的 Azure 訂用帳戶，請視需要使用 `Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>` 來切換它們

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
執行這個指令碼之後，您應該可以在將診斷資料寫入儲存體之後約 30 分鐘，於 Log Analytics 中看見記錄。 如果您無法在這個時間之後取得記錄，請參閱下列的疑難排解小節。  

>[AZURE.NOTE] Azure 入口網站中看不到此組態。 您可以使用 `Get-AzureRmOperationalInsightsStorageInsight` Cmdlet 來確認組態。  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>停止 Log Analytics 收集 Azure 診斷記錄

若要刪除資源的 Log Analytics 組態，請使用 `Remove-AzureRmOperationalInsightsStorageInsight` Cmdlet。

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>針對 Azure 診斷記錄的組態進行疑難排解

*問題*

設定資源來記錄至傳統儲存體時顯示下列錯誤︰

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*解決方案*

使用 `Add-AzureAccount` 登入傳統部署模型的 API

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>針對 Azure 診斷記錄的資料收集進行疑難排解

如果您在 Log Analytics 中看不到 Azure 資源的資料，您可以使用下列疑難排解步驟：

+ 確認資料是傳輸到儲存體帳戶
+ 確認適用於服務的 Log Analytics 解決方案已啟用
+ 確認已將 Log Analytics 設定為從儲存體中讀取
+ 更新儲存體帳戶金鑰

### <a name="verify-data-is-flowing-to-the-storage-account"></a>確認資料會傳輸到儲存體帳戶

您可以使用可瀏覽 Azure 儲存體的工具 (例如 Visual Studio) 或使用 PowerShell，以檢查資料是否流向儲存體帳戶。

若要尋找儲存體帳戶，要將資源設定為記錄來使用下列 PowerShell︰

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Azure 診斷所使用的儲存體容器名稱格式如下︰  

`insights-logs-<Category>`

如需深入了解檢視儲存體帳戶的內容，請參閱[使用儲存體 Cmdlet 來檢查容器以取得最新資料](../storage/storage-powershell-guide-full.md)。

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>確認適用於服務的 Log Analytics 解決方案已啟用

如果您使用 `Add-AzureDiagnosticsToLogAnalyticsUI`，即會為您自動啟用正確的 Log Analytics 解決方案。

若要檢查是否已啟用解決方案，請執行下列 PowerShell：

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

如果解決方案尚未啟用，您可以使用下列 PowerShell：

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

若要尋找可針對每個資源類型啟用的解決方案名稱，請使用下列 PowerShell (一旦您匯入模組之後，此變數就可供使用)︰

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>確認已將 Log Analytics 設定為從儲存體中讀取

如果您新增其他 Azure 資源，您需要為它們啟用診斷記錄並設定 Log Analytics。
若要檢查 Log Analytics 已設定收集哪些資源和儲存體帳戶的記錄，請使用下列 PowerShell：

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>更新儲存體金鑰

如果您變更儲存體帳戶的金鑰，Log Analytics 組態也需要使用新的金鑰來更新。
您可以使用下列 PowerShell，利用新的金鑰來更新 Log Analytics 組態︰

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

若要尋找儲存體 Insight 名稱，請使用 `Get-AzureRmOperationalInsightsStorageInsight` Cmdlet，如先前範例所示。

## <a name="next-steps"></a>後續步驟

- [對 IIS 使用 Blob 儲存體，對事件使用表格儲存體](log-analytics-azure-storage-iis-table.md)，以讀取 Azure 服務 (將診斷寫入表格儲存體) 的記錄，或讀取寫入 Blob 儲存體的 IIS 記錄。
- [啟用解決方案](log-analytics-add-solutions.md) 以提供資料的深入見解。
- [使用搜尋查詢](log-analytics-log-searches.md) 以分析資料。



<!--HONumber=Oct16_HO2-->


