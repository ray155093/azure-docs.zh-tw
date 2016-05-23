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
    ms.date="05/10/2016"
    ms.author="sstein"/>

# 使用 PowerShell 將 Azure SQL Database 還原至先前的時間點

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

本文將說明如何使用 PowerShell 將資料庫還原至先前的時間點。

[**還原時間點**](sql-database-point-in-time-restore.md)是自助功能，可讓您將資料庫從我們擷取自所有資料庫的自動備份還原至資料庫保留期間內的任何時間點。若要深入了解自動備份和資料庫保留期限，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

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
- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)


## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->