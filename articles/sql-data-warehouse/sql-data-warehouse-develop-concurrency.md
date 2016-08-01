<properties
   pageTitle="SQL 資料倉儲中的並行存取和工作負載管理 | Microsoft Azure"
   description="了解 Azure SQL 資料倉儲中的並行存取和工作負載管理以開發解決方案。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/15/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 資料倉儲中的並行存取和工作負載管理

為了大規模傳遞可預測的效能，SQL 資料倉儲可讓使用者控制並行存取層級以及資源配置，例如記憶體和 CPU 優先順序。本文介紹並行存取和工作負載管理的概念；說明如何實作這兩種功能，以及如何在資料倉儲中控制它們。SQL 資料倉儲工作負載管理的目的，是要幫助您支援多使用者環境。它並不適用於多租用戶工作負載。

## 並行存取限制

SQL 資料倉儲最多允許 1,024 個並行存取連線。這 1,024 個連線全都可以並行提交查詢。不過，為了達到最佳輸送量，SQL 資料倉儲可能會將某些查詢排入佇列，以確保每個查詢能夠獲得最少的記憶體授與。佇列會在查詢執行時發生。藉由在到達並行存取限制時將查詢排入佇列，SQL 資料倉儲就能夠確保作用中查詢能夠取得急需的記憶體資源存取權，以增加總輸送量。

並行存取限制受到兩個概念所控制，分別是**並行查詢**和**並行存取插槽**。若要執行查詢，它必須在查詢並行限制和並行位置配置下執行。

- **並行查詢**就是在同一時間執行的查詢數目。SQL 資料倉儲在較大的 DW 大小 (DW1000 以上) 支援高達 32 個**並行查詢**。不過，由於並行查詢數目會因 DWU 數量而異，我們提供以下資料表來顯示依 DWU 的限制。
- **並行存取插槽**是更動態的概念。每個查詢可以取用一或多個並行位置。查詢取用的確切插槽數目取決於 SQL 資料倉儲的大小和查詢的[資源類別](#resource-classes)。

下表說明並行查詢和並行存取插槽的限制。

### 並行存取限制

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| 並行查詢上限 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 並行存取插槽上限 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 120 | 240 |

當符合上述其中一個臨界值時，新的查詢就會排入佇列。當其他查詢完成時，已排入佇列的查詢會以先進先出的基本原則執行，然後查詢與位置數目就會落到限制之下。

> [AZURE.NOTE]  只針對動態管理檢視 (DMV) 或目錄檢視執行的 SELECT 查詢**不**受任何並行限制所控管。這可讓使用者監視系統，不論在系統上執行的查詢數目多寡。

## 資源類別

資源類別是 SQL 資料倉儲工作負載管理中不可或缺的一部分，因為它們允許將更多記憶體或 CPU 週期配置給由指定使用者執行的查詢。有四種資源類別可以以**資料庫角色**的形式指派給使用者。這四種資源類別分別是 **smallrc、mediumrc、largerc 和 xlargerc**。smallrc 中的使用者會獲得較小量的記憶體，因此有較高的並行存取能力。相反地，指派給 xlargerc 的使用者會獲得大量記憶體，因此可以並行執行的查詢較少。

根據預設，每位使用者都是小型資源類別 smallrc 的成員。`sp_addrolemember` 程序可用來增加資源類別，`sp_droprolemember` 則可用來減少資源類別。例如，此命令會將 loaduser 的資源類別增加為 largerc︰

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

理想的作法是建立永久指派給資源類別的使用者，而不是變更使用者的資源類別。例如，在配置了更多記憶體時，叢集資料行存放區資料表的載入會建立更高品質的索引。若要確保載入可以取得更高的記憶體，請建立特別用來載入資料的使用者，並將使用者永久指派給較高的資源類別。

有幾種查詢不會受益於較大的記憶體配置，且系統會忽略其資源類別配置，並一律改為在小型資源類別中執行。強制這些查詢一律在小型資源類別中執行，可讓這些查詢在並行存取插槽承受壓力時執行，防止這些查詢耗用超過所需數量的插槽。這些[資源類別例外狀況](#resource-class-exceptions)會在本文稍後說明。

資源類別的其他詳細資料︰

- 必須有 `ALTER ROLE` 權限才能變更使用者的資源類別。
- 雖然使用者可以加入一或多個較高的資源類別，但使用者會採用指派給他們的最高資源類別的屬性。也就是說，如果使用者指派給 mediumrc 和 largerc，則會採用較高的資源類別 largerc。
- 無法變更系統管理使用者的資源類別。
 
本文結尾處的[管理使用者](#Managing-users)一節可以找到更多建立使用者並將他們指派給資源類別的詳細資料和範例。

## 記憶體配置

提升使用者的資源類別有利有弊。雖然提升使用者的資源類別可能表示其查詢可以取得更多記憶體，因而可以更快速地執行，但較高的資源類別也會減少可以執行的並行查詢數目。這是將大量記憶體配置給單一查詢，和允許其他也需要配置記憶體的查詢並行執行兩者間的取捨。如果提供一名使用者高記憶體配置進行查詢，其他使用者將無法存取該相同的記憶體以執行查詢。

下表依 DWU 和資源類別來對應所配置的記憶體與每個散發套件。在 SQL 資料倉儲中，每個資料庫有 60 個散發套件。例如，在 DW2000 上以 xlarge 資源類別執行的查詢可以在 60 個分散式資料庫內各取得 6,400 MB。

### 每個散發套件的記憶體配置 (MB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| smallrc | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| mediumrc | 100 | 200 | 200 | 400 | 400 | 400 | 800 | 800 | 800 | 1,600 | 1,600 | 3,200 |
| largerc | 200 | 400 | 400 | 800 | 800 | 800 | 1,600 | 1,600 | 1,600 | 3,200 | 3,200 | 6,400 |
| xlargerc | 400 | 800 | 800 | 1,600 | 1,600 | 1,600 | 3,200 | 3,200 | 3,200 | 6,400 | 6,400 | 12,800 |


使用上述相同範例，在全系統以 xlarge 資源類別於 DW2000 上執行的查詢，會配置總共 375 GB 的記憶體 (6,400 MB * 60 個散發套件 / 1,024 以轉換為 GB)。

### 全系統記憶體配置 (GB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
|smallrc | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |
|mediumrc | 6 | 12 | 12 | 23 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 188 |
|largerc | 12 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 375 |
|xlargerc | 23 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 188 | 375 | 375 | 750 |

## 並行存取插槽耗用量

如上所述，資源類別越高，授與的記憶體越多。因為記憶體是固定的資源，每個查詢所配置的記憶體越多，可支援的並行存取就越少。下表將所有上述的概念重述為單一檢視，顯示 DWU 的可用並行位置數目以及每個資源類別所取用的位置。

### 並行存取插槽的配置和耗用量

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :---------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| **配置** | | | | | | | | | | | | |
| 並行查詢上限 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 並行存取插槽上限 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 120 | 240 |
| **插槽耗用量** | | | | | | | | | | | | |
| smallrc | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 32 |
| largerc | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 64 |
| xlargerc | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 | 64 | 128 |

從這個表格中，您可以看出執行為 DW1000 的 SQL 資料倉儲提供總共 40 個並行位置給最多 32 個並行查詢。如果所有的使用者都以小型資源類別執行，將允許 32 個並行查詢，因為每個查詢會取用 1 個並行位置。如果所有的使用者都以中型資源類型執行，每位使用者將會配置每個散發套件 800 MB，總共 47 GB 的記憶體配置，而這些中型資源類型的使用者並行存取將限制為 8 位使用者。

## 查詢的重要性

在幕後，共有八個工作負載群組會控制資源類別的行為。不過，八個工作負載群組中只有四個可在任何給定的 DWU 中使用。這很合理，因為每個工作負載群組都會指派給 smallrc、mediumrc、largerc 或 xlargerc。了解這些幕後工作負載群組的重要性在於其中某些工作負載群組會設定為較高的**重要性**。重要性可用於 CPU 排程。以高重要性執行的查詢會取得比中度重要性的查詢高 3 倍的 CPU 週期。因此，並行存取插槽對應也會判斷 CPU 重要性。當查詢取用 16 個以上的插槽時，就會以高重要性執行。

以下是每個工作負載群組的重要性對應。

| 工作負載群組 | 並行存取插槽對應 | 重要性對應 |
| :------------------  | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | 中型 |
| SloDWGroupC01 | 2 | 中型 |
| SloDWGroupC02 | 4 | 中型 |
| SloDWGroupC03 | 8 | 中型 |
| SloDWGroupC04 | 16 | 高 |
| SloDWGroupC05 | 32 | 高 |
| SloDWGroupC06 | 64 | 高 |
| SloDWGroupC07 | 128 | 高 |

針對 DW500 SQL 資料倉儲，作用中的工作負載群組會對應至資源類別，如下所示。

| 資源類別 | 工作負載群組 | 已使用的並行存取插槽 | 重要性 |
| :--------------- | :------------- | :--------------------:   | :--------- |
| smallrc | SloDWGroupC00 | 1 | 中型 |
| mediumrc | SloDWGroupC02 | 4 | 中型 |
| largerc | SloDWGroupC03 | 8 | 中型 |
| xlargerc | SloDWGroupC04 | 16 | 高 |


下列 DMV 查詢可用來從資源管理員的觀點詳細查看記憶體資源配置的差異，或在進行疑難排解時分析工作負載群組的作用中和歷史使用量︰

```sql
WITH rg
AS
(   SELECT  
     pn.name						AS node_name
    ,pn.[type]						AS node_type
    ,pn.pdw_node_id					AS node_id
    ,rp.name						AS pool_name
    ,rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,wg.name						AS group_name
    ,wg.importance					AS group_importance
    ,wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,wg.max_dop						AS group_max_dop
    ,wg.effective_max_dop				AS group_effective_max_dop
    ,wg.total_request_count				AS group_total_request_count
    ,wg.total_queued_request_count			AS group_total_queued_request_count
    ,wg.active_request_count				AS group_active_request_count
    ,wg.queued_request_count				AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
    	    AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn	
            ON	wg.pdw_node_id	= pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

## 資源類別例外狀況

大多數查詢會採用資源類別，不過，有一些例外狀況。這通常發生於執行動作所需的資源不足。也就是例外狀況通常是查詢永遠不會利用更高的資源類別所配置的更高記憶體的情況。在這些情況下，一律會使用預設或小型資源類別 (smallrc)，而不考慮指派給使用者的資源類別。例如，`CREATE LOGIN` 一律會在 smallrc 中執行。執行此作業所需的資源非常少，因此沒必要將查詢納入並行存取插槽模型中。預先配置大量記憶體給這個動作太浪費。從並行存取插槽模型中排除 `CREATE LOGIN`，SQL 資料倉儲會更有效率。

下列陳述式**不**接受資源類別：

- CREATE 或 DROP TABLE
- ALTER TABLE ...SWITCH、SPLIT 或 MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE、UPDATE 或 DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE、ALTER 或 DROP USER
- CREATE、ALTER 或 DROP PROCEDURE
- CREATE 或 DROP VIEW
- INSERT VALUES
- 從系統檢視和 DMV 來 SELECT
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

### 採用並行存取限制的查詢

請務必記得，大部分的使用者查詢很可能由資源類別控管。一般來說，使用中的查詢工作負載必須落在並行查詢和並行存取插槽臨界值內，除非已被平台明確排除。一般使用者無法選擇從並行存取插槽模型中排除查詢。一旦超越任何一個臨界值，查訊就會開始排入佇列。排入佇列的查詢將根據提交時間的優先順序解決。

再次提醒您，下列陳述式會**採用**資源類別：

- INSERT-SELECT
- UPDATE
- 刪除
- SELECT (當查詢使用者資料表時)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT
- 載入資料
- 資料移動服務 (DMS) 進行的資料移動作業


## 管理使用者

1. **建立登入：**開啟 SQL 資料倉儲之**主要**資料庫的連接，並執行下列命令。
	
	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] 您最好在 Azure SQL Database 和 SQL 資料倉儲建立登入主要資料庫的使用者。此層級有兩個可用的伺服器角色，需要登入才能在主要資料庫中擁有使用者以授與成員資格。這些角色為 `Loginmanager` 和 `dbmanager`。在 Azure SQL Database 和 SQL 資料倉儲中，這些角色會授與管理登入以及建立資料庫的權限。這與 SQL Server 有所不同。如需詳細資料，請參閱[管理 Azure SQL Database 中的資料庫和登入][]。

2. **建立使用者帳戶︰**開啟 **SQL 資料倉儲資料庫**的連接，然後執行下列命令。

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **授與權限︰**下列範例會在 SQL 資料倉儲資料庫上授與 `CONTROL`。在資料庫層級的 `CONTROL` 相當於 SQL Server 中的 db\_owner。

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **增加資源類別︰**若要將使用者加入至增加工作負載管理角色，請使用下列查詢。

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **減少資源類別︰**若要從工作負載管理角色移除使用者，請使用下列查詢。

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] 您不可能從 smallrc 移除使用者。

## 已排入佇列的查詢偵測和其他 DMV

`sys.dm_pdw_exec_requests` DMV 可用來識別並行存取佇列中等候的查詢。

```sql
SELECT 	 r.[request_id]				 AS Request_ID
	,r.[status]				 AS Request_Status
	,r.[submit_time]			 AS Request_SubmitTime
	,r.[start_time]				 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

工作負載管理角色可以使用 `sys.database_principals` 來檢視。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

下列查詢會顯示每位使用者指派給哪些角色。

```sql
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL 資料倉儲具有下列等候類型。

- LocalQueriesConcurrencyResourceType：並行存取插槽架構之外的查詢。DMV 查詢及 `SELECT @@VERSION` 這類的系統函數是本機查詢的範例。
- UserConcurrencyResourceType：並行存取插槽架構之內的查詢。針對使用者資料表的查詢代表會使用此資源類型的範例。
- DmsConcurrencyResourceType：資料移動作業所產生的等候。
- BackupConcurrencyResourceType：此等候指出正在備份資料庫。此資源類型的最大值為 1。如果在同一時間要求多個備份，其他備份會排入佇列。

`sys.dm_pdw_waits` DMV 可用來查看要求正在等待的資源。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]			AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,	SESSION_ID()			AS Current_session
,	s.[status]			AS Session_status
,	s.[login_name]
,	s.[query_count]
,	s.[client_id]
,	s.[sql_spid]
,	r.[command]			AS Request_command
,	r.[label]
,	r.[status]			AS Request_status
,	r.[submit_time]
,	r.[start_time]
,	r.[end_compile_time]
,	r.[end_time]
,	DATEDIFF(ms,r.[submit_time],r.[start_time])		AS Request_queue_time_ms
,	DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,	DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,	r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV 只會顯示由指定查詢取用的資源等候。資源等候時間只會測量等候提供資源的時間，與訊號等候時間相反，後者是基礎 SQL Server 將查詢排程到 CPU 所需的時間。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE	[session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV 可用於等候的歷史趨勢分析。

```sql
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w;
```

## 後續步驟

如需管理資料庫使用者和安全性的詳細資訊，請參閱[保護 SQL 資料倉儲中的資料庫][]。如需更大的資源類別如何改善叢集資料行存放區索引品質的詳細資訊，請參閱[重建索引以提升區段品質]。

<!--Image references-->

<!--Article references-->
[保護 SQL 資料倉儲中的資料庫]: ./sql-data-warehouse-overview-manage-security.md
[重建索引以提升區段品質]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality

<!--MSDN references-->
[管理 Azure SQL Database 中的資料庫和登入]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0720_2016-->