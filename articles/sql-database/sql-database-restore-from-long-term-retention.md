---
title: "從 Azure 復原服務保存庫中的備份還原資料庫 | Microsoft Docs"
description: "如何針對 Azure 復原服務保存庫中的備份還原資料庫，以及還原這些備份所用空間的快速參考"
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
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>從長期備份保留備份中還原資料庫

在此「作法」主題中，您可了解如何從長期備份保留中的備份還原資料庫。

## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>使用 Azure 入口網站從長期備份保留還原

1. 在 [Azure 保存庫備份] 刀鋒視窗中，按一下要還原的備份，然後按一下 [選取]。

    ![選取保存庫中的備份](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. 在 [資料庫名稱] 文字方塊中，提供還原的資料庫名稱。

    ![新的資料庫名稱](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. 按一下 [確定]，從保存庫中的備份將資料庫還原到新的資料庫。

4. 在工具列上，按一下 [通知] 圖示以檢視還原作業的狀態。

    ![從保存庫還原作業進度](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 完成還原作業之後，開啟 [SQL Database] 刀鋒視窗，以檢視剛還原的資料庫。

    ![從保存庫還原的資料庫](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>後續步驟

- 若要在 Azure 復原服務保存庫中設定自動備份的長期保留，請參閱[設定長期備份保留](sql-database-configure-long-term-retention.md)
- 若要檢視 Azure 復原服務保存庫中的備份，請參閱[檢視長期保留中的備份](sql-database-view-backups-in-vault.md)
- 若要深入了解服務產生的自動備份，請參閱[自動備份](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
- 若要深入了解從備份還原，請參閱[從備份還原](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


