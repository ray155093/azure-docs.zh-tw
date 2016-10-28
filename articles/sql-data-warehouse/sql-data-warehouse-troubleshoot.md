<properties
   pageTitle="針對 Azure SQL 資料倉儲問題進行疑難排解 | Microsoft Azure"
   description="針對 Azure SQL 資料倉儲問題進行疑難排解。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# 針對 Azure SQL 資料倉儲問題進行疑難排解

本主題列出我們從客戶聽來的一些較常見的疑難排解問題。

## 連接

| 問題 | 解決方案 |
| :----------------------------------| :---------------------------------------------- |
| 使用者 'NT AUTHORITY\\ANONYMOUS LOGON' 登入失敗。(Microsoft SQL Server，錯誤：18456) | 當 AAD 使用者嘗試連線到主要資料庫，但主要資料庫中沒有使用者時，就會發生此錯誤。若要修正此問題，請在連線時指定您想要連接的 SQL 資料倉儲，或將使用者新增到主要資料庫。如需詳細資訊，請參閱[安全性概觀][]一文。|
|伺服器主體 "MyUserName" 在目前的資訊安全內容下無法存取「主要」資料庫。無法開啟使用者預設資料庫。登入失敗。使用者 'MyUserName' 登入失敗。(Microsoft SQL Server，錯誤：916) | 當 AAD 使用者嘗試連線到主要資料庫，但主要資料庫中沒有使用者時，就會發生此錯誤。若要修正此問題，請在連線時指定您想要連接的 SQL 資料倉儲，或將使用者新增到主要資料庫。如需詳細資訊，請參閱[安全性概觀][]一文。|
| CTAIP 錯誤 | 若已在 SQL Server Master 資料庫上建立登入，但未在 SQL 資料倉儲資料庫上建立，則會發生這個錯誤。如果您遇到這個錯誤，請查看[安全性概觀][]一文。這篇文章說明如何在主要資料庫上建立登入和使用者，接著如何在 SQL 資料倉儲資料庫上建立使用者。|
| 遭到防火牆封鎖 |為確保只有已知的 IP 位址擁有資料庫的存取權限，Azure SQL 資料庫受到伺服器及資料庫層級的防火牆所保護。防火牆預設將會受到保護，因此您在可以連線之前，必須明確啟用單一 IP 位址或位址範圍。若要設定防火牆的存取，請遵循[佈建指示][]中[設定用戶端 IP 的伺服器防火牆存取][]的步驟。|
| 無法與工具或驅動程式連線 | SQL 資料倉儲建議使用 [SSMS][]、[SSDT for Visual Studio 2015][] 或 [sqlcmd][] 來查詢您的資料。如需驅動程式和連接到 SQL 資料倉儲的詳細資訊，請參閱 [Azure SQL 資料倉儲的驅動程式][]和[連接到 Azure SQL 資料倉儲][]文章。|


## 工具

| 問題 | 解決方案 |
| :----------------------------------| :---------------------------------------------- |
| Visual Studio 物件總管中遺漏 AAD 使用者 | 這是已知的問題。解決方法是在 [sys.database\_principals][] 中檢視使用者。若要深入了解使用 Azure Active Directory 與 SQL 資料倉儲，請參閱[適用於 Azure SQL 資料倉儲的驗證][]。|

## 效能

| 問題 | 解決方案 |
| :----------------------------------| :---------------------------------------------- |
| 查詢效能疑難排解 | 如果您正試著針對特定查詢進行疑難排解，請從[了解如何監視查詢][]開始。|
| 查詢效能和計劃不佳通常是因為遺漏統計資料 | 效能不佳最常見的原因是缺乏資料表的統計資料。如需有關如何建立統計資料，以及這對於效能為何重要，請參閱[維護資料表統計資料][Statistics]。|
| 並行存取低落/排入佇列的查詢偏少 | 為了瞭解如何平衡記憶體配置與並行存取，必須先了解[工作負載管理][]。|
| 如何實作最佳作法 | [SQL 資料倉儲最佳作法][]一文是學習改善查詢效能的最佳起點。|
| 如何透過調整來提升效能 | 有時，只要[調整您的 SQL 資料倉儲][]來提升查詢的計算能力，即可改善效能。|
| 索引品質不佳導致查詢效能不佳 | 有時，查詢會因為[資料行存放區索引品質不佳][]而變慢。請參閱這篇文章，以取得詳細資訊及如何[重建索引以提升區段品質][]。|

## 系統管理

| 問題 | 解決方案 |
| :----------------------------------| :---------------------------------------------- |
| 訊息 40847：無法執行這項作業，因為伺服器可能會超過允許的資料庫交易單位配額 45000。 | 減少您正在嘗試建立的資料庫 [DWU][]，或是[要求增加配額][]。|
| 調查空間使用量 | 請參閱[資料表大小][]，以了解您系統的空間使用量。|
| 協助管理資料表 | 請參閱[資料表概觀][Overview]一文，以協助管理您的資料表。本文還包含更詳細主題的連結，例如[資料表的資料類型][Data types]、[散發資料表][Distribute]、[編製資料表的索引][Index]、[分割資料表][Partition]、[維護資料表統計資料][Statistics]和[暫存資料表][Temporary]。|

## Polybase

| 問題 | 解決方案 |
| :----------------------------------| :---------------------------------------------- |
| UTF-8 錯誤 | 目前，PolyBase 僅支援載入以 UTF-8 編碼的資料檔案。如需有關如何克服這項限制的指引，請參閱[解決 PolyBase UTF-8 需求][]。|
| 因為資料列太大而載入失敗 | 目前，Polybase 不支援大型的資料列。這表示如果資料表包含 VARCHAR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX)，則無法使用外部資料表來載入您的資料。目前，只有透過 Azure Data Factory (含 BCP) Azure 串流分析、SSIS、BCP 或 .NET SQLBulkCopy 類別，才支援載入大型資料列。未來版本將增加讓 PolyBase 支援大型資料列。|
| 使用 bcp 載入含有 MAX 資料類型的資料表失敗 | 已知的問題是在某些情況下，VARCHAR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX) 必須放在資料表結尾。請嘗試將您的 MAX 資料行移到資料表的結尾。|

## 與 SQL Database 不同之處

| 問題 | 解決方案 |
| :----------------------------------| :---------------------------------------------- |
| 不支援的 SQL Database 功能 | 請參閱[不支援的資料表功能][]。|
| 不支援的 SQL Database 資料類型 | 請參閱[不支援的資料類型][]。|
| DELETE 和 UPDATE 限制 | 請參閱 [UPDATE 因應措施][]、[DELETE 因應措施][]和[使用 CTAS 來解決不支援 UPDATE 和 DELETE 語法的問題][]。 |
| 不支援 MERGE 陳述式 | 請參閱 [MERGE 因應措施][]。|
| 預存程序限制 | 請參閱[預存程序限制][]，以了解預存程序的一些限制。|
| UDF 不支援 SELECT 陳述式 | 這是 UDF 目前的限制。關於我們支援的語法，請參閱 [CREATE FUNCTION][]。 |

## 後續步驟

如果您找不到解決上述問題的方法，以下是一些您可以嘗試的其他資源。

- [部落格]
- [功能要求]
- [影片]
- [CAT 小組部落格]
- [建立支援票證]
- [MSDN 論壇]
- [Stack Overflow 論壇]
- [Twitter]

<!--Image references-->

<!--Article references-->
[安全性概觀]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Azure SQL 資料倉儲的驅動程式]: ./sql-data-warehouse-connection-strings.md
[連接到 Azure SQL 資料倉儲]: ./sql-data-warehouse-connect-overview.md
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[調整您的 SQL 資料倉儲]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[要求增加配額]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[了解如何監視查詢]: ./sql-data-warehouse-manage-monitor.md
[佈建指示]: ./sql-data-warehouse-get-started-provision.md
[設定用戶端 IP 的伺服器防火牆存取]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[SQL 資料倉儲最佳作法]: ./sql-data-warehouse-best-practices.md
[資料表大小]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[不支援的資料表功能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[不支援的資料類型]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[資料行存放區索引品質不佳]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[重建索引以提升區段品質]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[工作負載管理]: ./sql-data-warehouse-develop-concurrency.md
[使用 CTAS 來解決不支援 UPDATE 和 DELETE 語法的問題]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE 因應措施]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE 因應措施]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE 因應措施]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[預存程序限制]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[適用於 Azure SQL 資料倉儲的驗證]: ./sql-data-warehouse-authentication.md
[解決 PolyBase UTF-8 需求]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database\_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT 小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 論壇]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow 論壇]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[影片]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!----HONumber=AcomDC_0907_2016-->