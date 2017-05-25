---
title: "Azure SQL Database 功能概觀 | Microsoft Docs"
description: "此頁面會提供 Azure SQL Database 邏輯伺服器和資料庫的概觀，並包含每個所列出功能連結的功能支援矩陣。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: features
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 9daf8be93216aefbcf02e3fcba44d048ab95b43d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/18/2017


---
# <a name="azure-sql-database-features"></a>Azure SQL Database 功能

下表列出 Azure SQL Database 的主要功能和 SQL Server 的同等功能，並提供是否支援每項特定功能的相關資訊以及每個平台上的功能詳細資訊連結。 如需移轉現有資料庫解決方案時所要考量的 Transact-SQL 差異，請參閱[解決移轉至 SQL Database 期間的 Transact-SQL 差異](sql-database-transact-sql-information.md)。

我們會持續新增 Azure SQL Database 的功能。 因此我們鼓勵您造訪我們的 Azure 服務更新網頁，並使用它的篩選條件：

* 篩選出 [SQL Database 服務](https://azure.microsoft.com/updates/?service=sql-database)。
* 篩選出 SQL Database 功能的「正式運作 [(GA)」公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 。

| **功能** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| 主動式異地複寫 | 不支援 - 請參閱[Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [支援](sql-database-geo-replication-overview.md)
| 一律加密 | [支援](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 支援 - 請參閱[憑證存放區](sql-database-always-encrypted.md)和[金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md)|
| AlwaysOn 可用性群組 | [支援](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 不支援 - 請參閱[主動式異地複寫](sql-database-geo-replication-overview.md) |
| 連結資料庫 | [支援](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 不支援 |
| 應用程式角色 | [支援](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [支援](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| 自動調整 | 不支援 | 支援 - 請參閱[服務層](sql-database-service-tiers.md) |
| Azure Active Directory | 不支援 | [支援](sql-database-aad-authentication.md) |
| Azure Data Factory | [支援](../data-factory/data-factory-introduction.md) | [支援](../data-factory/data-factory-introduction.md) |
| 稽核 | [支援](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [支援](sql-database-auditing.md) |
| BACPAC 檔案 (匯出) | [支援](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [支援](sql-database-export.md) |
| BACPAC 檔案 (匯入) | [支援](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [支援](sql-database-import.md) |
| 備份 | [支援](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 不支援 |
| 內建函式 | [支援](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱 [個別函式] (https://docs.microsoft.com/sql/t-sql/functions/functions) |
| 變更資料擷取 | [支援](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 不支援 |
| 變更追蹤 | [支援](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [支援](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| 定序陳述式 | [支援](https://docs.microsoft.com/sql/t-sql/statements/collations) | [支援](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| 資料行存放區索引 | [支援](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [僅限高階版](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| Common Language Runtime (CLR) | [支援](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 不支援 |
| 自主資料庫 | [支援](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [支援](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| 自主使用者 | [支援](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [支援](sql-database-manage-logins.md#non-administrator-users) |
| 控制流程語言關鍵字 | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| 跨資料庫查詢 | [支援](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | 部分 - 請參閱[彈性查詢](sql-database-elastic-query-overview.md) |
| 資料指標 | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| 資料壓縮 | [支援](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [支援](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| 資料庫備份 | [使用者管理](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [由 SQL Database 服務管理](sql-database-automated-backups.md) |
| Database Mail | [支援](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 不支援 |
| 資料庫鏡像 | [支援](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 不支援 |
| 資料庫組態設定 | [支援](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [支援](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [支援](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 不支援 |
| 資料庫快照集 | [支援](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 不支援 |
| 資料類型 | [支援](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [支援](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| DBCC 陳述式 | [支援](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大部分 - 請參閱[個別陳述式](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) |
| DDL 陳述式 | [支援](https://docs.microsoft.com/sql/t-sql/statements/statements) | 大部分 - 請參閱[個別陳述式](https://docs.microsoft.com/sql/t-sql/statements/statements)
| DDL 觸發程序 | [支援](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [僅限資料庫](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| 分散式交易 | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 僅有限的內部 SQL Database 案例 |
| DML 陳述式 | [支援](https://docs.microsoft.com/sql/t-sql/queries/queries) | 大部分 - 請參閱 [個別陳述式]](https://docs.microsoft.com/sql/t-sql/queries/queries) |
| DML 觸發程序 | [支援](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [支援](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMV | [所有](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 部分 - 請參閱[個別 DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) |
| 彈性集區 | 不支援 | [支援](sql-database-elastic-pool.md) |
| 彈性工作 | 不支援 - 請參閱 [SQL Server 代理程式](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | [支援](sql-database-elastic-jobs-getting-started.md) | 
| 彈性查詢 | 不支援 - 請參閱[跨資料庫查詢](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | [支援](sql-database-elastic-query-overview.md) |
| 事件通知 | [支援](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [支援](sql-database-insights-alerts-portal.md) |
| 運算式 | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| 擴充事件 | [支援](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分 - 請參閱[個別事件](sql-database-xevent-db-diff-from-svr.md) |
| 擴充預存程序 | [支援](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 不支援 |
| 檔案和檔案群組 | [支援](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [僅主要](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Filestream | [支援](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 不支援 |
| 全文檢索搜尋 | [支援](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [不支援第三方斷詞工具](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| Functions | [支援](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱[個別函式](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| 記憶體內部最佳化 | [支援](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [僅限高階版](sql-database-in-memory.md) |
| 作業 | 請參閱 [SQL Server 代理程式](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 請參閱[彈性作業](sql-database-elastic-jobs-getting-started.md) |
| JSON 資料支援 | [支援](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [支援](sql-database-json-features.md) |
| 語言元素 | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大部分 - 請參閱[個別元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) |  
| 連結的伺服器 | [支援](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 不支援 - 請參閱[彈性查詢](sql-database-elastic-query-horizontal-partitioning.md) |
| 記錄傳送 | [支援](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 不支援 - 請參閱[主動式異地複寫](sql-database-geo-replication-overview.md) |
| Master Data Services (MDS) | [支援](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 不支援 |
| 最低記錄大量匯入 | [支援](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 不支援 |
| 修改系統資料 | [支援](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 不支援 |
| 線上索引作業 | [支援](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [支援 - 服務層的交易大小限制](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| 運算子 | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大部分 - 請參閱[個別運算子](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) |
| 資料庫還原時間點 | [支援](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [支援](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [支援](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 不支援
| 原則式管理 | [支援](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 不支援 |
| 述詞 | [支援](https://docs.microsoft.com/sql/t-sql/queries/predicates) | 大部分 - 請參閱[個別述詞](https://docs.microsoft.com/sql/t-sql/queries/predicates)
| R 服務 | [支援](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| 資源管理員 | [支援](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 不支援 |
| RESTORE 陳述式 | [支援](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 不支援 | 
| 從備份還原資料庫 | [支援](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [僅從內建備份](sql-database-recovery-using-backups.md) |
| 資料列層級安全性 | [支援](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [支援](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| 語意搜尋 | [支援](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 不支援 |
| 序號 | [支援](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [支援](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Service Broker | [支援](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 不支援 |
| 伺服器組態設定 | [支援](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 不支援 - 請參閱[資料庫組態選項](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| SET 陳述式 | [支援](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大部分 - 請參閱[個別陳述式](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 
| 空間 | [支援](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [支援](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| SQL Server 代理程式 | [支援](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 不支援 - 請參閱[彈性工作](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [支援](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 不支援 - 請參閱 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [支援](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 不支援 - 請參閱 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [支援](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [支援](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server Profiler | [支援](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 不支援 - 請參閱[擴充事件](sql-database-xevent-db-diff-from-svr.md) |
| SQL Server 複寫 | [支援](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [僅限交易和快照複寫訂閱者](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [支援](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 不支援 |
| 預存程序 | [支援](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [支援](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| 系統預存函式 | [支援](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 部分 - 請參閱[個別函式](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) |
| 系統預存程序 | [支援](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 請參閱[個別預存程序](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| 系統資料表 | [支援](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 請參閱[個別資料表](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| 系統目錄檢視 | [支援](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 請參閱[個別檢視](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)
| 資料表分割 | [支援](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 支援 - [僅限主要檔案群組](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| 暫存資料表 | [本機和全域](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [僅限本機](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| 暫存資料表 | [支援](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [支援](sql-database-temporal-tables.md) |
| 交易 | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| 變數 | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [支援](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| 透明資料加密 (TDE)  | [支援](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [支援](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Windows Server 容錯移轉叢集 | [支援](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 不支援 - 請參閱[主動式異地複寫](sql-database-geo-replication-overview.md) |
| XML 索引 | [支援](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [支援](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>後續步驟

- 如需了解 Azure SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database？](sql-database-technical-overview.md)
- 如需 Transact-SQL 支援和差異的詳細資訊，請參閱[解決移轉至 SQL Database 期間的 Transact-SQL 差異](sql-database-transact-sql-information.md)。

