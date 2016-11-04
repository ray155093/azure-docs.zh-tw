---
title: 將 Azure SQL Database 還原到先前的時間點 (Azure 入口網站) | Microsoft Docs
description: 將 Azure SQL Database 還原至先前的時間點。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/17/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# 使用 Azure 入口網站將 Azure SQL Database 還原至先前的時間點
> [!div class="op_single_selector"]
> * [概觀](sql-database-recovery-using-backups.md)
> * [還原時間點：PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

本文將說明如何使用 Azure 入口網站，從 [SQL Database 自動備份](sql-database-automated-backups.md)將資料庫還原至先前的時間點。

## 選取要還原至先前時間點的資料庫
若要在 Azure 入口網站中還原資料庫，請執行下列動作：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在畫面左側選取 [瀏覽] > [SQL Database]。
3. 瀏覽至您想要還原的資料庫並加以選取。
4. 在資料庫刀鋒視窗的頂端，選取 [還原]：
   
   ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/restore.png)
5. 指定資料庫名稱、時間點，然後按一下 [確定]：
   
   ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## 後續步驟
* 如需商務持續性概觀和案例，請參閱[商務持續性概觀](sql-database-business-continuity.md)
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-geo-replication-overview.md)
* 若要了解如何使用自動備份進行封存，請參閱[資料庫複製](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->