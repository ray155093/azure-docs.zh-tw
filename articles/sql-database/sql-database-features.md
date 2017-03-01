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
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 5d8fa8328bb2257b77a27fc2fa33565e20000e10
ms.lasthandoff: 02/17/2017


---
# <a name="azure-sql-database-features"></a>Azure SQL Database 功能
此主題會提供 Azure SQL Database 邏輯伺服器和資料庫的概觀，並包含每個所列出功能連結的功能支援矩陣。 

## <a name="what-is-an-azure-sql-database-logical-server"></a>什麼是 Azure SQL Database 邏輯伺服器？
Azure SQL Database 邏輯伺服器做為多個資料庫的中央管理點。 在 SQL Database 中，伺服器是邏輯建構，不同於您可能已熟悉運用在內部部署世界中的 SQL Server 執行個體。 具體來說，SQL Database 服務對於其邏輯伺服器相關之資料庫位置不提供任何保證，且不公開任何執行個體層級存取權或功能。 如需 Azure SQL 邏輯伺服器的詳細資訊，請參閱[邏輯伺服器](sql-database-server-overview.md)。 

## <a name="what-is-an-azure-sql-database"></a>什麼是 Azure SQL Database？
Azure SQL Database 的每個資料庫會與邏輯伺服器相關聯。 資料庫可以是︰

- 單一資料庫包含其[自有資源集](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- [資料庫集區](sql-database-elastic-pool.md)的一部分，[共用一個資源集](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU)
- [分區化資料庫向外延展集](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)的一部分，可以是單一或集區資料庫
- 參與[多租用戶 SaaS 設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)的資料庫集一部分，其資料庫可以是單一值或集區資料庫 (或兩者) 

如需 Azure SQL 資料庫的詳細資訊，請參閱 [SQL Database](sql-database-overview.md)。

## <a name="what-features-are-supported"></a>支援哪些功能？

下表列出 Azure SQL Database 和 SQL Server 的主要功能、指定其支援性，並提供每個平台上的功能詳細資訊連結。 針對 Transact-SQL 功能，請依照資料表中的連結以了解功能的類別。 另請參閱 [Azure SQL Database Transact-SQL 的差異](sql-database-transact-sql-information.md)，了解缺乏特定類型功能支援的原因詳細背景。

我們持續新增 V12 的功能。 因此我們鼓勵您造訪我們的 Azure 服務更新網頁，並使用它的篩選條件：

* 篩選出 [SQL Database 服務](https://azure.microsoft.com/updates/?service=sql-database)。
* 篩選出 SQL Database 功能的「正式運作 [(GA)」公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 。

> [!TIP]
> 若要測試現有資料庫與 Azure SQL Database 的相容性，請參閱[將 SQL Server Database 移轉到 Azure](sql-database-cloud-migrate.md)。
>

| **功能** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| 主動式異地複寫 | 不支援 - 請參閱[啟用 AlwaysOn 可用性群組](https://msdn.microsoft.com/library/hh510230.aspx) | [支援](sql-database-geo-replication-overview.md)
| 一律加密 | [支援](https://msdn.microsoft.com/library/mt163865.aspx) | [支援](sql-database-always-encrypted.md) |
| AlwaysOn 可用性群組 | [支援](https://msdn.microsoft.com/library/hh510230.aspx) | 不支援 - 請參閱[主動式異地複寫](sql-database-geo-replication-overview.md) |
| 連接資料庫 | [支援](https://msdn.microsoft.com/library/ms190209.aspx) | 不支援 |
| 應用程式角色 | [支援](https://msdn.microsoft.com/library/ms190998.aspx) | [支援](https://msdn.microsoft.com/library/ms190998.aspx) |
| 自動調整 | 不支援 | [支援](sql-database-service-tiers.md) |
| Azure Active Directory | 不支援 | [支援](sql-database-aad-authentication.md) |
| Azure Data Factory | 不支援 - 請參閱 [SQL Server Integration Services (SSIS)](https://msdn.microsoft.com/library/ms141026.aspx) | [支援](https://azure.microsoft.com/services/data-factory/) |
| 稽核 | [支援](https://msdn.microsoft.com/library/cc280386.aspx) | [支援](sql-database-auditing.md) |
| BACPAC 檔案 (匯出) | [支援](https://msdn.microsoft.com/library/hh213241.aspx) | [支援](sql-database-export.md) |
| BACPAC 檔案 (匯入) | [支援](https://msdn.microsoft.com/library/hh710052.aspx) | [支援](sql-database-import-portal.md) |
| 備份和還原陳述式 | [支援](https://msdn.microsoft.com/library/ff848768.aspx) | 不支援 |
| 內建函數 | [支援](https://msdn.microsoft.com/library/ms174318.aspx) | [大部分](https://msdn.microsoft.com/library/ms174318.aspx) |
| 變更資料擷取 | [支援](https://msdn.microsoft.com/library/cc645937.aspx) | 不支援 |
| 變更追蹤 | [支援](https://msdn.microsoft.com/library/bb933875.aspx) | [支援](https://msdn.microsoft.com/library/bb933875.aspx) |
| 定序陳述式 | [支援](https://msdn.microsoft.com/library/ff848763.aspx) | [支援](https://msdn.microsoft.com/library/ff848763.aspx) |
| 資料行存放區索引 | [支援](https://msdn.microsoft.com/library/gg492088.aspx) | [僅限高階版](https://msdn.microsoft.com/library/gg492088.aspx) |
| Common Language Runtime (CLR) | [支援](https://msdn.microsoft.com/library/ms131102.aspx) | 不支援 |
| 自主資料庫 | [支援](https://msdn.microsoft.com/library/ff929071.aspx) | 內建 |
| 自主使用者 | [支援](https://msdn.microsoft.com/library/ff929188.aspx) | [支援](sql-database-manage-logins.md#non-administrator-users) |
| 控制流程語言關鍵字 | [支援](https://msdn.microsoft.com/library/ms174290.aspx) | [支援](https://msdn.microsoft.com/library/ms174290.aspx) |
| 跨資料庫查詢 | [支援](https://msdn.microsoft.com/library/dn584627.aspx) | [彈性查詢](sql-database-elastic-query-overview.md) |
| 資料指標 | [支援](https://msdn.microsoft.com/library/ms181441.aspx) | [支援](https://msdn.microsoft.com/library/ms181441.aspx) | 
| 資料壓縮 | [支援](https://msdn.microsoft.com/library/cc280449.aspx) | [支援](https://msdn.microsoft.com/library/cc280449.aspx) |
| 資料庫備份 | [公開給使用者](https://msdn.microsoft.com/library/ms187048.aspx) | [內建](sql-database-automated-backups.md) |
| Database Mail | [支援](https://msdn.microsoft.com/library/ms189635.aspx) | 不支援 |
| 資料庫鏡像 | [支援](https://msdn.microsoft.com/library/ms189852.aspx) | 不支援 |
| 資料庫組態選項 | [支援](https://msdn.microsoft.com/library/mt629158.aspx) | [支援](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [支援](https://msdn.microsoft.com/library/ff877925.aspx) | 不支援 |
| 資料庫快照集 | [支援](https://msdn.microsoft.com/library/ms175158.aspx) | 不支援 |
| 資料類型 | [支援](https://msdn.microsoft.com/library/ms187752.aspx) | [支援](https://msdn.microsoft.com/library/ms187752.aspx) |  
| DBCC 陳述式 | [所有](https://msdn.microsoft.com/library/ms188796.aspx) | [部分](https://msdn.microsoft.com/library/ms188796.aspx) |
| DDL 陳述式 | [支援](https://msdn.microsoft.com/library/ff848799.aspx) | [大部分](https://msdn.microsoft.com/library/ff848799.aspx)
| DDL 觸發程序 | [支援](https://msdn.microsoft.com/library/ms175941.aspx) | [僅限資料庫](https://msdn.microsoft.com/library/ms175941.aspx) |
| 分散式交易 | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | 僅有限的內部 SQL Database 案例 |
| DML 陳述式 | [支援](https://msdn.microsoft.com/library/ff848766.aspx) | [大部分](https://msdn.microsoft.com/library/ff848766.aspx) |
| DML 觸發程序 | [支援](https://msdn.microsoft.com/library/ms178110.aspx) | [支援](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMV | [所有](https://msdn.microsoft.com/library/ms188754.aspx) | [部分](https://msdn.microsoft.com/library/ms188754.aspx) |
| 彈性集區 | 不支援 | [支援](sql-database-elastic-pool.md) |
| 彈性工作 | 不支援 - 請參閱 [SQL Server 代理程式](https://msdn.microsoft.com/library/ms189237.aspx) | [支援](sql-database-elastic-jobs-getting-started.md) | 
| 彈性查詢 | 不支援 - 請參閱[跨資料庫查詢](https://msdn.microsoft.com/library/dn584627.aspx) | [支援](sql-database-elastic-query-overview.md) |
| 事件通知 | [支援](https://msdn.microsoft.com/library/ms186376.aspx) | [支援](sql-database-insights-alerts-portal.md) |
| 運算式 | [支援](https://msdn.microsoft.com/library/ms190286.aspx) | [支援](https://msdn.microsoft.com/library/ms190286.aspx) |
| 擴充事件 | [支援](https://msdn.microsoft.com/library/bb630282.aspx) | [部分](sql-database-xevent-db-diff-from-svr.md) |
| 擴充預存程序 | [支援](https://msdn.microsoft.com/library/ms164627.aspx) | 不支援 |
| 檔案群組 | [支援](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [僅主要](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Filestream | [支援](https://msdn.microsoft.com/library/gg471497.aspx) | 不支援 |
| 全文檢索搜尋 | [支援](https://msdn.microsoft.com/library/ms142571.aspx) | [不支援協力廠商斷詞工具](https://msdn.microsoft.com/library/ms142571.aspx) |
| Functions | [支援](https://msdn.microsoft.com/library/ms174318.aspx) | [大部分](https://msdn.microsoft.com/library/ms174318.aspx) |
| 記憶體內部最佳化 | [支援](https://msdn.microsoft.com/library/dn133186.aspx) | [僅限高階版](https://msdn.microsoft.com/library/dn133186.aspx) |
| 作業 | [SQL Server 代理程式](https://msdn.microsoft.com/library/ms189237.aspx) | [支援](sql-database-elastic-jobs-getting-started.md) |
| JSON 資料支援 | [支援](https://msdn.microsoft.com/library/dn921897.aspx) | [支援](sql-database-json-features.md) |
| 語言元素 | [支援](https://msdn.microsoft.com/library/ff848807.aspx) | [大部分](https://msdn.microsoft.com/library/ff848807.aspx) |  
| 連結的伺服器 | [支援](https://msdn.microsoft.com/library/ms188279.aspx) | 不支援 - 請參閱[彈性查詢](sql-database-elastic-query-horizontal-partitioning.md) |
| 記錄傳送 | [支援](https://msdn.microsoft.com/library/ms187103.aspx) | 不支援 - 請參閱[主動式異地複寫](sql-database-geo-replication-overview.md) |
| 管理命令 | [支援](https://msdn.microsoft.com/library/ms190286.aspx)| [不支援](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [支援](https://msdn.microsoft.com/library/ff487003.aspx) | 不支援 |
| 最低記錄大量匯入 | [支援](https://msdn.microsoft.com/library/ms190422.aspx) | 不支援 |
| 修改系統資料 | [支援](https://msdn.microsoft.com/library/ms178028.aspx) | 不支援 |
| 線上索引作業 | [支援](https://msdn.microsoft.com/library/ms177442.aspx) | [服務層的交易大小限制](https://msdn.microsoft.com/library/ms177442.aspx) |
| 運算子 | [支援](https://msdn.microsoft.com/library/ms174986.aspx) | [大部分](https://msdn.microsoft.com/library/ms174986.aspx) |
| 資料庫還原時間點 | [支援](https://msdn.microsoft.com/library/ms179451.aspx) | [支援](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [支援](https://msdn.microsoft.com/library/mt143171.aspx) | [不支援]
| 原則式管理 | [支援](https://msdn.microsoft.com/library/bb510667.aspx) | 不支援 |
| 述詞 | [支援](https://msdn.microsoft.com/library/ms189523.aspx) | [大部分](https://msdn.microsoft.com/library/ms189523.aspx)
| 資源管理員 | [支援](https://msdn.microsoft.com/library/bb933866.aspx) | [內建](sql-database-service-tiers.md) |
| 從備份還原資料庫 | [支援](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [僅從內建備份](sql-database-recovery-using-backups.md) |
| 資料列層級安全性 | [支援](https://msdn.microsoft.com/library/dn765131.aspx) | [支援](https://msdn.microsoft.com/library/dn765131.aspx) |
| 安全性陳述式 | [支援](https://msdn.microsoft.com/library/ff848791.aspx) | [部分](https://msdn.microsoft.com/library/ff848791.aspx) |
| 語意搜尋 | [支援](https://msdn.microsoft.com/library/gg492075.aspx) | 不支援 |
| 序號 | [支援](https://msdn.microsoft.com/library/ff878058.aspx) | [支援](https://msdn.microsoft.com/library/ff878058.aspx) |
| Service Broker | [支援](https://msdn.microsoft.com/library/bb522893.aspx) | 不支援 |
| 伺服器組態選項 | [支援](https://msdn.microsoft.com/library/ms189631.aspx) | 不支援 - 請參閱[資料庫組態選項](https://msdn.microsoft.com/library/mt629158.aspx) |
| SET 陳述式 | [支援](https://msdn.microsoft.com/library/ms190356.aspx) | [大部分](https://msdn.microsoft.com/library/ms190356.aspx) 
| 空間 | [支援](https://msdn.microsoft.com/library/bb933790.aspx) | [支援](https://msdn.microsoft.com/library/bb933790.aspx) |
| SQL Server 代理程式 | [支援](https://msdn.microsoft.com/library/ms189237.aspx) | 不支援 - 請參閱[彈性工作](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [支援](https://msdn.microsoft.com/library/bb522607.aspx) | 不支援 - 請參閱 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [支援](https://msdn.microsoft.com/library/ms141026.aspx) | 不支援 - 請參閱 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [支援](https://msdn.microsoft.com/library/hh245198.aspx) | [支援](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server Profiler | [支援](https://msdn.microsoft.com/library/ms181091.aspx) | 不支援 - 請參閱[擴充事件](https://msdn.microsoft.com/library/ms181091.aspx) |
| SQL Server 複寫 | [支援](https://msdn.microsoft.com/library/ms151198.aspx) | [僅限交易和快照複寫訂閱者](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [支援](https://msdn.microsoft.com/library/ms159106.aspx) | 不支援 |
| 預存程序 | [支援](https://msdn.microsoft.com/library/ms190782.aspx) | [支援](https://msdn.microsoft.com/library/ms190782.aspx) |
| 系統預存函式 | [支援](https://msdn.microsoft.com/library/ff848780.aspx) | [部分](https://msdn.microsoft.com/library/ff848780.aspx) |
| 系統預存程序 | [支援](https://msdn.microsoft.com/library/ms187961.aspx) | [部分](https://msdn.microsoft.com/library/ms187961.aspx) |
| 系統資料表 | [支援](https://msdn.microsoft.com/library/ms179932.aspx) | [部分](https://msdn.microsoft.com/library/ms179932.aspx) |
| 系統檢視表 | [支援](https://msdn.microsoft.com/library/ms177862.aspx) | [部分](https://msdn.microsoft.com/library/ms177862.aspx)
| 資料表分割 | [支援](https://msdn.microsoft.com/library/ms190787.aspx) | [僅限主要檔案群組](https://msdn.microsoft.com/library/ms190787.aspx) |
| 暫存資料表 | [本機和全域](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [僅限本機](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| 暫存資料表 | [支援](https://msdn.microsoft.com/library/dn935015.aspx) | [支援](sql-database-temporal-tables.md) |
| 交易陳述式 | [支援](https://msdn.microsoft.com/library/ms174377.aspx) | [支援](https://msdn.microsoft.com/library/ms174377.aspx) |
| 變數 | [支援](https://msdn.microsoft.com/library/ff848809.aspx) | | [支援](https://msdn.microsoft.com/library/ff848809.aspx) | 
| 透明資料加密 (TDE)  | [支援](https://msdn.microsoft.com/library/bb934049.aspx) | [支援](https://msdn.microsoft.com/dn948096.aspx) |
| Windows Server 容錯移轉叢集 | [支援](https://msdn.microsoft.com/library/hh270278.aspx) | 不支援 - 請參閱[主動式異地複寫](sql-database-geo-replication-overview.md) |
| XML 索引 | [支援](http://msdn.microsoft.com/library/bb934097.aspx) | [支援](http://msdn.microsoft.com/library/bb934097.aspx) |
| XML 陳述式 | [支援](https://msdn.microsoft.com/library/ff848798.aspx) | [支援](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>後續步驟

- 如需了解 Azure SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database？](sql-database-technical-overview.md)
- 如需了解 Azure SQL 邏輯伺服器的概觀，請參閱 [SQL Database 邏輯伺服器概觀](sql-database-server-overview.md)
- 如需了解 Azure SQL Database 的概觀，請參閱 [SQL Database 概觀](sql-database-overview.md)
- 如需了解 Transact-SQL 支援與差異的詳細資訊，請參閱 [ 的差異](sql-database-transact-sql-information.md)。
- 如需根據您**服務層**的特定資源配額和限制。 如需服務層級的概觀，請參閱 [SQL Database 服務層級](sql-database-service-tiers.md)。
- 如需安全性概觀，請參閱 [Azure SQL Database 安全性概觀](sql-database-security-overview.md)。
- 如需驅動程式的可用性和 SQL Database 支援的相關資訊，請參閱＜ [SQL Database 與 SQL Server 的連線庫](sql-database-libraries.md)＞。

