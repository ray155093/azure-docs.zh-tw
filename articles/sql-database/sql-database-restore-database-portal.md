---
title: "Azure 入口網站：還原 Azure SQL Database | Microsoft Docs"
description: "還原 Azure SQL Database (Azure 入口網站)。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>使用 Azure 入口網站還原 Azure SQL Database

下列步驟示範如何從已刪除的資料庫和從異地備援備份將 Azure SQL Database 還原至某個時間點。

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>將 Azure SQL Database 還原至先前的時間點 

> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery-portal.md)
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


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>從備份還原已刪除的 Azure SQL Database
在 Azure 入口網站還原已刪除的資料庫：

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [更多服務]  >  [SQL 伺服器]。
2. 選取包含您想還原之資料庫的伺服器。
3. 向下捲動至伺服器刀鋒視窗的 [作業] 區段，然後選取 [已刪除的資料庫]：[還原 Azure SQL 資料庫]![](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. 選取您想要還原的資料庫。
5. 指定資料庫名稱，按一下 [確定]：
   
   ![還原 Azure SQL Database](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>從異地備援備份還原 Azure SQL Database

若要在 Azure 入口網站中異地還原資料庫，請執行下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 在畫面的左側，選取 [+新增] > [資料庫] > [SQL Database]：
   
   ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 選取 [備份] 作為來源，然後選取您想要復原的備份。 指定資料庫名稱、您想要將資料庫還原至其中的伺服器，然後按一下 [建立]︰
   
   ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/geo-restore.png)

4. 按一下頁面右上方的通知圖示來監視還原作業的狀態。

## <a name="next-steps"></a>後續步驟
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)


