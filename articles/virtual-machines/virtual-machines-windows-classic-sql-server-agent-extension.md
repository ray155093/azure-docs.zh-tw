<properties
	pageTitle="適用於 SQL Server VM 的 SQL Server Agent 擴充功能 (傳統) | Microsoft Azure"
	description="本主題說明如何管理 SQL Server 代理程式擴充功能，其可將特定的 SQL Server 管理工作自動化。其中包括自動備份、自動修補和 Azure 金鑰保存庫整合。本主題使用傳統部署模式。"
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
	ms.date="05/16/2016"
	ms.author="jroth"/>

# 適用於 SQL Server VM 的 SQL Server Agent 擴充功能 (傳統)

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-server-agent-extension.md)
- [傳統](virtual-machines-windows-classic-sql-server-agent-extension.md)

Azure 虛擬機器會執行 SQL Server IaaS Agent 擴充功能 (SQLIaaSAgent) 以自動化系統管理工作。本主題概述擴充功能支援的服務以及安裝、狀態及移除相關指示。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 若要檢視這篇文章的 Resource Manager 版本，請參閱[適用於 SQL Server VM Resource Manager 的 SQL Server Agent 擴充功能](virtual-machines-windows-sql-server-agent-extension.md)。

## 支援的服務

SQL Server IaaS 代理程式擴充功能支援下列管理工作︰

| 系統管理功能 | 說明 |
|---------------------|-------------------------------|
| **SQL 自動備份** | 針對 VM 中 SQL Server 的預設執行個體，將所有資料庫的備份排程自動化。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動備份 (傳統)](virtual-machines-windows-classic-sql-automated-backup.md)。|
| **SQL 自動修補** | 設定維護期間 (在此期間會進行 VM 的更新)，以避免在工作負載尖峰時段進行更新。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補 (傳統)](virtual-machines-windows-classic-sql-automated-patching.md)。|
| **Azure 金鑰保存庫整合** | 讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。如需詳細資訊，請參閱[在 Azure VM 上設定 SQL Server 的 Azure 金鑰保存庫整合 (傳統)](virtual-machines-windows-classic-ps-sql-keyvault.md)。|

## 必要條件

在 VM 上使用 SQL Server IaaS 代理程式擴充功能的需求：

**作業系統**：

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 版本**：

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**：

- [下載及設定最新的 Azure PowerShell 命令](../powershell-install-configure.md)

**虛擬機器客體代理程式**：

- BGInfo 擴充會自動安裝在新的 Azure VM 上。

## 安裝

當您佈建其中一個 SQL Server 虛擬機器資源庫映像時，系統會自動安裝 SQL Server IaaS 代理程式擴充功能。

如果您建立 OS 專用的 Windows Server 虛擬機器，您可以使用 **Set-AzureVMSqlServerExtension** PowerShell Cmdlet 手動安裝擴充。使用命令來設定其中一個代理程式的服務，例如自動修補。VM 會安裝代理程式 (如果尚未安裝的話)。如需使用 **Set-AzureVMSqlServerExtension** PowerShell 的指示，請參閱本文[支援的服](#supported-services)務一節中的個別主題。

如果更新到最新版的 SQL IaaS 代理程式擴充，您必須在更新擴充之後重新啟動虛擬機器。

>[AZURE.NOTE] 如果您手動在 VM 上安裝 SQL Server IaaS 代理程式擴充，您必須使用和管理其使用 PowerShell 命令的功能。這個案例不提供入口網站介面。

## 狀態

驗證已安裝擴充功能的其中一項方法，是在 Azure 入口網站中檢視代理程式狀態。選取虛擬機器刀鋒視窗中的 [所有設定]，然後按一下 [擴充]。您應該會看到其中列出 **SQLIaaSAgent** 擴充。

![Azure 入口網站中的 SQL Server IaaS 代理程式擴充功能](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

您也可以使用 **Get-AzureVMSqlServerExtension** Azure PowerShell Cmdlet。

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## 移除   

在 Azure 入口網站中，您可以按一下虛擬機器內容 [擴充] 刀鋒視窗中的省略符號來解除安裝擴充。然後按一下 [刪除]。

![將 Azure 入口網站中的 SQL Server IaaS 代理程式擴充功能解除安裝](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

您也可以使用 **Remove-AzureVMSqlServerExtension** Powershell Cmdlet。

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## 後續步驟

開始使用擴充功能所支援的其中一項服務。如需詳細資訊，請參閱本文[支援的服務](#supported-services)一節中的參考主題。

如需在 Azure 虛擬機器上執行 SQL Server 的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。

<!---HONumber=AcomDC_0629_2016-->