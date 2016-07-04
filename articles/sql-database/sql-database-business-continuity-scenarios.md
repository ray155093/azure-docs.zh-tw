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
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Azure SQL Database 的商務持續性案例

> [AZURE.SELECTOR]
- [商務持續性](sql-database-business-continuity.md)
- [案例](sql-database-business-continuity-scenarios.md)
- [還原時間點](sql-database-point-in-time-retore.md)
- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)
- [異地還原](sql-database-geo-restore.md)
- [主動式異地複寫](sql-database-geo-replication)

在本文中，您將了解數個 Azure SQL Database 商務持續性案例。

## 從中斷復原

[還原 Azure SQL Database 或容錯移轉到次要資料庫](sql-database-disaster-recovery.md)說明如何使用下列功能來從中斷復原：

- [主動式異地複寫](sql-database-geo-replication-overview.md)
- [異地還原](sql-database-geo-restore.md)

本文討論初始復原的時機、如何使用每一個功能進行復原、如何在復原之後設定您的資料庫，以及如何在復原之後設定您的應用程式。

## 從使用者錯誤復原

[從使用者錯誤復原 Azure SQL Database](sql-database-user-error-recovery.md) 說明如何使用下列功能，從使用者錯誤或非預期的資料修改中復原：

- [還原時間點](sql-database-point-in-time-restore.md) 
- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)

## 執行災害復原演練

[執行災害復原演練](sql-database-disaster-recovery-drills.md)說明如何使用下列功能執行災害復原演練：

- [主動式異地複寫](sql-database-geo-replication-overview.md)
- [異地還原](sql-database-geo-restore.md)

建議您定期驗證應用程式的復原工作流程整備。最佳設計作法是，驗證容錯移轉所涉及之資料遺失和 (或) 中斷的應用程式行為和影響。這也是大多數業界標準在商務持續性認證中的規定。

災害復原演練內容包括：

- 模擬資料層中斷情況
- 復原 
- 驗證復原後的應用程式完整性

## 管理災害復原後的安全性

[如何管理災害復原後的安全性](sql-database-geo-replication-security-config.md)說明設定和控制[作用中異地複寫](sql-database-geo-replication-overview.md)的驗證需求，以及設定使用者存取次要資料庫權限所需的步驟。它也會說明如何在使用[異地還原](sql-database-geo-restore.md)之後，啟用復原資料庫的存取權。

## 使用作用中異地複寫管理雲端應用程式的輪流升級

[使用 SQL Database 作用中異地複寫管理雲端應用程式的輪流升級](sql-database-manage-application-rolling-upgrade.md)說明如何使用 SQL Database 中的[異地複寫](sql-database-geo-replication-overview.md)，來啟用雲端應用程式的輪流升級。因為升級是干擾性作業，它應該是您商務持續性規劃與設計的一部分。本文將探討兩種不同的升級程序協調方法，並討論每個選項的優缺點。

## 使用作用中異地複寫設計雲端災害復原應用程式

[使用 SQL Database 的作用中異地複寫設計雲端災害復原應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)說明如何使用 SQL Database 中的[作用中異地複寫](sql-database-geo-replication-overview.md)，來設計具備區域失敗和嚴重中斷復原功能的資料庫應用程式。本文會將應用程式部署拓撲、您設為目標的服務等級協定、流量延遲及成本納入考量，接著探討常見的應用程式模式 - 各有其優缺點。

## 使用彈性資料庫集區的應用程式災害復原策略

[使用 SQL Database 彈性集區之應用程式的災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)說明使用[彈性資料庫集區](sql-database-elastic-pool.md)的災害復原案例。

## 後續步驟

- 如需針對災害復原使用和設定作用中異地複寫的相關資訊，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)
- 如需針對災害復原使用異地還原的相關資訊，請參閱[異地還原](sql-database-geo-restore.md)

## 其他資源

- [SQL Database 商務持續性和災害復原](sql-database-business-continuity.md)
- [還原時間點](sql-database-point-in-time-restore.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [異地複寫的安全性設定](sql-database-geo-replication-security-config.md)
- [SQL Database BCDR 常見問題集](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->