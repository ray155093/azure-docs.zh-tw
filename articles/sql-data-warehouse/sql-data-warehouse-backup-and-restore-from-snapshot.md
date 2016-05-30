<properties
   pageTitle="從 SQL 資料倉儲中的使用者錯誤復原資料庫 | Microsoft Azure"
   description="從 SQL 資料倉儲中的使用者錯誤復原資料庫的步驟"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/30/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# 從 SQL 資料倉儲中的使用者錯誤復原資料庫

SQL 資料倉儲提供兩個核心功能，可從造成非預期之資料損毀或刪除的使用者錯誤復原。

- 還原即時資料庫
- 還原已刪除的資料庫

這兩個功能會在相同的伺服器上還原至新的資料庫。請務必確定您要還原到的伺服器有足夠的 DTU 容量供新的資料庫使用。請參閱此部落格文章以深入了解[如何檢視和增加 DTU 配額][]。

## 復原即時資料庫
Azure SQL 資料倉儲服務至少每 8 小時會以資料庫快照集來保護所有即時資料庫，並保留 7 天，為您提供一組獨立的還原點。當您暫停或卸除資料庫時，也會建立資料庫快照集，並將其保留 7 天。當使用者錯誤造成非預期的資料修改時，您可以在保留期限內將資料庫還原到任何還原點。

### Azure 入口網站

若要使用 Azure 入口網站進行還原，請使用下列步驟。

1. 登入 [Azure 入口網站][]。
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Database]。
3. 瀏覽至您的資料庫，然後選取它。
4. 在資料庫刀鋒視窗頂端，按一下 [還原]。
5. 指定新的 [資料庫名稱]，並選取 [還原點]，然後按一下 [建立]。
6. 資料庫還原程序就會開始，且您可以使用 [通知] 監視程序。

### PowerShell

使用 Azure PowerShell 以程式設計方式搭配 [Restore-AzureRmSqlDatabase][] Cmdlet 執行資料庫還原。

> [AZURE.NOTE]  若要搭配使用 Azure Powershell 與 SQL 資料倉儲，您需要安裝 Azure PowerShell 1.0.3 版或更高版本。您可以執行 **Get-Module -ListAvailable -Name Azure** 來檢查您的版本。可透過 [Microsoft Web Platform Installer][] 安裝最新的版本。如需安裝最新版本的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][]。

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

### REST API
使用 REST 可以程式設計方式執行資料庫還原。

1. 使用 Get 資料庫還原點作業取得資料庫還原點清單。
2. 使用[建立資料庫還原要求][]作業來開始還原。
3. 使用[資料庫作業狀態][]作業來追蹤還原狀態。

>[AZURE.NOTE] 還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。

## 復原已刪除的資料庫
Azure SQL 資料倉儲會在卸除資料庫前採用資料庫快照集，並將其保留 7 天。若意外刪除資料庫，您可以將已刪除的資料庫還原至刪除的時間點。

### Azure 入口網站

若要使用 Azure 入口網站來還原已刪除的資料庫，請按照下列步驟執行。

1. 登入 [Azure 入口網站][]。
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Server]。
3. 瀏覽至您的伺服器，然後選取它。
4. 在伺服器的刀鋒視窗上向下捲動至 [作業]，然後按一下 [已刪除的資料庫] 圖格。
5. 按一下您想要還原的已刪除資料庫。
5. 指定新的 [資料庫名稱]，然後按一下 [建立]。
6. 資料庫還原程序就會開始，且您可以使用 [通知] 監視程序。


### PowerShell
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

### REST API
使用 REST 可以程式設計方式執行資料庫還原。

1.	使用[列出可還原的已卸除資料庫][]作業來列出所有可還原的已刪除資料庫。
2.	使用[取得可還原的已卸除資料庫][]作業來取得您想要還原之已刪除資料庫的詳細資料。
3.	使用[建立資料庫還原要求][]作業來開始還原。
4.	使用[資料庫作業狀態][]作業來追蹤還原狀態。

>[AZURE.NOTE] 還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。

## 後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: ../sql-database/sql-database-business-continuity.md
[完成復原的資料庫]: ../sql-database/sql-database-recovered-finalize.md
[如何安裝和設定 Azure PowerShell]: ../powershell/powershell-install-configure.md

<!--MSDN references-->
[建立資料庫還原要求]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[資料庫作業狀態]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[取得可還原的已卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[列出可還原的已卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[如何檢視和增加 DTU 配額]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure 入口網站]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->