<properties
   pageTitle="Azure SQL 資料倉儲中的資料庫還原 (PowerShell) | Microsoft Azure"
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
   ms.date="06/01/2016"
   ms.author="elfish;barbkess;sonyama;kevin"/>

# 備份與還原 Azure SQL 資料倉儲中的資料庫 (PowerShell)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-manage-database-restore.md)
- [入口網站](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

在 SQL 資料倉儲中還原資料庫的 PowerShell 工作。

本主題中的工作：

- 還原即時資料庫
- 還原已刪除的資料庫

[AZURE.INCLUDE [SQL 資料倉儲備份保留原則](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## 開始之前

### 請驗證您的 SQL Database DTU 容量。 
由於 SQL 資料倉儲會還原至位於您邏輯 SQL Server 上的新資料庫，請務必確保您還原至的 SQL Server 擁有足夠的 DTU 容量可供新資料庫使用。如需有關[如何檢視和增加 DTU 配額][]的詳細資訊，請參閱此部落格文章。

### 安裝 PowerShell

若要搭配使用 Azure Powershell 與 SQL 資料倉儲，您需要安裝 Azure PowerShell 1.0 版或更高版本。您可以執行 **Get-Module -ListAvailable -Name Azure** 來檢查您的版本。透過 [Microsoft Web Platform Installer][] 即可安裝最新版本。如需有關安裝最新版本的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][]。

## 還原即時資料庫

若要從快照還原資料庫，請使用 [Restore-AzureRmSqlDatabase][] PowerShell Cmdlet。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之資料庫的訂用帳戶。
4. 列出資料庫的還原點。
5. 使用 RestorePointCreationDate 挑選出想要的還原點。
6. 將資料庫還原到想要的還原點。
7. 確認還原的資料庫在線上。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

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

還原完成後，您可以依照[完成復原的資料庫][]指南來設定復原的資料庫。

## 還原已刪除的資料庫

若要還原已刪除的資料庫，請使用 [Restore-AzureRmSqlDatabase][] Cmdlet。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之已刪除資料庫的訂用帳戶。
4. 取得已刪除的特定資料庫。
5. 還原已刪除的資料庫。
6. 確認還原的資料庫在線上。

```Powershell

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

還原完成後，您可以依照[完成復原的資料庫][]指南來設定復原的資料庫。

## 後續步驟
如需詳細資訊，請參閱 [Azure SQL Database 商務持續性概觀][]和[管理概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: sql-database-business-continuity.md
[完成復原的資料庫]: sql-database-recovered-finalize.md
[如何安裝和設定 Azure PowerShell]: powershell-install-configure.md
[管理概觀]: sql-data-warehouse-overview-manage.md

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

<!---HONumber=AcomDC_0608_2016-->