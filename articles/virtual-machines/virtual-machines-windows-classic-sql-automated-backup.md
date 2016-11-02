<properties
    pageTitle="SQL Server 虛擬機器的自動備份 (傳統) | Microsoft Azure"
    description="說明在使用 Resource Manager 的 Azure 虛擬機器中執行的 SQL Server 自動備份功能。 "
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
    ms.date="09/26/2016"
    ms.author="jroth" />


# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-(classic)"></a>Azure 虛擬機器中的 SQL Server 自動備份 (傳統)

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-automated-backup.md)
- [傳統](virtual-machines-windows-classic-sql-automated-backup.md)

自動備份會針對執行 SQL Server 2014 Standard 或 Enterprise 之 Azure VM 上所有現存和新的資料庫，自動設定 [Managed Backup 到 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) 。 這可讓您設定採用持久性 Azure Blob 儲存體的一般資料庫備份。 自動備份相依於 [SQL Server IaaS 代理程式擴充](virtual-machines-windows-classic-sql-server-agent-extension.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 若要檢視這篇文章的 Resource Manager 版本，請參閱 [Automated Backup for SQL Server in Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)(Azure 虛擬機器的 SQL Server 自動備份 (Resource Manager))。

## <a name="prerequisites"></a>必要條件

若要使用自動備份，請考慮下列必要條件︰

**作業系統**：

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 版本**：

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

>[AZURE.NOTE] 尚未支援 SQL Server 2016 進行「自動備份」。

**資料庫組態**：

- 目標資料庫必須使用完整復原模型。

**Azure PowerShell**：

- [安裝最新的 Azure PowerShell 命令](../powershell-install-configure.md)。

**SQL Server IaaS 擴充功能**：

- [安裝 SQL Server IaaS 擴充功能](virtual-machines-windows-classic-sql-server-agent-extension.md)。

## <a name="settings"></a>Settings

下表說明可以為自動備份設定的選項。 針對傳統 VM，您必須使用 PowerShell 來設定這些設定。

|設定|範圍 (預設值)|說明|
|---|---|---|
|**自動備份**|啟用/停用 (已停用)|針對執行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM，啟用或停用自動備份。|
|**保留期限**|1-30 天 (30 天)|保留備份的天數。|
|**儲存體帳戶**|Azure 儲存體帳戶 (針對指定 VM 建立的儲存體帳戶)|將自動備份檔案儲存在 Blob 儲存體中時，所使用的 Azure 儲存體帳戶。 這個位置會建立一個容器來儲存所有備份檔案。 備份檔案命名慣例包括日期、時間和電腦名稱。|
|**加密**|啟用/停用 (已停用)|啟用或停用加密。 啟用加密時，用來還原備份的憑證會放在與使用相同命名慣例之相同 automaticbackup 容器中的指定儲存體帳戶裡。 如果密碼變更，就會以該密碼產生新的憑證，但是舊的憑證還是會保留，以還原先前的備份。|
|**密碼**|密碼文字 (無)|加密金鑰的密碼。 唯有啟用加密時，才需要此密碼。 若要還原加密的備份，您必須要有建立備份時所使用的正確密碼和相關憑證。|

## <a name="configuration-with-powershell"></a>使用 PowerShell 進行設定

在下列 PowerShell 範例中，是針對現有的 SQL Server 2014 VM 設定自動備份。 **New-AzureVMSqlServerAutoBackupConfig** 命令會設定自動備份設定，以在 $storageaccount 變數所指定的 Azure 儲存體帳戶中儲存備份。 這些備份將會保留 10 天。 **Set-AzureVMSqlServerExtension** 命令會使用這些設定更新指定的 Azure VM。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

可能需要幾分鐘的時間來安裝及設定 SQL Server IaaS 代理程式。

若要啟用加密，請修改先前的指令碼，為 CertificatePassword 參數傳遞 EnableEncryption 參數和密碼 (安全字串)。 下列指令碼會啟用上述範例中的自動備份設定，並新增加密。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

若要停用自動備份，請執行相同的指令碼，但不要對 **New-AzureVMSqlServerAutoBackupConfig** 使用 **-Enable** 參數。 和安裝一樣，可能需要幾分鐘的時間來停用自動備份。

>[AZURE.NOTE] 停用及解除安裝 SQL Server IaaS 代理程式不會移除先前設定的受管理備份設定。 在停用或解除安裝 SQL Server IaaS 代理程式之前，應先停用自動備份。

## <a name="next-steps"></a>後續步驟

自動備份會在 Azure VM 上設定受管理備份。 因此，請務必 [檢閱受管理備份的文件](https://msdn.microsoft.com/library/dn449496.aspx) ，以了解其行為和隱含意義。

您可以在下列主題中找到 Azure VM 上 SQL Server 的其他備份和還原指引： [Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-windows-sql-backup-recovery.md)。

如需有關其他可用之自動化工作的資訊，請參閱 [SQL Server IaaS 代理程式擴充功能](virtual-machines-windows-classic-sql-server-agent-extension.md)。

如需有關在 Azure VM 上執行 SQL Server 的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。



<!--HONumber=Oct16_HO2-->


