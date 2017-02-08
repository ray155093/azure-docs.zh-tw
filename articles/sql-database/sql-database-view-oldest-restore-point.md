---
title: "從服務產生的 Azure SQL Database 備份檢視最早的還原點 | Microsoft Docs"
description: "如何從服務產生的資料庫備份檢視最早還原點的快速參考"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: ae35dd15c386bbbf4e77a143d245efcd1f44f238


---
# <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-an-azure-sql-database"></a>從服務產生的 Azure SQL Database 備份檢視最早的還原點

在此「作法」主題中，您可了解如何從服務產生的 Azure SQL Database 備份檢視最早的還原點。

## <a name="view-the-oldest-restore-point-using-the-azure-portal"></a>使用 Azure 入口網站檢視最早的還原點

1. 開啟您資料庫的 [SQL Database] 刀鋒視窗。

    ![新範例資料庫刀鋒視窗](./media/sql-database-get-started/new-sample-db-blade.png)

2. 在工具列上，按一下 [還原]。

    ![還原工具列](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. 在 [還原] 刀鋒視窗中，檢閱最早的還原點。

    ![最早的還原點](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>後續步驟

- 若要深入了解服務產生的自動備份，請參閱[自動備份](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
- 若要深入了解從備份還原，請參閱[從備份還原](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


