---
title: "在 Azure 復原服務保存庫中設定自動備份的長期保留 | Microsoft Docs"
description: "如何在 Azure 復原服務保存庫中設定自動備份的長期保留的快速參考"
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
ms.openlocfilehash: b395fc5c1deaf04205dcfccacd13d30ec90dd1e7


---
# <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>在 Azure 復原服務保存庫中設定自動備份的長期保留 

在此「作法」主題中，您將會深入了解如何在 Azure 復原服務保存庫中設定自動備份的長期保留。

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

14. 在啟用長期備份保留之後，開啟 [復原服務保存庫] 刀鋒視窗 (移至 [所有資源]，並從您訂用帳戶的資源清單中選取)。

   ![檢視復原服務保存庫](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)


> [!TIP]
> 如需教學課程，請參閱[開始使用備份與還原以保護和復原資料](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>後續步驟

- 若要從長期保留的備份復原資料庫，請參閱[從長期保留的備份復原](sql-database-restore-from-long-term-retention.md)
- 若要檢視 Azure 復原服務保存庫中的備份，請參閱[檢視長期保留中的備份](sql-database-view-backups-in-vault.md)
- 若要深入了解服務產生的自動備份，請參閱[自動備份](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
- 若要深入了解從備份還原，請參閱[從備份還原](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


