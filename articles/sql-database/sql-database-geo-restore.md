---
title: "從異地備援備份異地還原 Azure SQL Database | Microsoft Docs"
description: "使用 Azure 入口網站或 PowerShell，從異地備援備份將 Azure SQL Database 異地還原至新的伺服器"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6104936089ac9a1a6bbc08a345105e5fa4ae8be7
ms.openlocfilehash: 0ab7090ba7b37b3447da95a3d577cfe82ea8003e


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>從異地備援備份還原 Azure SQL Database

本文說明如何使用異地還原將資料庫還原至新的伺服器。 做法是透過 Azure 入口網站或使用 PowerShell。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>使用 Azure 入口網站從異地備援備份還原 Azure SQL Database

若要在 Azure 入口網站中異地還原資料庫，請執行下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 在畫面的左側，選取 [+新增] > [資料庫] > [SQL Database]：
   
   ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 選取 [備份] 作為來源，然後選取您想要復原的備份。 指定資料庫名稱、您想要將資料庫還原至其中的伺服器，然後按一下 [建立]︰
   
   ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/geo-restore.png)

4. 按一下頁面右上方的通知圖示來監視還原作業的狀態。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>使用 PowerShell 從異地備援備份還原 Azure SQL Database

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>將資料庫異地還原至獨立資料庫
1. 使用 [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx) Cmdlet 來取得您想要還原的資料庫異地備援備份。
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) Cmdlet 來從異地備援備份開始還原。
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>將資料庫異地還原至彈性集區
1. 使用 [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx) Cmdlet 來取得您想要還原的資料庫異地備援備份。
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) Cmdlet 來從異地備援備份開始還原。 指定您想要還原資料庫的目標集區名稱。
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>後續步驟
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)。
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)。  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)。




<!--HONumber=Dec16_HO3-->


