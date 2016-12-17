---
title: "將 Azure SQL Database 還原到先前的時間點 (Azure 入口網站) | Microsoft Docs"
description: "將 Azure SQL Database 還原到先前的時間點。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>使用 Azure 入口網站將 Azure SQL Database 還原到先前的時間點
> [!div class="op_single_selector"]
> * [概觀](sql-database-recovery-using-backups.md)
> * [還原時間點：PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

本文說明如何使用 Azure 入口網站，從 [SQL Database 自動備份](sql-database-automated-backups.md)將資料庫還原到先前的時間點。

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>將 SQL 資料庫還原至先前的時間點
在 Azure 入口網站中選取要還原的資料庫：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在畫面的左側，選取 [更多服務] > [SQL Database]。
3. 按一下您想要還原的資料庫。
4. 在資料庫頁面的頂端，選取 [還原]：
   
   ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/restore.png)
5. 在 [還原] 頁面上，選取要將資料庫還原到哪個日期和時間 (採用 UTC 時間)，然後按一下 [確定]：
   
   ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>監視還原作業
1. 在上一個步驟中按一下 [確定] 之後，按一下頁面右上方的通知圖示，然後按一下 [正在還原 SQL 資料庫] 通知以查看詳細資料。
   
    ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. [正在還原 SQL 資料庫] 頁面會開啟，內含有關還原狀態的資訊。 您可以按一下明細項目來查看詳細資料：
   
    ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>後續步驟
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


