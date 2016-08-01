<properties
	pageTitle="SQL Server VM 的自動修補 (傳統) | Microsoft Azure"
	description="針對 Azure 中以傳統部署模式執行的 SQL Server 虛擬機器，說明自動修補功能。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/15/2016"
	ms.author="jroth" />

# Azure 虛擬機器中的 SQL Server 自動修補 (傳統)

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-automated-patching.md)
- [傳統](virtual-machines-windows-classic-sql-automated-patching.md)

自動修補會針對執行 SQL Server 的 Azure 虛擬機器建立維護時間範圍。自動更新只能在此維護時間範圍內安裝。對於 SQL Server，這可以確保系統更新和任何相關聯的重新啟動會在對資料庫最好的時間發生。自動修補相依於 [SQL Server IaaS 代理程式擴充](virtual-machines-windows-classic-sql-server-agent-extension.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 若要檢視這篇文章的 Resource Manager 版本，請參閱 [Automated Patching for SQL Server in Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md) (Azure 虛擬機器的 SQL Server 自動修補 (Resource Manager))。

## 必要條件

若要使用自動修補，請考慮下列必要條件︰

**作業系統**：

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 版本**：

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**：

- 如果您打算使用 PowerShell 設定自動修補，請[安裝最新的 Azure PowerShell 命令](../powershell-install-configure.md)。

>[AZURE.NOTE] 自動修補相依於 SQL Server IaaS 代理程式擴充。目前的 SQL 虛擬機器資源庫映像預設會新增這項擴充。如需詳細資訊，請參閱 [SQL Server IaaS 代理程式擴充](virtual-machines-windows-classic-sql-server-agent-extension.md)。

## Settings

下表說明可以為自動修補設定的選項。實際的設定步驟會依據您是使用 Azure 入口網站或 Azure Windows PowerShell 命令而有所不同。

|設定|可能的值|說明|
|---|---|---|
|**自動修補**|啟用/停用 (已停用)|啟用或停用 Azure 虛擬機器的自動修補。|
|**維護排程**|每天、星期一、星期二、星期三、星期四、星期五、星期六、星期日|虛擬機器的 Windows、SQL Server 和 Microsoft 更新的下載及安裝排程。|
|**維護開始時間**|0-24|更新虛擬機器的當地開始時間。|
|**維護時間範圍**|30-180|允許完成下載和安裝更新的分鐘數。|
|**PATCH 類別**|重要事項|要下載並安裝的更新類別。|

## 入口網站的組態
您可以在佈建期間或針對現有的 VM，使用「Azure 入口網站」來設定「自動修補」。

### 新的 VM
在傳統部署模型中建立新的 SQL Server 虛擬機器時，請使用「Azure 入口網站」來設定「自動修補」。

下列 Azure 入口網站螢幕擷取畫面顯示 [選用組態] | [SQL 自動修補] 下的選項。

![Azure 入口網站中的 SQL 自動修補](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

### 現有的 VM
針對現有的 SQL Server 2012 或 2014 虛擬機器，在虛擬機器屬性的 [**組態**] 區段中，選取 [**自動修補**] 設定。在 [**自動修補**] 視窗中，您可以啟用功能、設定維護排程和啟動時間，以及選擇維護時間範圍。如下列螢幕擷取畫面所示。

![Azure 入口網站中的自動修補設定](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] 當您第一次啟用自動修補時，Azure 會在背景中設定 SQL Server IaaS 代理程式。在此期間，Azure 入口網站可能不會顯示已設定自動修補。請等候幾分鐘的時間來安裝及設定代理程式。之後，Azure 入口網站將會反映新的設定。

## 使用 PowerShell 進行設定

您也可以使用 PowerShell 來設定自動修補。

在下列範例中，會使用 PowerShell 在現有的 SQL Server VM 上設定自動修補。**New-AzureVMSqlServerAutoPatchingConfig** 命令會設定自動更新的維護時間範圍。

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

下表會根據此範例來描述對目標 Azure VM 的實際效果：

|參數|效果|
|---|---|
|**DayOfWeek**|在每個星期四安裝修補程式。|
|**MaintenanceWindowStartingHour**|在上午 11:00 開始更新。|
|**MaintenanceWindowsDuration**|必須在 120 分鐘內安裝修補程式。根據開始時間，其必須在下午 1:00 之前完成。|
|**PatchCategory**|此參數唯一可能的設定是 "Important"。|

可能需要幾分鐘的時間來安裝及設定 SQL Server IaaS 代理程式。

若要停用自動修補，請執行相同的指令碼，但不要對 New-AzureVMSqlServerAutoPatchingConfig 使用 -Enable 參數。和安裝一樣，可能需要幾分鐘的時間來停用自動修補。

## 後續步驟

如需其他可用的自動化工作的相關資訊，請參閱 [SQL Server IaaS Agent 擴充功能](virtual-machines-windows-classic-sql-server-agent-extension.md)。

如需有關在 Azure VM 上執行 SQL Server 的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。

<!---HONumber=AcomDC_0720_2016-->