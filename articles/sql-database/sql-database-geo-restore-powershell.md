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
    ms.workload="data-management" 
    ms.date="05/10/2016"
    ms.author="sstein"/>

# 從異地備援備份使用 PowerShell 異地還原 Azure SQL Database

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

本文說明如何使用 PowerShell，將資料庫還原至使用異地還原的新伺服器。

[異地還原](sql-database-geo-restore.md)可從異地備援備份還原資料庫，進而建立新的資料庫。您可以在任何 Azure 區域中的任何伺服器上建立資料庫。異地還原使用異地備援備份做為其來源，因此即使因為中斷而無法存取資料庫，也能用來復原資料庫。異地還原會對所有服務層自動啟用，且不須額外付費。

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

- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)


## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->