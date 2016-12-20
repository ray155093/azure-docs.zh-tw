---
title: "從自動備份還原 Azure SQL Database (Azure 入口網站) | Microsoft Docs"
description: "從自動備份還原 Azure SQL Database (Azure 入口網站)。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 62d94085-d7e7-4f08-bb83-404cf866a6c1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85f8f2473e372a72c66153cd4131ba46e1c8c741


---
# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>使用 Azure 入口網站從自動備份還原 Azure SQL Database
> [!div class="op_single_selector"]
> * [概觀](sql-database-recovery-using-backups.md#geo-restore)
> * [異地還原：PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

本文說明如何使用 Azure 入口網站，透過[異地還原](sql-database-recovery-using-backups.md#geo-restore)將資料庫從[自動備份](sql-database-automated-backups.md)還原至新的伺服器。

## <a name="select-a-database-to-restore"></a>選取要還原的資料庫
若要在 Azure 入口網站中還原資料庫，請執行下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 在畫面的左側，選取 [+新增] > [資料庫] > [SQL Database]：
   
   ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 選取 [備份] 作為來源，然後選取您想要復原的備份。 指定資料庫名稱、您想要將資料庫還原至其中的伺服器，然後按一下 [建立]︰
   
   ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/geo-restore.png)

按一下頁面右上方的通知圖示來監視還原作業的狀態。

## <a name="next-steps"></a>後續步驟
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


