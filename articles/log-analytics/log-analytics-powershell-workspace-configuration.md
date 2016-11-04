---
title: 使用 PowerShell 建立和設定 Log Analytics 工作區 | Microsoft Docs
description: Log Analytics 會使用來自內部部署或雲端基礎結構中之伺服器的資料。 您可以在 Azure 診斷產生電腦資料時，從 Azure 儲存體加以收集。
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 08/15/2016
ms.author: richrund

---
# <a name="manage-log-analytics-using-powershell"></a>使用 PowerShell 管理 Log Analytics
您可以從命令列或在指令碼中，使用 [Log Analytics PowerShell Cmdlet](http://msdn.microsoft.com/library/mt188224.aspx) 在 Log Analytics 中執行各種功能。  您可以使用 PowerShell 執行的工作範例包括︰

* 建立工作區
* 新增或移除方案
* 匯入和匯出已儲存的搜尋
* 建立電腦群組
* 從已安裝 Windows 代理程式的電腦啟用 IIS 記錄檔收集功能
* 從 Linux 和 Windows 電腦收集效能計數器
* 在 Linux 電腦上收集 syslog 事件 
* 從 Windows 事件記錄檔收集事件
* 收集自訂事件記錄檔
* 將 Log Analytics 代理程式加入至 Azure 虛擬機器
* 設定 Log Analytics 將 Azure 診斷所收集的資料編製索引

本文提供兩個程式碼範例，示範您可以從 PowerShell 執行的一些功能。  關於其他功能，您可以參考 [Log Analytics PowerShell Cmdlet 參考文件](http://msdn.microsoft.com/library/mt188224.aspx) 。

> [!NOTE]
> Log Analytics 在以前稱為 Operational Insights，這也是 Cmdlet 中使用此名稱的原因。
> 
> 

## <a name="prerequisites"></a>必要條件
若要透過 PowerShell 來使用 Log Analytics 工作區，您必須具有：

* Azure 訂用帳戶，而且 
* 將您的 Azure Log Analytics 工作區連結到您的 Azure 訂用帳戶。

如果您已建立 OMS 工作區，但它尚未連結到 Azure 訂用帳戶，您可以建立連結︰

* 在 Azure 入口網站
* 在 OMS 入口網站或 
* 使用 Get-AzureRmOperationalInsightsLinkTargets 和 New-AzureRmOperationalInsightsWorkspace Cmdlet。

## <a name="create-and-configure-a-log-analytics-workspace"></a>建立及設定 Log Analytics 工作區
下列指令碼範例說明如何：

1. 建立工作區
2. 列出可用的方案
3. 將方案加入至工作區
4. 匯入已儲存的搜尋
5. 匯出已儲存的搜尋
6. 建立電腦群組
7. 從已安裝 Windows 代理程式的電腦啟用 IIS 記錄檔收集功能
8. 從 Linux 電腦收集邏輯磁碟效能計數器 (% Used Inodes; Free Megabytes; % Used Space; Disk Transfers/sec; Disk Reads/sec; Disk Writes/sec)
9. 從 Linux 電腦收集 syslog 事件
10. 從 Windows 電腦的應用程式事件記錄檔收集錯誤和警告事件
11. 從 Windows 電腦收集記憶體可用 Mb 效能計數器
12. 收集自訂記錄檔 

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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

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
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
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

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>設定 Log Analytics 來編製 Azure 診斷的索引
對於無代理程式監視的 Azure 資源，資源必須啟用及設定 Azure 診斷，以寫入至儲存體帳戶。 然後可以設定 Log Analytics 以從儲存體帳戶收集記錄檔。 您需要進行上述設定的資源包括︰

* 傳統雲端服務 (Web 和背景工作角色)
* Service Fabric 叢集
* 網路安全性群組
* 金鑰保存庫和 
* 應用程式閘道

您也可以使用 PowerShell 在一個 Azure 訂用帳戶中設定 Log Analytics 工作區，以便從不同的 Azure 訂用帳戶收集記錄檔。

下列範例示範如何執行：

1. 列出現有的儲存體帳戶和 Log Analytics 檢索的資源來源位置
2. 建立組態以讀取儲存體帳戶
3. 更新新建立的組態以檢索其他位置的資料
4. 刪除新建立的組態

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
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

## <a name="next-steps"></a>後續步驟
* [檢閱 Log Analytics PowerShell Cmdlet](http://msdn.microsoft.com/library/mt188224.aspx) 。

<!--HONumber=Oct16_HO2-->


