---
title: "還原已刪除的 Azure SQL Database (Azure 入口網站) | Microsoft Docs"
description: "還原已刪除的 Azure SQL Database (Azure 入口網站)。"
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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5a3f40ff8811c08d130c2a3e0a7d61aed9457d1


---
# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>還原使用 Azure 入口網站的已刪除 Azure SQL Database
> [!div class="op_single_selector"]
> * [概觀](sql-database-recovery-using-backups.md)
> * [**還原已刪除的 DB：入口網站**](sql-database-restore-deleted-database-portal.md)
> * [還原已刪除的 DB：PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## <a name="select-the-database-to-restore"></a>選取要還原的資料庫
在 Azure 入口網站還原已刪除的資料庫：

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [更多服務]  >  [SQL 伺服器]。
2. 選取包含您想還原之資料庫的伺服器。
3. 向下捲動至伺服器刀鋒視窗的 [作業] 區段，然後選取 [已刪除的資料庫]：[還原 Azure SQL 資料庫]![](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. 選取您想要還原的資料庫。
5. 指定資料庫名稱，按一下 [確定]：
   
   ![還原 Azure SQL Database](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="next-steps"></a>後續步驟
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


