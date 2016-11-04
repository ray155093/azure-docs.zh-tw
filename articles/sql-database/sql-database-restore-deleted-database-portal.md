---
title: 還原已刪除的 Azure SQL Database (Azure 入口網站) | Microsoft Docs
description: 還原已刪除的 Azure SQL Database (Azure 入口網站)。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/09/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# 還原使用 Azure 入口網站的已刪除 Azure SQL Database
> [!div class="op_single_selector"]
> * [概觀](sql-database-recovery-using-backups.md)
> * [還原已刪除的資料庫︰PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## 選取要還原的資料庫
若要在 Azure 入口網站中還原資料庫，請執行下列動作：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在畫面左側選取 [瀏覽] > [SQL Server]。
3. 瀏覽至包含已刪除資料庫的伺服器，您想要還原與選取伺服器
4. 向下捲動至伺服器刀鋒視窗的 [作業] 區段，然後選取 [已刪除的資料庫]：![還原 Azure SQL Database](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5. 按一下您想要還原的已刪除資料庫。
6. 指定資料庫名稱，然後按一下 [確定]：
   
   ![還原 Azure SQL Database](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## 後續步驟
* 如需商務持續性概觀和案例，請參閱[商務持續性概觀](sql-database-business-continuity.md)
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-geo-replication-overview.md)
* 若要了解如何使用自動備份進行封存，請參閱[資料庫複製](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->