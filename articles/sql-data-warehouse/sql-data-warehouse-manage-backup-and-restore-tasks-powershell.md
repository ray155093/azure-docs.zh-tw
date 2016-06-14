<properties
   pageTitle="Azure SQL 資料倉儲中的備份與還原 (PowerShell) | Microsoft Azure"
   description="還原 Azure SQL 資料倉儲中的即時、已刪除或無法存取之資料庫的 PowerShell 工作。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# 備份與還原 Azure SQL 資料倉儲中的資料庫 (PowerShell)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [入口網站](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

如何使用 PowerShell 備份與還原 SQL 資料倉儲中的資料庫。

本主題中的工作：

- 還原即時資料庫
- 還原已刪除的資料庫
- 從不同的 Azure 地理區域還原無法存取的資料庫

[AZURE.INCLUDE [SQL 資料倉儲備份保留原則](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## 開始之前

### 請驗證您的 SQL Database DTU 容量。 
由於 SQL 資料倉儲會還原至位於您邏輯 SQL Server 上的新資料庫，請務必確保您還原至的 SQL Server 擁有足夠的 DTU 容量可供新資料庫使用。請參閱此部落格文章以深入了解[如何檢視和增加 DTU 配額][]。

### 安裝 PowerShell

若要搭配使用 Azure Powershell 與 SQL 資料倉儲，您需要安裝 Azure PowerShell 1.0 版或更高版本。您可以執行 **Get-Module -Name Azure -ListAvailable** 來檢查您的版本。可透過 [Microsoft Web Platform Installer][] 安裝最新的版本。如需安裝最新版本的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][]。

## 還原即時資料庫

若要從快照還原資料庫，請使用 [Restore-AzureRmSqlDatabase][] Cmdlet。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之資料庫的訂用帳戶。
4. 列出資料庫的還原點。
5. 使用 RestorePointCreationDate 挑選出想要的還原點。
6. 將資料庫還原到想要的還原點。
7. 確認還原的資料庫在線上。

```PowerShell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] 針對伺服器 foo.database.windows.net，請使用 "foo" 作為上述 PowerShell Cmdlet 中的 -ServerName。

還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。

## 還原已刪除的資料庫

若要還原已刪除的資料庫，請使用 [Restore-AzureRmSqlDatabase][] Cmdlet。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之已刪除資料庫的訂用帳戶。
4. 取得已刪除的特定資料庫。
5. 還原已刪除的資料庫。
6. 確認還原的資料庫在線上。

```PowerShell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] 針對伺服器 foo.database.windows.net，請使用 "foo" 作為上述 PowerShell Cmdlet 中的 -ServerName。

還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。

## 從異地備援備份異地還原資料倉儲

若要復原資料庫，請使用 [Restore-AzureRmSqlDatabase][] Cmdlet。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之資料庫的訂用帳戶。
4. 取得您想要復原的資料庫。
5. 建立資料庫的復原要求。
6. 確認異地還原資料庫的狀態。

```PowerShell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

### 在執行異地還原後設定您的資料庫
這份檢查清單可以用來幫助您準備產生復原的資料庫。

1. **更新連接字串**：確認您用戶端工具的連接字串指向剛復原的資料庫。
2. **修改防火牆規則**：確認目標伺服器上的防火牆規則，並確定已啟用您用戶端電腦或 Azure 與伺服器以及剛復原之資料庫的連線。
3. **確認伺服器登入和資料庫使用者**：確認應用程式使用的所有登入是否都存在於主控您已復原之資料庫的伺服器上。重新建立缺少的登入，並將適當的已復原資料庫之權限授與登入。 
4. **啟用稽核**：如果需要稽核才能存取您的資料庫，則您必須在資料庫復原之後啟用稽核。

如果來源資料庫是啟用 TDE，則復原的資料庫將是啟用 TDE。


## 後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: sql-database-business-continuity.md
[完成復原的資料庫]: sql-database-recovered-finalize.md
[如何安裝和設定 Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[如何檢視和增加 DTU 配額]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0601_2016-->