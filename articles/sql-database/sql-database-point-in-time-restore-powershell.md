<properties 
    pageTitle="將 Azure SQL Database 還原到先前的時間點 (PowerShell) | Microsoft Azure" 
    description="將 Azure SQL Database 還原至先前的時間點" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/09/2016"
    ms.author="sstein"/>

# 使用 PowerShell 將 Azure SQL Database 還原至先前的時間點

本文將說明如何使用 PowerShell，從 [SQL Database 自動備份](sql-database-automated-backups.md)將資料庫還原至先前的時間點。

[AZURE.INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]

## 將資料庫還原至某個時間點做為獨立資料庫

1. 使用 [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) Cmdlet 取得您想要還原的資料庫。

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) Cmdlet 將資料庫還原至某個時間點。
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## 將資料庫還原至某個時間點形成彈性資料庫集區
   
1. 使用 [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) Cmdlet 取得您想要還原的資料庫。

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) Cmdlet 將資料庫還原至某個時間點。
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## 後續步驟

- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [時間點還原](sql-database-point-in-time-restore.md)
- [使用 Azure 入口網站進行時間點還原](sql-database-point-in-time-restore-portal.md)
- [使用 REST API 進行時間點還原](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動備份](sql-database-automated-backups.md)

## 其他資源

- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->