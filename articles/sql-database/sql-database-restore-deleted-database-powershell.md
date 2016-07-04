<properties
	pageTitle="還原已刪除的 Azure SQL Database (PowerShell) | Microsoft Azure"
	description="還原已刪除的 Azure SQL Database (PowerShell)。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 PowerShell 還原已刪除的 Azure SQL Database

> [AZURE.SELECTOR]
- [概觀](sql-database-restore-deleted-database.md)
- [Azure 入口網站](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]


## 將刪除的資料庫還原至獨立資料庫

1. 取得您想要使用 [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) Cmdlet 還原的已刪除資料庫備份。

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 開始使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) Cmdlet 從已刪除的資料庫備份還原。
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## 將已刪除的資料庫還原至彈性資料庫集區

1. 取得您想要使用 [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) Cmdlet 還原的已刪除資料庫備份。

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 開始使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) Cmdlet 從已刪除的資料庫備份還原。
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## 後續步驟

- 如需如何使用 Azure 入口網站還原已刪除資料庫的詳細步驟，請參閱[使用 Azure 入口網站還原已刪除的資料庫](sql-database-restore-deleted-database-portal.md)。
- 如需如何還原已刪除資料庫的相關資訊，請參閱[使用 REST API 還原刪除的資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)。
- 如需還原已刪除資料庫的詳細資訊，請參閱[還原已刪除的資料庫](sql-database-restore-deleted-database.md)。
- 如需 Azure SQL Database 自動備份的詳細資訊，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。

## 其他資源

- [還原時間點](sql-database-point-in-time-restore.md)
- [商務持續性概觀](sql-database-business-continuity.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->