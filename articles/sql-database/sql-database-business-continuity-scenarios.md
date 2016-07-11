<properties
	pageTitle="Azure SQL Database 商務持續性案例 | Microsoft Azure"
	description="Azure SQL Database 商務續航力案例"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="carlrab"
   ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Azure SQL Database 的商務持續性案例

本文將介紹適用於商務持續性的數個災害復原案例及數個應用程式設計案例。

## 從中斷復原

在發生中斷的情況下，[從中斷復原 Azure SQL Database](sql-database-disaster-recovery.md) 說明如何使用下列商務持續性解決方案來從中斷復原：

- [主動式異地複寫](sql-database-geo-replication-overview.md)
- [異地還原](sql-database-recovery-using.backups.md#geo-restore)

從中斷復原所需的特定步驟和時間長度，會根據您所選擇的商務持續性解決方案而有所不同。不過，不論您的商務持續性解決方案為何，您都需要知道何時開始復原、適用於每個商務持續性解決方案的資料庫修復步驟、如何在復原之後設定資料庫，以及如何在復原之後設定應用程式，以便完成從中斷復原。

## 從錯誤復原

在發生使用者錯誤或其他意外修改資料的情況下，[從錯誤復原 Azure SQL Database](sql-database-user-error-recovery.md) 說明如何使用下列商務持續性解決方案來從錯誤復原：

- [還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)
- [還原已刪除的資料庫](sql-database-recovery-using-backups.md#deleted-database-restore)

從中斷復原所需的特定步驟和時間長度，會根據您所選擇的商務持續性解決方案而有所不同。不過，不論您的商務持續性解決方案為何，您都需要知道如何針對每個商務持續性解決方案從錯誤復原。

## 執行災害復原演練來為中斷做好準備

若要使您的商務持續性解決方案有效，建議您定期驗證應用程式的復原工作流程整備。最佳設計作法是，驗證容錯移轉所涉及之資料遺失和 (或) 中斷的應用程式行為和影響。這也是大多數業界標準在商務持續性認證中的規定。

災害復原演練內容包括：

- 模擬資料層中斷情況
- 復原
- 驗證復原後的應用程式完整性

[執行災害復原演練](sql-database-disaster-recovery-drills.md)說明如何使用下列其中一個商務持續性解決方案來執行災害復原演練：

- [主動式異地複寫](sql-database-geo-replication-overview.md)
- [異地還原](sql-database-recovery-using-backups.md#geo-restore)

## 使用作用中異地複寫管理雲端應用程式的輪流升級

使用 SQL Database 升級雲端應用程式是一項干擾性作業，因此，您需要包含這個案例做為商務持續性規劃和設計的一部分。[管理應用程式升級](sql-database-manage-application-rolling-upgrade.md)說明如何使用 SQL Database 中的[異地複寫](sql-database-geo-replication-overview.md)，來啟用雲端應用程式的輪流升級。因為升級，本文將探討兩種不同的升級程序協調方法，並討論每個選項的優缺點。

## 使用作用中異地複寫設計雲端災害復原應用程式

[設計雲端災害復原應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)說明如何使用 SQL Database 中的[作用中異地複寫](sql-database-geo-replication-overview.md)，來設計具備區域失敗和嚴重中斷復原功能的資料庫應用程式。本文會將應用程式部署拓撲、您設為目標的服務等級協定、流量延遲及成本納入考量，接著探討常見的應用程式模式 - 各有其優缺點。

## 使用彈性資料庫集區的應用程式災害復原策略

[彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)說明使用[彈性資料庫集區](sql-database-elastic-pool.md)的災害復原案例。

## 後續步驟

- 如需商務持續性概觀，請參閱[商務持續性概觀](sql-database-business-continuity.md)
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
- 若要了解商務持續性設計及復原案例，請參閱[持續性案例](sql-database-business-continuity-scenarios.md)
- 若要了解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
- 若要了解更快速的復原選項，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)
- 若要了解如何使用自動備份進行封存，請參閱[資料庫複製](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->