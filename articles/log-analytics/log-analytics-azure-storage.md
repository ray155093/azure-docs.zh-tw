<properties
	pageTitle="將 Azure 儲存體連接至 Log Analytics | Microsoft Azure"
	description="Log Analytics 會使用來自內部部署或雲端基礎結構中之伺服器的資料。您可以在 Azure 診斷產生電腦資料時，從 Azure 儲存體加以收集。"
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
	ms.date="04/28/2016"
	ms.author="banders"/>

# 將 Azure 儲存體連接至 Log Analytics

Log Analytics 會使用來自內部部署或雲端基礎結構中之伺服器的資料。您可以在 Azure 診斷產生電腦資料時，從 Azure 儲存體加以收集。

利用收集自 Azure 儲存體的資料，您快速搜尋雲端服務和虛擬機器的事件與 IIS 記錄檔。您也可以安裝 Microsoft Monitoring Agent，從您的虛擬機器取得額外的見解。

更新評估、變更追蹤和 SQL 評估解決方案皆可搭配使用 Microsoft Monitoring Agent，在您的虛擬機器上提供更深入的見解。您可以在登入 [OMS 入口網站](https://www.microsoft.com/oms/)時，[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md) (如果您尚未這樣做)。

若為 Azure 虛擬機器，有兩個簡單的方法可啟用代理程式型資料收集：

- 在 Microsoft Azure 管理入口網站中
- 使用 PowerShell

針對記錄資料，使用代理程式型收集時，您必須在 [OMS 入口網站](https://www.microsoft.com/oms/)的 [記錄檔管理] 設定頁面中設定要收集的記錄檔。

>[AZURE.NOTE] 如果您已將 OMS 設為使用 Azure 診斷來編製記錄資料的索引，並設定代理程式來收集記錄檔，則系統會重複編製相同記錄檔的索引。這兩個資料來源會依照一般資料費率向您收費。如果您已安裝代理程式，您應該使用代理程式收集記錄資料，而不將 Azure 診斷所收集的記錄檔編製索引。

## Microsoft Azure 入口網站

您可以安裝 OMS 的代理程式，並使用 [Azure 入口網站](https://portal.azure.com)，連接執行該代理程式的 Azure 虛擬機器。

### 若要安裝 OMS 的代理程式，並將虛擬機器連接至工作區

1.	登入 [Azure 入口網站](http://portal.azure.com)。
2.	瀏覽以尋找 **Log Analytics (OMS)**，然後加以選取。
3.	在您的 OMS 工作區清單中，選取 Azure VM 要連接的工作區。 ![OMS 工作區](./media/log-analytics-azure-storage/oms-connect-azure-01.png)
4.	在 [Log Analytics 管理] 下方，按一下 [虛擬機器]。![虛擬機器](./media/log-analytics-azure-storage/oms-connect-azure-02.png)
5.	在 [虛擬機器] 清單中，選取您要為其安裝代理程式的虛擬機器。VM 的 [OMS connection status]\(OMS 連接狀態) 會指出它是 [未連接] 狀態。![VM 未連接](./media/log-analytics-azure-storage/oms-connect-azure-03.png)
6.	在虛擬機器的詳細資訊中，按一下 [連接]。隨即自動安裝代理程式並針對 OMS 工作區進行設定，但程序可能需要幾分鐘才能完成。![連接 VM](./media/log-analytics-azure-storage/oms-connect-azure-04.png)
7.	當代理程式安裝並連接時，您會看到 [OMS connection status]\(OMS 連接狀態) 更新為顯示 [This workspace]\(此工作區)。

![已連接](./media/log-analytics-azure-storage/oms-connect-azure-05.png)

>[AZURE.NOTE] 您必須安裝 [Azure VM 代理程式](../virtual-machines/virtual-machines-windows-extensions-features.md)，才會自動安裝 OMS 的代理程式。如果您有 Azure Resource Manager 虛擬機器，代理程式也不會顯示在清單中，而必須使用 PowerShell 或建立 ARM 範本來進行安裝。

如果 Azure 管理入口網站未顯示某些 VM，可能原因包括：

- 該 VM 已受不同 OMS 工作區的管理
- 該 VM 是目前仍不支援的 ARM VM

## PowerShell

如果您想要編寫指令碼以變更 Azure 虛擬機器，您可以使用 PowerShell 以啟用 Microsoft Monitoring Agent。

Microsoft Monitoring Agent 是 [Azure 虛擬機器擴充功能](../virtual-machines/virtual-machines-windows-extensions-features.md)，您可以使用 PowerShell 管理它，如下列範例所示。

若是傳統的 Azure 虛擬機器，請使用下列 PowerShell 範例：

```
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```


若是 Azure Resource Manager 虛擬機器，請使用下列 PowerShell 範例：

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName) 
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId 
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRMVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

Set-AzureRMVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId':  '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey' }"


```
使用 PowerShell 進行設定時，您必須提供工作區識別碼及主要金鑰。您可以在 OMS 入口網站的 [設定] 頁面上，或使用上述範例所示的 PowerShell，找到您的工作區 ID 及主索引鍵。

![工作區 ID 及主索引鍵](./media/log-analytics-azure-storage/oms-analyze-azure-sources.png)

## 使用 Azure 診斷收集資料

OMS 可以分析由 Azure 診斷寫入 Azure 儲存體的資料。有 2 個要執行的主要步驟：

- 設定將診斷資料收集至 Azure 儲存體
- 設定 OMS 來分析儲存體帳戶中的資料

下列主題說明如何將診斷資料收集至 Azure 儲存體，以及如何設定 OMS 來分析 Azure 儲存體中的資料。

Azure 診斷是 Azure 的擴充功能，可讓您從背景工作角色、Web 角色或 Azure 中執行的虛擬機器收集診斷資料。資料會儲存在 Azure 儲存體帳戶中，並可由 OMS 使用。

>[AZURE.NOTE] 如果您具有付費的訂用帳戶，在您將診斷傳送至儲存體帳戶，或是當 OMS 從您的儲存體帳戶讀取資料時，都需要支付儲存和交易的一般資料費用。

Azure 診斷可收集下列類型的遙測資料：

資料來源|說明
 ---|---
IIS 記錄檔|IIS 網站的相關資訊。
Azure 診斷基礎結構記錄檔|診斷本身的相關資訊。
IIS 失敗要求記錄檔 |IIS 網站或應用程式之失敗要求的相關資訊。
Windows 事件記錄檔|傳送至 Windows 事件記錄系統的資訊。
效能計數器|作業系統和自訂效能計數器。
損毀傾印|應用程式損毀時之處理序狀態的相關資訊。
自訂錯誤記錄檔|您的應用程式或服務所建立的記錄檔。
NET EventSource|您的程式碼使用 .NET [EventSource 類別](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource%28v=vs.110%29.aspx)所產生的事件
以資訊清單為基礎的 ETW|任何處理序所產生的 ETW 事件
Syslog|傳送至 Syslog 或 Rsyslog 精靈的事件


目前，OMS 能夠分析：

- 來自 Web 角色和虛擬機器的 IIS 記錄檔
- 來自執行 Windows 作業系統的 Web 角色、背景工作角色及 Azure 虛擬機器的 Windows 事件記錄檔和 ETW 記錄檔
- 從 Azure 虛擬機器執行 Linux 作業系統的 Syslog
- 針對網路安全性群組、應用程式閘道和 KeyVault 資源，以 json 格式寫入 blob 儲存體的診斷

記錄檔必須位於下列位置：

- WADWindowsEventLogsTable (資料表儲存體) – 包含來自 Windows 事件記錄檔的資訊。
- WADETWEventTable (表格儲存體) – 包含來自 Windows ETW 記錄檔的資訊。
- WADServiceFabricSystemEventTable, WADServiceFabricReliableActorEventTable, WADServiceFabricReliableServiceEventTable (表格儲存體) - 包含 Service Fabric 作業、執行者和服務事件的相關資訊。
- wad-iis-logfiles (Blob 儲存體) – 包含 IIS 記錄檔的相關資訊。
- LinuxsyslogVer2v0 (資料表儲存體) – 包含 Linux syslog 事件。

    >[AZURE.NOTE] 的 IIS 記錄檔，以啟用額外的見解。

若為虛擬機器，您也可以選擇將 [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) 安裝於虛擬機器以啟用其他見解。除了分析 IIS 記錄檔和事件記錄檔之外，您也能夠執行其他分析，包括組態變更追蹤、SQL 評估及更新評估。

您可以在[意見反應頁面](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy)進行投票，幫助我們為 OMS 排定其他記錄檔的分析優先順序。

## 在 Web 角色中針對 IIS 記錄檔和事件集合啟用 Azure 診斷

請參閱[如何在雲端服務中啟用診斷](https://msdn.microsoft.com/library/azure/dn482131.aspx)。您將使用該處的基本資訊，並在這裡自訂用於 OMS 的步驟。

啟用 Azure 診斷時：

- 預設會儲存 IIS 記錄檔，並在 scheduledTransferPeriod 傳輸間隔傳輸記錄檔資料。

- 預設不會傳輸 Windows 事件記錄檔。

### 啟用診斷

若要啟用 Windows 事件記錄檔，或變更 scheduledTransferPeriod，請使用 XML 組態檔 (diagnostics.wadcfg) 設定 Azure 診斷，如＜如何在雲端服務中啟用診斷＞主題中的[步驟 2：將 diagnostics.wadcfg 檔案加入您的 Visual Studio 方案](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2)和[步驟 3：設定應用程式的診斷](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3)中所示。下列範例組態檔會從應用程式和系統記錄檔中收集 IIS 記錄檔和所有事件：

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

在＜如何在雲端服務中啟用診斷＞主題中的[步驟 4：設定診斷資料的儲存體](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4)中，確定您的 ConfigurationSettings 會指定儲存體帳戶，如下列範例所示：

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** 和 **AccountKey** 值可在 Microsoft Azure 管理入口網站的儲存體帳戶儀表板的 [管理存取金鑰] 下找到。連接字串的通訊協定必須為 **https**。

將更新的診斷組態套用至雲端服務，且該服務已開始將診斷寫入 Azure 儲存體時，您即可設定 OMS。

## 為事件記錄檔和 IIS 記錄檔集合啟用虛擬機器中的 Azure 診斷

您可以搭配使用下列程序與 Microsoft Azure 管理入口網站，為事件記錄檔和 IIS 記錄檔集合啟用虛擬機器中的 Azure 診斷。

### 使用 Azure 管理入口網站啟用虛擬機器中的 Azure 診斷

1. 建立虛擬機器時安裝 VM 代理程式。如果虛擬機器已經存在，請確認已安裝 VM 代理程式。
	- 如果您使用預設的 Azure 管理入口網站建立虛擬機器，請執行 [自訂建立] 並選取 [安裝 VM 代理程式]。
	- 如果使用新的 Azure 管理入口網站來建立虛擬機器，請依序選取 [選擇性組態] 及 [診斷]，並將 [狀態] 設為 [開啟]。

	完成時，VM 會自動安裝和執行 Azure 診斷擴充功能以負責收集診斷資料。

2. 在現有的 VM 上啟用監視和設定事件記錄。您可以在 VM 層級上啟用診斷。若要啟用診斷然後設定事件記錄，請執行下列步驟：
	1. 選取 VM。
	2. 按一下 [監視]。
	3. 按一下 [診斷]。
	4. 將 [狀態] 設為 [開啟]。
	5. 選取您想要使用的每個診斷度量。OMS 可分析 Windows 事件系統記錄檔、Windows 事件應用程式記錄檔和 IIS 記錄檔。
	7. 按一下 [確定]。

您可以使用 Azure PowerShell，更精確地指定寫入至 Azure 儲存體的事件。請參閱 Azure 診斷 1.2 組態結構描述，以取得組態檔範例和其結構描述的詳細文件。確定從[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 安裝並設定 Azure PowerShell 0.8.7 版或更新版本。如果您安裝 1.2 版之前的 Microsoft Azure 診斷，您無法使用新的入口網站來啟用或設定診斷。

您可以使用下列 PowerShell 指令碼啟用和更新代理程式。您也可以搭配使用此指令碼與自訂記錄組態。您必須修改指令碼來設定儲存體帳戶、服務名稱和虛擬機器名稱。

### 使用 Azure PowerShell 啟用虛擬機器中的診斷

檢閱下列指令碼範例、複製它、視需要修改它、將範例儲存為 PowerShell 指令碼檔案，然後再執行指令碼。


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## 啟用 OMS 的 Azure 儲存體分析

您可以使用 [Log Analytics 中的資料來源](log-analytics-data-sources.md#collect-data-from-azure-diagnostics)的資訊，啟用儲存體分析，並將 OMS 設為從具備 Azure 診斷功能的 Azure 儲存體帳戶進行存取。

大約 1 小時後，您就會看到來自儲存體帳戶的資料，這些資訊可在 OMS 中進行分析。


## 後續步驟

- 如果您的組織使用 Proxy 伺服器或防火牆，請[在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)，讓代理程式可與 Log Analytics 服務通訊。

<!---HONumber=AcomDC_0518_2016-->