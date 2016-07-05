<properties 
    pageTitle="從異地備援備份 (PowerShell) 異地還原 Azure SQL Database | Microsoft Azure" 
    description="從異地備援備份將 Azure SQL Database 異地還原至新的伺服器" 
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

# 從異地備援備份使用 PowerShell 異地還原 Azure SQL Database


> [AZURE.SELECTOR]
- [概觀](sql-database-geo-restore.md)
- [Azure 入口網站](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

本文說明如何透過 PowerShell，使用異地還原來將資料庫還原至新的伺服器。

[AZURE.INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]

## 將資料庫異地還原至獨立資料庫

1. 取得您想要使用 [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) Cmdlet 還原之資料庫的異地備援備份。

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 開始使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) Cmdlet 從異地備援備份還原。
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## 將資料庫異地還原至彈性資料庫集區

1. 取得您想要使用 [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) Cmdlet 還原之資料庫的異地備援備份。

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 開始使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) Cmdlet 從異地備援備份還原。指定您想要還原資料庫的目標集區名稱。
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## 後續步驟

- 如需如何使用 Azure 入口網站，從異地備援備份還原 Azure SQL Database 的詳細步驟，請參閱[使用 Azure 入口網站進行異地還原](sql-database-geo-restore-portal.md)
- 如需從異地備援備份還原 Azure SQL Database 的詳細資訊，請參閱[使用 PowerShell 進行異地還原](sql-database-geo-restore.md)
- 如需如何從中斷復原的完整討論，請參閱[從中斷復原](sql-database-disaster-recovery.md)


## 其他資源

- [商務持續性案例](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->