<properties
   pageTitle="雲端商務持續性 - 還原時間點 - SQL Database | Microsoft Azure"
   description="了解還原時間點，其可讓您復原 Azure SQL Database 到先前的時間 (最多 35 天)。"
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/17/2016"
   ms.author="sstein"/>

# 概觀︰SQL Database 還原時間點

> [AZURE.SELECTOR]
- [商務持續性概觀](sql-datbase-business-continuity.md)
- [還原時間點](sql-database-point-in-time-restore.md)
- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)
- [異地還原](sql-database-geo-restore.md)
- [主動式異地複寫](sql-database-geo-replication-overview.md)
- [商務持續性案例](sql-database-business-continuity-scenarios.md)

還原時間點可讓您使用 [SQL Database 自動備份](sql-database-automated-backups.md)，將現有資料庫當成新的資料庫還原到同一部邏輯伺服器上較早的時間點。您無法覆寫現有的資料庫。您可以使用 [Azure 入口網站](sql-database-point-in-time-restore-portal.md)、[PowerShell](sql-database-point-in-time-restore-powershell.md) 或 [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) 還原到較早的時間點。

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

資料庫可以還原到任何效能層級或彈性集區。您必須確定在伺服器或集區上有足夠的 DTU 配額，或者記住還原會建立新的資料庫，以及還原資料庫的服務層和效能層級可能會與實際資料庫的目前狀態不同。完成之後，還原的資料庫通常是可在線上完全存取的資料庫，根據其服務層和效能層級依正常費率計費。

若要找出可用的最早還原點，請使用[取得資料庫](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*) 以取得最早的還原點 (非異地複寫的還原點)。

如果您為了要復原而還原資料庫，可以將還原的資料庫視為原始資料庫的替代品，或用它來從原始資料庫擷取資料並將其更新。如果還原資料庫用來做為原始資料庫的替代品，您應該確認效能層級及/或服務層適當，並在必要時調整資料庫大小。您可以重新命名原始資料庫，然後使用 T-SQL 中的 ALTER DATABASE 命令提供原始名稱給還原的資料庫。

如果您打算從還原資料庫擷取資料，必須分別撰寫及執行您需要的任何資料復原指令碼。雖然還原作業可能要花很長的時間才能完成，但是資料庫將會在整個資料庫清單中顯示。如果您在還原期間刪除資料庫，這樣會取消作業，而且不會向您收費。

## 還原時間點的復原時間

還原資料庫所花費的時間取決於許多因素，包括資料庫大小、交易記錄數目、選取的時間點，以及需要重新執行來重新建構所選時間點狀態的活動數目。針對非常大型及/或作用中的資料庫，還原可能需要數小時。大多數資料庫的還原會在 12 小時內完成。

## 備份/還原以及複製/匯出/匯入

還原時間點是在發生意外資料遺失或損毀時用來復原基本、標準和進階資料庫的建議方法。備份和還原成本較低 (除非超量，否則備份無需任何費用，但是要確保交易一致匯出並儲存 BACPAC 檔案所需的資料庫複製就要向您收費)、免管理 (備份會自動執行，但是您必須自行管理或排程匯出)，而且具有較佳的 RPO (您可以還原至特定時間點，其資料粒度 (一分鐘) 比實際使用複製/匯出/匯入更細微) 和更優秀的復原時間 (從備份還原的速度通常比匯入還快，後者包含建立結構描述、停用索引、載入資料，然後個別啟用每個資料表的索引等步驟)。請注意，對於超過保留期限的長期封存，複製和匯出依然是建議的解決方案。


## 摘要

自動備份和還原時間點可保護您的資料庫免於發生資料意外損毀或刪除。此零成本免管理解決方案可供所有 SQL 資料庫使用。備份與還原針對短期復原需求，提供複製/匯出/匯入解決方案的顯著改進。我們鼓勵您使用還原時間點做為商務持續性策略的一部分，並且只有基於長期封存或資料移轉等用途才需要使用匯出。


## 後續步驟

- 如需使用 Azure 入口網站復原至某個時間點的詳細步驟，請參閱[使用 Azure 入口網站還原時間點](sql-database-point-in-time-restore-portal.md)。
- 如需使用 PowerShell 復原至某個時間點的詳細步驟，請參閱[使用 PowerShell 的還原時間點](sql-database-point-in-time-restore-powershell.md)。
- 如需如何使用 REST API 復原至某個時間點的相關資訊，請參閱[建立或更新資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)。
- 如需如何從使用者或應用程式錯誤中復原的完整討論，請參閱[從使用者錯誤復原](sql-database-user-error-recovery.md)。

## 其他資源

- [商務持續性案例](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->