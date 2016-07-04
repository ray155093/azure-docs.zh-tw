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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# 使用 PowerShell 將 Azure SQL Database 還原至先前的時間點

> [AZURE.SELECTOR]
- [概觀](sql-database-point-in-time-restore.md)
- [Azure 入口網站](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

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

- 如需使用 Azure 入口網站復原至某個時間點的詳細步驟，請參閱[使用 Azure 入口網站還原時間點](sql-database-point-in-time-restore-portal.md)。
- 如需如何使用 REST API 復原至某個時間點的相關資訊，請參閱[建立或更新資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)。
- 如需還原時間點的概觀，請參閱[還原時間點](sql-database-point-in-time-restore.md)。
- 如需如何從使用者或應用程式錯誤中復原的完整討論，請參閱[從使用者錯誤復原](sql-database-user-error-recovery.md)。

## 其他資源

- [商務持續性案例](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->