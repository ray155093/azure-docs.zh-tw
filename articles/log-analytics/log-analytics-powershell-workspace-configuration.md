<properties
	pageTitle="使用 PowerShell 建立和設定 Log Analytics 工作區 | Microsoft Azure"
	description="Log Analytics 會使用來自內部部署或雲端基礎結構中之伺服器的資料。您可以在 Azure 診斷產生電腦資料時，從 Azure 儲存體加以收集。"
	services="log-analytics"
	documentationCenter=""
	authors="richrundmsft"
	manager="jochan"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="powershell"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="richrund"/>

# 使用 PowerShell 管理 Log Analytics

您可以從命令列或在指令碼中，使用 [記Log Analytics PowerShell Cmdlet](http://msdn.microsoft.com/library/mt188224.aspx) 在 Log Analytics 中執行各種功能。您可以使用 PowerShell 執行的工作範例包括︰

+ 建立工作區
+ 新增或移除方案
+ 匯入和匯出已儲存的搜尋
+ 將 Log Analytics 代理程式加入至 Azure 虛擬機器
+ 設定 Log Analytics 將 Azure 診斷所收集的資料編製索引

本文提供兩個程式碼範例，示範您可以從 PowerShell 執行的一些功能。關於其他功能，您可以參考 [Log Analytics PowerShell Cmdlet 參考文件](http://msdn.microsoft.com/library/mt188224.aspx)。

> [AZURE.NOTE] Log Analytics 在以前稱為 Operational Insights，這也是 Cmdlet 中使用此名稱的原因。

## 必要條件

若要透過 PowerShell 來使用 Log Analytics 工作區，您必須具有：

+ Azure 訂用帳戶，而且 
+ 將您的 Azure Log Analytics 工作區連結到您的 Azure 訂用帳戶。

如果您已建立 OMS 工作區，但尚未將它連結到 Azure 訂用帳戶，您可以在 Azure 入口網站、OMS 入口網站或使用 Get-AzureRMOperationalInsightsLinkTargets 和 New-AzureRMOperationalInsightsWorkspace Cmdlet 建立連結。

## 建立 Log Analytics 工作區、加入方案和已儲存的搜尋

下列指令碼範例說明如何：

1.	建立工作區
2.	列出可用的方案
3.	將方案加入至工作區
4.	匯入已儲存的搜尋
5.	匯出已儲存的搜尋

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName="C:" CounterName="Current Disk Queue Length"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($soln in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $soln -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

```

## 設定 Log Analytics 來編製 Azure 診斷的索引 

關於 Azure 資源的無代理程式監視，包括 Web 和背景工作角色、Service Fabric 叢集、網路安全性群組、金鑰保存庫和應用程式閘道，這些資源必須先啟用 Azure 診斷來寫入儲存體帳戶，然後才能設定 Log Analytics 從儲存體帳戶收集記錄檔。

您也可以使用 PowerShell 在一個 Azure 訂用帳戶中設定 Log Analytics 工作區，以便從不同的 Azure 訂用帳戶收集記錄檔。

下列範例示範如何執行：

1.	列出現有的儲存體帳戶和 Log Analytics 檢索的資源來源位置
2.	建立新的組態以讀取儲存體帳戶
3.	更新新建立的組態以檢索其他位置的資料
4.	刪除新建立的組態

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two with the storage account resource id and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## 後續步驟

- 如需有關使用 PowerShell 來設定 Log Analytics 的資訊，請[檢閱 Log Analytics PowerShell Cmdlet](http://msdn.microsoft.com/library/mt188224.aspx)。

<!---HONumber=AcomDC_0518_2016-->