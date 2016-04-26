<properties
	pageTitle="SQL Server IaaS 代理程式擴充功能 (傳統) | Microsoft Azure"
	description="本主題說明 SQL Server 代理程式擴充功能，其可讓在 Azure 上執行 SQL Server 的 VM 使用自動化功能。它會使用傳統部署模式。"
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
	ms.date="04/08/2016"
	ms.author="jroth"/>

# SQL Server IaaS 代理程式擴充功能 (傳統)

這個延伸模組可讓 Azure 虛擬機器中的 SQL Server 使用這篇文章中列出的某些服務，這些服務只有在安裝此延伸模組時才能使用。此延伸模組會為 Azure 入口網站中的 SQL Server 資源庫映像自動安裝。它可以安裝在 Azure 中的任何 SQL Server VM，其已安裝 Azure VM 客體代理程式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


## 必要條件
使用 Powershell cmdlet 的需求：

- [這裡提供](../powershell-install-configure.md)最新 Azure PowerShell

在您的 VM 上使用延伸模組的需求：

- Azure VM 客體代理程式
- Windows Server 2012、Windows Server 2012 R2 或更新版本
- SQL Server 2012、SQL Server 2014 或更新版本

## 延伸模組可用的服務

- **SQL 自動化備份**：這項服務會針對 VM 中 SQL Server 的預設執行個體，自動化所有資料庫的備份排程。如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的自動化備份 (傳統)](virtual-machines-windows-classic-sql-automated-backup.md)。
- **SQL 自動化修補**：這項服務可讓您設定維護視窗 (在此期間會進行 VM 的更新)，您就可以在工作負載的尖峰時段避免更新。如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的自動化修補 (傳統)](virtual-machines-windows-classic-sql-automated-patching.md)。
- **Azure 金鑰保存庫整合**：這項服務可讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。如需詳細資訊，請參閱[在 Azure VM 上設定 SQL Server 的 Azure 金鑰保存庫整合 (傳統)](virtual-machines-windows-classic-ps-sql-keyvault.md)。

## 利用 Powershell 新增延伸模組
如果您透過 [Azure 入口網站](virtual-machines-windows-portal-sql-server-provision.md)佈建 SQL Server VM，將會自動安裝延伸模組。對於透過 Azure 傳統入口網站佈建的 SQL Server VM，或對於您授與自己 SQL 授權的目標 VM，您可以使用 **Set-AzureVMSqlServerExtension** Azure PowerShell Cmdlet 新增這個擴充功能。

### 語法

Set-AzureVMSqlServerExtension [[-ReferenceName] [String]] [-VM] IPersistentVM [[-Version] [String]] [[-AutoPatchingSettings] [AutoPatchingSettings]] [-AutoBackupSettings[AutoBackupSettings]] [-Profile [AzureProfile]] [CommonParameters]

> [AZURE.NOTE] 建議省略 –Version 參數。若沒有該參數，預設值為最新版本的延伸模組。

### 範例
下列範例使用 $abs (這裡未顯示) 中所定義的組態來設定自動備份設定。serviceName 是主控虛擬機器的雲端服務名稱。如需完整範例，請參閱 [Azure 虛擬機器中 SQL Server 的自動化備份 (傳統)](virtual-machines-windows-classic-sql-automated-backup.md)。

	Get-AzureVM –ServiceName "serviceName" –Name "vmName" | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## 檢查延伸模組的狀態
如果您想要檢查這個延伸模組以及與其相關聯之服務的狀態，您可以使用任一入口網站。在您現有 VM 的詳細資料中，找出**設定**下的**延伸模組**。

您也可以使用 **Get-AzureVMSqlServerExtension** Azure PowerShell Cmdlet。

### 語法

Get-AzureVMSqlServerExtension [[-VM] [IPersistentVM]] [-Profile [AzureProfile]] [CommonParameters]

### 範例
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## 利用 Powershell 移除延伸模組   
如果您想要從 VM 中移除此擴充功能，則可以使用 **Remove-AzureVMSqlServerExtension** Azure PowerShell Cmdlet。

### 語法

Remove-AzureVMSqlServerExtension [-Profile [AzureProfile]] -VM IPersistentVM [CommonParameters]

<!---HONumber=AcomDC_0413_2016-->