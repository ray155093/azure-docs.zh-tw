---
title: "Azure 入口網站：管理長期資料庫備份保留 | Microsoft Docs"
description: "有關如何使用 Azure 入口網站在「Azure 復原服務」保存庫中設定、管理自動 Azure SQL Database 備份的長期保留及從該保留還原的快速參考"
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
ms.date: 12/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: fa4cf56c10f885a2fd4b62e08a369a51d1280ab3
ms.lasthandoff: 03/25/2017


---
# <a name="configure-manage-and-restore-from-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-the-azure-portal"></a>使用 Azure 入口網站在 Azure 復原服務保存庫中設定、管理資料庫備份的長期保留及從該保留還原

在本主題中，您將了解如何使用 Azure 入口網站，在「Azure 復原服務」保存庫中設定、管理自動備份的長期保留及從該保留還原。 您也可以使用 [PowerShell](sql-database-manage-long-term-backup-retention-powershell.md) 來執行此工作。

如需有關長期備份保留的詳細資訊，請參閱[長期備份保留](sql-database-long-term-retention.md)。

> [!TIP]
> 如需教學課程，請參閱[透過 Azure 入口網站開始使用備份與還原以保護和復原資料 (英文)](sql-database-get-started-backup-recovery-portal.md)。
>

## <a name="configure-long-term-retention-using-the-azure-portal"></a>使用 Azure 入口網站設定長期保留

1. 開啟您伺服器的 **SQL Server** 刀鋒視窗。

    ![SQL Server 刀鋒視窗](./media/sql-database-get-started/sql-server-blade.png)

2. 按一下 [長期備份保留]。

   ![長期備份保留連結](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. 在 [長期備份保留] 刀鋒視窗中，檢閱並接受預覽條款 (除非您已完成，或預覽中沒有這項功能)。

   ![接受預覽條款](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. 若要設定資料庫的長期備份保留時間，在方格中選取該資料庫，然後按一下工具列上的 [設定]。

   ![選取長期備份保留的資料庫](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. 在 [設定] 刀鋒視窗中，按一下 [復原服務保存庫] 下的 [設定所需設定]。

   ![設定保存庫連結](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. 在 [復原服務保存庫] 刀鋒視窗中，選取現有的保存庫 (若有)。 否則，如果找不到您訂用帳戶的復原服務保存庫，按一下以結束流程，並建立復原服務保存庫。

   ![建立新的保存庫連結](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. 在 [復原服務保存庫] 刀鋒視窗上，按一下 [新增]。

   ![新增保存庫連結](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. 在 [復原服務保存庫] 刀鋒視窗中，提供新復原服務保存庫的有效名稱。

   ![新增保存庫名稱](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. 選取您的訂用帳戶和資源群組，然後選取保存庫的位置。 完成時，按一下 [建立]。

   ![建立新的保存庫](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > 保存庫必須位於與 Azure SQL 邏輯伺服器相同的區域，而且必須使用相同的資源群組做為邏輯伺服器。
   >

10. 建立新的保存庫之後，執行必要的步驟以返回 [復原服務保存庫] 刀鋒視窗。

11. 在 [復原服務保存庫] 刀鋒視窗中，按一下保存庫，然後按一下 [選取]。

   ![選取現有的保存庫](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. 在 [設定] 刀鋒視窗中，提供新保留原則的有效名稱、適當修改預設保留原則，然後按一下 [確定]。

   ![定義保留原則](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. 在 [長期備份保留] 刀鋒視窗中，按一下 [儲存]，然後按一下 [確定]，將長期備份保留原則套用到所有選取的資料庫。

   ![定義保留原則](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. 按一下 [儲存] 以使用這個新的原則對您所設定的 Azure 復原服務保存庫啟用長期備份保留。

   ![定義保留原則](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)


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
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery-portal.md)

## <a name="next-steps"></a>後續步驟

- 若要使用 PowerShell 來管理長期備份保留中的備份，請參閱[使用 PowerShell 來管理長期備份保留 (英文)](sql-database-manage-long-term-backup-retention-powershell.md)
- 若要深入了解服務產生的自動備份，請參閱[自動備份](sql-database-automated-backups.md)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
- 若要深入了解從備份還原，請參閱[從備份還原](sql-database-recovery-using-backups.md)
