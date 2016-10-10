<properties
	pageTitle="適用於 SQL Server VM 的 SQL Server Agent 擴充功能 (傳統) | Microsoft Azure"
	description="本主題說明如何管理 SQL Server 代理程式擴充功能，此擴充功能可將特定 SQL Server 管理工作自動化。其中包括自動備份、自動修補和 Azure 金鑰保存庫整合。本主題使用傳統部署模式。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/26/2016"
	ms.author="jroth"/>

# 適用於 SQL Server VM 的 SQL Server Agent 擴充功能 (傳統)

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-server-agent-extension.md)
- [傳統](virtual-machines-windows-classic-sql-server-agent-extension.md)

Azure 虛擬機器會執行 SQL Server IaaS Agent 擴充功能 (SQLIaaSAgent) 以自動化系統管理工作。本主題概述擴充功能所支援的服務，以及與安裝、狀態及移除相關的指示。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]  
若要檢視這篇文章的 Resource Manager 版本，請參閱[適用於 SQL Server VM Resource Manager 的 SQL Server Agent 擴充功能](virtual-machines-windows-sql-server-agent-extension.md)。

## 支援的服務

SQL Server IaaS 代理程式擴充功能支援下列管理工作︰

| 系統管理功能 | 說明 |
|---------------------|-------------------------------|
| **SQL 自動備份** | 針對 VM 中 SQL Server 的預設執行個體，將所有資料庫的備份排程自動化。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動備份 (傳統)](virtual-machines-windows-classic-sql-automated-backup.md)。|
| **SQL 自動修補** | 設定維護期間 (在此期間會進行 VM 的更新)，以避免在工作負載尖峰時段進行更新。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補 (傳統)](virtual-machines-windows-classic-sql-automated-patching.md)。|
| **Azure 金鑰保存庫整合** | 讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。如需詳細資訊，請參閱[在 Azure VM 上設定 SQL Server 的 Azure 金鑰保存庫整合 (傳統)](virtual-machines-windows-classic-ps-sql-keyvault.md)。|

## 必要條件

在 VM 上使用 SQL Server IaaS 代理程式擴充功能的需求：

### 作業系統：

- Windows Server 2012
- Windows Server 2012 R2

### SQL Server 版本：

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### Azure PowerShell：

[下載及設定最新的 Azure PowerShell 命令](../powershell-install-configure.md)。

啟動 Windows PowerShell，然後使用 **Add-AzureAccount** 命令將它與您的 Azure 訂用帳戶連線。

	Add-AzureAccount

如果您有多個訂用帳戶，請使用 **Select-AzureSubscription** 以選取包含您目標傳統 VM 的訂用帳戶。

	Select-AzureSubscription -SubscriptionName <subscriptionname>

此時，您可以使用 **Get-AzureVM** 命令來取得傳統虛擬機器及其相關服務名稱的清單。

	Get-AzureVM

## 安裝

針對傳統 VM，您必須使用 PowerShell 來安裝「SQL Server IaaS 代理程式擴充功能」並設定其相關服務。請使用 **Set-AzureVMSqlServerExtension** PowerShell Cmdlet 來安裝擴充功能。例如，下列命令會在 Windows Server VM (傳統) 上安裝擴充功能，並將它命名為 "SQLIaaSExtension"。

	Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

如果更新到最新版的 SQL IaaS 代理程式擴充，您必須在更新擴充之後重新啟動虛擬機器。

>[AZURE.NOTE] 傳統虛擬機器沒有可透過入口網站安裝及設定「SQL IaaS 代理程式擴充功能」的選項。

## 狀態

驗證已安裝擴充功能的其中一項方法，是在 Azure 入口網站中檢視代理程式狀態。請選取虛擬機器刀鋒視窗中的 [所有設定]，然後按一下 [擴充功能]。您應該會看到其中列出 **SQLIaaSAgent** 擴充功能。

![Azure 入口網站中的 SQL Server IaaS 代理程式擴充功能](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

您也可以使用 **Get-AzureVMSqlServerExtension** Azure Powershell Cmdlet。

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## 移除   

在「Azure 入口網站」中，您可以按一下虛擬機器屬性之 [擴充功能] 刀鋒視窗上的省略符號，來將擴充功能解除安裝。然後按一下 [刪除]。

![將 Azure 入口網站中的 SQL Server IaaS 代理程式擴充功能解除安裝](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

您也可以使用 **Remove-AzureVMSqlServerExtension** Powershell Cmdlet。

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## 後續步驟

開始使用擴充功能所支援的其中一項服務。如需詳細資訊，請參閱本文[支援的服務](#supported-services)一節中參考的主題。

如需在 Azure 虛擬機器上執行 SQL Server 的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。

<!---HONumber=AcomDC_0928_2016-->