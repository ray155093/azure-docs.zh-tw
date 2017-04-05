---
title: "儲存多達 10 年的 Azure SQL 資料庫備份 | Microsoft Docs"
description: "了解 Azure SQL Database 如何支援多達 10 年的儲存備份。"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 768a630e1652a48fa4478ec2c25173d536ea6c09
ms.lasthandoff: 03/23/2017


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>儲存多達 10 年的 Azure SQL 資料庫備份
許多應用程式具有法規、相容性或其他商務用途，需要您保留 SQL Database [自動備份](sql-database-automated-backups.md)所提供超過 7-35 天的自動完整資料庫備份。 **長期備份保留**功能可讓您將 Azure SQL Database 備份儲存在 Azure 復原服務保存庫中多達 10 年。 每個保存庫最多可以儲存 1000 個資料庫。 您可以選取資料庫中的任何備份以將其還原為新的保存庫。

> [!IMPORTANT]
> 長期備份保留期目前為預覽版本，可在下列區域使用︰澳大利亞東部、澳大利亞東南部、巴西南部、美國中部、東亞、美國東部、美國東部 2、印度中部、印度南部、日本東部、日本西部、美國中北部、北歐、美國中南部、東南亞、西歐和美國西部。
>

> [!NOTE]
> 您可以在 24 小時期間在每個保存庫啟用多達 200 個資料庫。 因此，我們建議您對每一部伺服器使用個別的保存庫，以將這項限制的影響降到最低。 
> 
> 
## <a name="how-does-sql-database-long-term-backup-retention-work"></a>SQL Database 長期備份保留如何運作？

備份的長期備份保留可讓您將 Azure SQL Database 伺服器與「Azure 復原服務」保存庫建立關聯。 

* 必須在與建立 SQL Server 相同的 Azure 訂用帳戶中以及相同的地理區域和資源群組中建立保存庫。 
* 接著，您可以針對任何資料庫設定保留原則。 原則會使每週的完整資料庫備份複製到復原服務保存庫，並保留指定的保留週期 (最多 10 年)。 
* 接著您可以從任何備份還原至訂用帳戶中任何伺服器的新資料庫。 複本會由 Azure 儲存體從現有的備份執行，且在現有的資料庫上沒有效能影響。

> [!TIP]
> 如需相關教學課程，請參閱[透過 Azure 入口網站開始使用備份與還原以保護和修復資料](sql-database-get-started-backup-recovery-portal.md)，或[透過 PowerShell 開始使用備份與還原以保護和修復資料](sql-database-get-started-backup-recovery-powershell.md)

## <a name="how-do-i-enable-long-term-backup-retention"></a>如何啟用長期備份保留？

若要設定資料庫的長期備份保留︰

1. 在相同的區域、訂用帳戶與資源群組中建立 Azure 復原服務保存庫做為 SQL Database 伺服器。 
2. 向保存庫註冊伺服器
3. 建立 Azure 復原服務保護原則
4. 將保護原則套用到需要長期備份保留的資料庫

若要使用 Azure 入口網站在「Azure 復原服務」保存庫中設定、管理自動備份的長期備份保留及從該保留還原，請參閱[使用 Azure 入口網站來管理長期備份保留 (英文)](sql-database-manage-long-term-backup-retention-portal.md)。 若要使用 PowerShell 在「Azure 復原服務」保存庫中設定、管理自動備份的長期備份保留及從該保留還原，請參閱[使用 PowerShell 來管理長期備份保留 (英文)](sql-database-manage-long-term-backup-retention-powershell.md)。

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-backup-retention-feature"></a>如何還原以長期備份保留功能儲存的資料庫？

從長期備份保留備份復原︰

1. 列出儲存備份的保存庫
2. 列出對應至您邏輯伺服器的容器
3. 列出對應至資料庫之保存庫內的資料來源
4. 列出可用來還原的復原點
5. 從復原點還原到您訂用帳戶內的目標伺服器

若要使用 Azure 入口網站在「Azure 復原服務」保存庫中設定、管理自動備份的長期備份保留及從該保留還原，請參閱[使用 Azure 入口網站來管理長期備份保留 (英文)](sql-database-manage-long-term-backup-retention-portal.md)。 若要使用 PowerShell 在「Azure 復原服務」保存庫中設定、管理自動備份的長期備份保留及從該保留還原，請參閱[使用 PowerShell 來管理長期備份保留 (英文)](sql-database-manage-long-term-backup-retention-powershell.md)。

## <a name="how-much-does-long-term-backup-retention-cost"></a>長期備份保留費用是多少？

Azure SQL Database 的長期備份保留是根據 [Azure 備份服務定價費率](https://azure.microsoft.com/pricing/details/backup/)收費。

Azure SQL Database 伺服器註冊至保存庫之後，您需支付儲存在保存庫中之每週備份所使用的總儲存體。

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>檢視以長期備份保留期儲存的可用備份

若要使用 Azure 入口網站在「Azure 復原服務」保存庫中設定、管理自動備份的長期備份保留及從該保留還原，請參閱[使用 Azure 入口網站來管理長期備份保留 (英文)](sql-database-manage-long-term-backup-retention-portal.md)。 若要使用 PowerShell 在「Azure 復原服務」保存庫中設定、管理自動備份的長期備份保留及從該保留還原，請參閱[使用 PowerShell 來管理長期備份保留 (英文)](sql-database-manage-long-term-backup-retention-powershell.md)。

## <a name="disabling-long-term-retention"></a>停用長期保留

復原服務會根據提供的保留原則自動處理備份的清除。 

* 若要停止將特定資料庫的備份傳送到保存庫，請移除該資料庫的保留原則。
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> 保存庫中現有的備份不會受到影響。 當保留期限到期時，復原服務會將它們自動刪除。


## <a name="removing-long-term-backup-retention-backups-from-the-azure-recovery-services-vault"></a>從 Azure 復原服務保存庫移除長期備份保留備份

若要從保存庫移除長期備份保留備份，請參閱[刪除長期保留備份 (英文)](sql-database-manage-long-term-backup-retention-powershell.md)

## <a name="long-term-backup-retention-faq"></a>長期備份保留常見問題集：

1. 問︰可以手動刪除保存庫中的特定備份嗎？

    答︰目前不可以，當保留期間已過期時，保存庫會自動清除備份。
2. 問︰是否可以註冊我的伺服器來將備份儲存至多個保存庫？

    答︰否，目前您只可以一次儲存備份到一個保存庫。
3. 問︰我可以在不同的訂用帳戶中有保存庫與伺服器嗎？

    答︰否，目前保存庫與伺服器必須位於相同的訂用帳戶和資源群組。
4. 問︰是否可以使用我在我的伺服器區域以外的不同區域中所建立的保存庫？

    答︰否，保存庫與伺服器必須位於相同的區域，以將複製時間降至最低，並避免產生流量費用。
5. 問︰我可以在一個保存庫中儲存幾個資料庫？

    答︰目前每個保存庫最多僅支援 1000 個資料庫。 
6. 問： 每個訂用帳戶可以建立幾個保存庫？

    A. 每個訂用帳戶最多可以建立 25 個保存庫。
7. 問： 每個保存庫每天可以設定幾個資料庫？

    A. 每個保存庫每天只能設定 200 個資料庫。
8. 問︰長期備份保留是否可與彈性集區搭配運作？

    答： 會。 集區中的任何資料庫可以使用保留原則設定。
9. 問︰是否可以選擇建立備份的時間？

    答︰否，SQL Database 會控制備份排程，以便將資料庫上的效能影響降到最低。
10. 問︰我針對資料庫啟用了 TDE。 可以搭配使用 TDE 與保存庫嗎？ 

    A. 是，可支援 TDE。 即使原始資料庫不存在，您也可以從保存庫還原資料庫。
11. 問： 如果我的訂用帳戶已暫止，則保存庫中的備份會發生什麼事？ 

    A. 如果您的訂用帳戶已暫停，我們會保留現有的資料庫和備份。 但不會將新的備份複製到保存庫。 在您重新啟動訂用帳戶之後，服務會繼續將備份複製到保存庫。 您的保存庫會在訂用帳戶暫止之前使用已在那裡複製的備份，使保存庫成為可供還原作業存取。 
12. 問︰是否可存取 SQL Database 備份檔案，以便可下載 / 還原至 SQL Server？

    答：否，目前不可以。
13. 問︰SQL 保留原則內是否可以有多個排程 (每日、每週、每月、每年)。

    答︰否，目前這只適用於虛擬機器備份。
14. 問： 如果我們在作為作用中異地複寫次要複本的資料庫上設定長期備份保留，會發生什麼情況？

    答：目前我們並不在複本上進行備份，因此沒有在次要複本上進行長期備份保留的選項。 不過，對客戶來說，在作用中異地複寫次要複本設定長期備份保留相當重要，原因如下：
    - 當容錯移轉發生而資料庫變成主要複本時，我們將會進行完整備份並將此完整備份上傳到保存庫。
    - 客戶在次要複本上設定長期備份保留並不需要額外付費。



## <a name="next-steps"></a>後續步驟
資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入了解其他 Azure SQL Database 業務持續性解決方案，請參閱[業務持續性概觀](sql-database-business-continuity.md)。


