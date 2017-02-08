---
title: "檢視 Azure 復原服務保存庫中的備份 | Microsoft Docs"
description: "如何檢視 Azure 復原服務保存庫中的備份以及這些備份所用空間的快速參考"
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
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>檢視長期備份保留中資料庫備份的相關資訊

在此「作法」主題中，您可了解如何檢視長期備份保留中資料庫備份的相關資訊。

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>使用 Azure 入口網站檢視長期備份保留資訊 

1. 開啟 Azure 復原服務保存庫的刀鋒視窗 (移至 [所有資源]，並從您訂用帳戶的資源清單中選取) 來檢視您保存庫中的資料庫備份所使用之儲存體數量。

   ![使用備份檢視復原服務保存庫](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. 開啟您資料庫的 [SQL Database] 刀鋒視窗。

    ![新範例資料庫刀鋒視窗](./media/sql-database-get-started/new-sample-db-blade.png)

3. 在工具列上，按一下 [還原]。

    ![還原工具列](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. 在 [還原] 刀鋒視窗中，按一下 [長期]。

5. 在 Azure 保存庫備份下，按一下 [選取備份]，在長期備份保留中檢視可用的資料庫備份。

    ![保存庫中的備份](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>後續步驟

- 若要在 Azure 復原服務保存庫中設定自動備份的長期保留，請參閱[](sql-database-configure-long-term-retention.md)
- 若要從長期備份保留中的備份還原資料庫，請參閱[從長期保留中還原](sql-database-restore-from-long-term-retention.md)
- 若要深入了解服務產生的自動備份，請參閱[自動備份](sql-database-automated-backups.md)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
- 若要深入了解從備份還原，請參閱[從備份還原](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


