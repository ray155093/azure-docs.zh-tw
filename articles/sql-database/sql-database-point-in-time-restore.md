---
title: "將 Azure SQL Database 還原到先前的時間點 | Microsoft Docs"
description: "將 Azure SQL Database 還原至先前的時間點"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>將 Azure SQL Database 還原至先前的時間點 

本方法文章將說明如何從 [SQL Database 自動備份](sql-database-automated-backups.md)將資料庫還原至先前的時間點。 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>使用 Azure 入口網站還原到先前的時間點

> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery.md)
>

在 Azure 入口網站中選取要還原的資料庫：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在畫面的左側，選取 [更多服務] > [SQL Database]。
3. 按一下您想要還原的資料庫。
4. 在資料庫頁面的頂端，選取 [還原]：
   
   ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/restore.png)
5. 在 [還原] 頁面上，選取要將資料庫還原到哪個日期和時間 (採用 UTC 時間)，然後按一下 [確定]：
   
   ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. 在上一個步驟中按一下 [確定] 之後，按一下頁面右上方的通知圖示，然後按一下 [正在還原 SQL 資料庫] 通知以查看詳細資料。
   
    ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. [正在還原 SQL 資料庫] 頁面會開啟，內含有關還原狀態的資訊。 您可以按一下明細項目來查看詳細資料：
   
    ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>使用 PowerShell 還原到先前的時間點

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>將資料庫還原至某個時間點做為單一資料庫
1. 使用 [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) Cmdlet 取得您想要還原的資料庫。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) Cmdlet 將資料庫還原至某個時間點。
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>將資料庫還原至某個時間點形成彈性集區
1. 使用 [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) Cmdlet 取得您想要還原的資料庫。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) Cmdlet 將資料庫還原至某個時間點。
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>後續步驟
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要從服務產生的資料庫備份檢視最早的還原點，請參閱[檢視最早的還原點](sql-database-view-oldest-restore-point.md)
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


