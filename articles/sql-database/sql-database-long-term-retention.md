---
title: "儲存多達 10 年的 Azure SQL 資料庫備份 | Microsoft Docs"
description: "了解 Azure SQL Database 如何支援多達 10 年的儲存備份。"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: carlrab; sashan
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 8b13faf1f6cdac355cc4d22b825cc2362a50e8f9


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>儲存多達 10 年的 Azure SQL 資料庫備份
許多應用程式具有法規、相容性或其他商務用途，需要您保留 SQL Database [自動備份](sql-database-automated-backups.md)所提供超過 7-35 天的自動完整資料庫備份。

**長期備份保留**功能可讓您將 Azure SQL Database 備份儲存在 Azure 復原服務保存庫中多達 10 年。 每個保存庫最多可以儲存 1000 個資料庫。 您可以選取資料庫中的任何備份以將其還原為新的保存庫。

> [!NOTE]
> 您可以在 24 小時期間在每個保存庫啟用多達 200 個資料庫。 因此，我們建議您對每一部伺服器使用個別的保存庫，以將這項限制的影響降到最低。 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>SQL Database 長期保留如何運作？

備份的長期保留可讓您將 Azure SQL Database 伺服器與 Azure 復原服務保存庫相關聯。 

* 必須在與建立 SQL Server 相同的 Azure 訂用帳戶中以及相同的地理區域和資源群組中建立保存庫。 
* 接著，您可以針對任何資料庫設定保留原則。 原則會使每週的完整資料庫備份複製到復原服務保存庫，並保留指定的保留週期 (最多 10 年)。 
* 接著您可以從任何備份還原至訂用帳戶中任何伺服器的新資料庫。 複本會由 Azure 儲存體從現有的備份執行，且在現有的資料庫上沒有效能影響。

## <a name="how-do-i-enable-long-term-retention"></a>如何啟用長期保留？

若要設定資料庫的長期備份保留︰

1. 在相同的區域、訂用帳戶與資源群組中建立 Azure 復原服務保存庫做為 SQL Database 伺服器。 
2. 向保存庫註冊伺服器
3. 建立 Azure 復原服務保護原則
4. 將保護原則套用到需要長期備份保留的資料庫

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>如何使用長期保存功能儲存的還原資料庫？

若要從長期保留備份復原︰

1. 列出儲存備份的保存庫
2. 列出對應至您邏輯伺服器的容器
3. 列出對應至資料庫之保存庫內的資料來源
4. 列出可用來還原的復原點
5. 從復原點還原到您訂用帳戶內的目標伺服器

## <a name="how-much-does-long-term-retention-cost"></a>長期保留費用是多少？

Azure SQL Database 的長期保留會根據 [Azure 備份服務價格費率](https://azure.microsoft.com/pricing/details/backup/)收費。

Azure SQL Database 伺服器註冊至保存庫之後，您需支付儲存在保存庫中之每週備份所使用的總儲存體。

## <a name="configuring-long-term-retention-in-the-azure-portal"></a>在 Azure 入口網站中設定長期保留

在 Azure SQL Database 伺服器刀鋒視窗中，您可以設定長期保留，以及視需要建立 Azure 復原服務保存庫。

- 若要在 Azure 復原服務保存庫中設定自動備份的長期保留，請參閱[設定長期備份保留](sql-database-configure-long-term-retention.md)
- 若要從長期保留的備份復原資料庫，請參閱[從長期保留的備份復原](sql-database-restore-from-long-term-retention.md)
- 若要檢視 Azure 復原服務保存庫中的備份，請參閱[檢視長期保留中的備份](sql-database-view-backups-in-vault.md)

> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery.md)
>

## <a name="configuring-long-term-retention-using-powershell"></a>使用 PowerShell 設定長期保留

您可以使用 PowerShell，使用下列步驟來設定長期保留。
1. 建立復原服務保存庫
   
   ```
   New-AzureRmResourceGroup -Name $ResourceGroupName –Location 'WestUS' 
   $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName $ResourceGroupName -Location 'WestUS' 
   Set-AzureRmRecoveryServicesBackupProperties   -BackupStorageRedundancy LocallyRedundant  -Vault $vault
   ```
2. 將 Azure SQL Database 伺服器註冊至保存庫，讓伺服器內的資料庫可以長期儲存備份。
   
   ```
   Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName 'RG1' -ServerName 'Server1' –ResourceId $vault.Id
   ```
3. 建立儲存備份的保留原則。
   
   ```
   #retrieve the default in-memory policy object for AzureSQLServer workload and set the retention period
   $RP1 = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase
   #Sets the retention value to two years
   $RP1.RetentionDurationType='Years'
   $RP1.RetentionCount=2
   #register the policy for use with any SQL database
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name 'SQLBackup1' –WorkloadType AzureSQLDatabase -retentionPolicy $RP1
   ```
4. 針對您要在保存庫中儲存備份的 SQL Database 啟用長期保存。
   
   ```
   #for your database you can select any policy created in the vault with which your server is registered
   Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'enabled' -ResourceId $policy.Id
   ```
5. 列出與保存庫相關聯的伺服器。 每一部伺服器都與保存庫中的特定容器相關聯。 您可以執行下列命令來列出已註冊的伺服器：
   
   ```
   #each server has an associated container in the vault
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL   
   #each database has an associated backup item in the respective container
   Get-AzureRmRecoveryServicesBackupItem –container $container
   ```
6. 列出容器中具有保留原則的資料庫。 每個資料庫在個別容器中都有相關聯的備份項目。 備份項目名稱衍生自資料庫名稱。
   
    ```
    #list the backup items in the container
    Get-AzureRmRecoveryServicesBackupItem –container $container
    ```

## <a name="restore-from-a-long-term-retention-backup"></a>從長期保留備份還原

若要從 Azure 復原服務保存庫中的備份還原資料庫，請使用下列步驟︰

1. 尋找與 SQL Server 相關聯的復原服務容器。
   
   ```
   #the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -Name 'Sql;myresourcegroup;myserver'
   ```
2. 尋找與資料庫相關聯的備份項目。
   
    ``` 
    #the following command finds the backup item associated with the database 'mydb'
    $item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name 'mydb' 
    ```
3. 尋找您想要還原的來源備份。
   
   ```
   #The following command lists the backups (also known as the “recovery points”) created in the specific time period.
   $RP=Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item –StartDate '2016-02-01' -EndDate '2016-02-20'
   ```
4. 從復原點還原至新的 Azure SQL Database。
   
   ```
   #This command restores from a selected backup. If there are multiple recovery points in the specified range $RP[0] refers to the first one.
   Restore-AzureRMSqlDatabase –FromLongTermRetentionBackup –ResourceId $RP[0].ID TargetResourceGroupName 'RG2' -TargetServerName 'Server2' -TargetDatabaseName 'DB2' [-Edition <String>] [-ServiceObjectiveName <String>] [-ElasticPoolName <String>] [<CommonParameters>]
   ```

## <a name="disabling-long-term-retention"></a>停用長期保留

復原服務會根據提供的保留原則自動處理備份的清除。 

* 若要停止將特定資料庫的備份傳送到保存庫，請移除該資料庫的保留原則。
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> 保存庫中現有的備份不會受到影響。 當保留期限到期時，復原服務會將它們自動刪除。
> 
> 

## <a name="removing-backups-from-the-azure-recovery-services-vault"></a>從 Azure 復原服務保存庫移除備份

若要手動從保存庫中移除備份。

1. 識別 'myserver' 保存庫中的容器
   
    ```
    Set-AzureRMRecoveryServicesVaultContext -Vault $vault 
    $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName 'myserver'
    ```
2. 識別要刪除的備份項目。
   
    ``` 
    $item=Get-AzureRmRecoveryServicesBackupItem –container $container -Name 'mydb'
    ```
3. 刪除備份項目 (所有資料庫 'mydb' 的備份)
   
    ```
    $job = Disable-AzureRmRecoveryServicesBackupProtection –item $item -Removerecoverypoints 
    Wait-AzureRmRecoveryServicesBackupJob $job
    ```
4. 刪除與 'myserver' 相關聯的容器
   
    ```
    Unregister-AzureRmRecoveryServicesBackupContainer –Container $container $container
    ```

## <a name="long-term-retention-faq"></a>長期保留常見問題集︰

1. 問︰可以手動刪除保存庫中的特定備份嗎？
   答︰目前不可以，當保留期間已過期時，保存庫會自動清除備份。
2. 問︰是否可以註冊我的伺服器來將備份儲存至多個保存庫？
   答︰否，目前您只可以一次儲存備份到一個保存庫。
3. 問︰我可以在不同的訂用帳戶中有保存庫與伺服器嗎？
   答︰否，目前保存庫與伺服器必須位於相同的訂用帳戶和資源群組。
4. 問︰是否可以使用我在我的伺服器區域以外的不同區域中所建立的保存庫？
   答︰否，保存庫與伺服器必須位於相同的區域，以將複製時間降至最低，並避免產生流量費用。
5. 問︰我可以在一個保存庫中儲存幾個資料庫？
   答︰目前我們僅支援每個保存庫最多 1000 個資料庫。 
6. 問： 每個訂用帳戶可以建立幾個保存庫？答︰每個訂用帳戶您可以建立最多 25 個保存庫。
7. 問： 每個保存庫每天可以設定幾個資料庫？答︰您每天每個保存庫只可以設定 200 個資料庫。
8. 問︰長期保存是否可與彈性集區搭配使用？
   答： 會。 集區中的任何資料庫可以使用保留原則設定。
9. 問︰是否可以選擇建立備份的時間？
   答︰否，SQL Database 會控制備份排程，以便將資料庫上的效能影響降到最低。
10. 問︰我針對資料庫啟用了 TDE。 可以搭配使用 TDE 與保存庫嗎？答︰是，可支援 TDE。 即使原始資料庫不存在，您也可以從保存庫還原資料庫。
11. 問： 如果我的訂用帳戶已暫止，則保存庫中的備份會發生什麼事？答︰如果您的訂用帳戶已暫停，我們會保留現有的資料庫和備份，但新的備份不會複製到保存庫。 在您重新啟動訂用帳戶之後，服務會繼續將備份複製到保存庫。 您的保存庫會在訂用帳戶暫止之前使用已在那裡複製的備份，使保存庫成為可供還原作業存取。 
12. 問︰是否可存取 SQL Database 備份檔案，以便可下載 / 還原至 SQL Server？
   答：否，目前不可以。
13. 問︰SQL 保留原則內是否可以有多個排程 (每日、每週、每月、每年)。
   答︰否，目前這只適用於虛擬機器備份。

## <a name="next-steps"></a>後續步驟
資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入了解其他 Azure SQL Database 業務持續性解決方案，請參閱[業務持續性概觀](sql-database-business-continuity.md)。




<!--HONumber=Dec16_HO2-->


