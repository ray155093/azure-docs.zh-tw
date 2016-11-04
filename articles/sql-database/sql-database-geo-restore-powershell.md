---
title: 從異地備援備份 (PowerShell) 異地還原 Azure SQL Database | Microsoft Docs
description: 從異地備援備份將 Azure SQL Database 異地還原至新的伺服器
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/17/2016
ms.author: sstein

---
# 使用 PowerShell 從異地備援備份還原 Azure SQL Database
> [!div class="op_single_selector"]
> * [概觀](sql-database-recovery-using-backups.md)
> * [異地還原：Azure 入口網站](sql-database-geo-restore-portal.md)
> 
> 

本文說明如何使用異地還原將資料庫還原至新的伺服器。這可以透過 PowerShell 完成。

[!INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]

## 將資料庫異地還原至獨立資料庫
1. 使用 [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) Cmdlet 來取得您想要還原的資料庫異地備援備份。
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) Cmdlet 來從異地備援備份開始還原。
   
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

## 將資料庫異地還原至彈性資料庫集區
1. 使用 [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) Cmdlet 來取得您想要還原的資料庫異地備援備份。
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) Cmdlet 來從異地備援備份開始還原。指定您想要還原資料庫的目標集區名稱。
   
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## 後續步驟
* 如需商務持續性概觀和案例，請參閱[商務持續性概觀](sql-database-business-continuity.md)。
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
* 若要了解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
* 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-geo-replication-overview.md)。
* 若要了解如何使用自動備份進行封存，請參閱[資料庫複製](sql-database-copy.md)。

<!---HONumber=AcomDC_0803_2016-->