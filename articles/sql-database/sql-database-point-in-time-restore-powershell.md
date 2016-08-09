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
    ms.workload="NA" 
    ms.date="07/17/2016"
    ms.author="sstein"/>

# 使用 PowerShell 將 Azure SQL Database 還原至先前的時間點

> [AZURE.SELECTOR]
- [概觀](sql-database-recovery-using-backups.md)
- [還原時間點：Azure 入口網站](sql-database-point-in-time-restore-portal.md)

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

- 如需商務持續性概觀和案例，請參閱[商務持續性概觀](sql-database-business-continuity.md)
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
- 若要了解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
- 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-geo-replication-overview.md)
- 若要了解如何使用自動備份進行封存，請參閱[資料庫複製](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->