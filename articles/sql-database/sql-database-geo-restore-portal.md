---
title: "Azure 入口網站：從異地備援備份異地還原 SQL Database | Microsoft Docs"
description: "使用 Azure 入口網站從異地備援備份將 Azure SQL Database 還原至新的伺服器"
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
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 7d9314444c39cda3f9d893e4f97b4afce4c75777
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-the-azure-portal"></a>使用 Azure 入口網站從異地備援備份還原 Azure SQL Database

本文說明如何透過 Azure 入口網站，使用異地還原將資料庫還原至新的伺服器。 此工作也可以[使用 PowerShell](sql-database-geo-restore-powershell.md) 來完成。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>使用 Azure 入口網站從異地備援備份還原 Azure SQL Database

若要在 Azure 入口網站中異地還原資料庫，請執行下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 在畫面的左側，選取 [+新增] > [資料庫] > [SQL Database]：
   
   ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 選取 [備份] 作為來源，然後選取您想要復原的備份。 指定資料庫名稱、您想要將資料庫還原至其中的伺服器，然後按一下 [建立]︰
   
   ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/geo-restore.png)

4. 按一下頁面右上方的通知圖示來監視還原作業的狀態。


## <a name="next-steps"></a>後續步驟
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)。
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)。  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)。


