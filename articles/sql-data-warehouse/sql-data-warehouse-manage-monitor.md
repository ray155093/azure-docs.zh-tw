<properties
   pageTitle="使用 DMV 監視工作負載 | Microsoft Azure"
   description="了解如何使用 DMV 監視工作負載。"
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
   ms.date="07/22/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# 使用 DMV 監視工作負載

本文說明如何使用動態管理檢視 (DMV)，在 Azure SQL 資料倉儲中監視工作負載及調查查詢執行。

## 監視連接

[Sys.dm\_pdw\_exec\_sessions][] 檢視可讓您監視 Azure SQL 資料倉儲資料庫的連接。此檢視包含作用中工作階段，以及最近中斷連接之工作階段的歷程記錄。Session\_id 是此檢視的主索引鍵，並依序指派給每個新的登入。

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## 監視查詢執行

若要監視執行查詢，請以 [sys.dm\_pdw\_exec\_requests][] 開始。此檢視包含查詢進度，以及最近完成之查詢的歷程記錄。Request\_id 專門識別每個查詢，而且是此檢視的主索引鍵。Request\_id 依序指派給每個新的查詢。針對指定的 session\_id 查詢這個資料表，即會顯示指定登入的所有查詢。

>[AZURE.NOTE] 預存程序會使用多個 request\_id。將循序指派要求 id。

請遵循以下步驟來調查特定查詢的查詢執行計畫和時間。

### 步驟 1︰識別您想要調查的查詢

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;
```

從上述的查詢結果中，記下您想要調查之查詢的**要求 ID**。

「暫止」狀態中的查詢會因為並行限制 ([並行存取和工作負載管理][]中有詳細說明) 而進入佇列。這些查詢也會顯示在 sys.dm\_pdw\_waits 等候查詢中，類型為 UserConcurrencyResourceType。查詢也會因其他原因 (例如鎖住) 等候。如果您的查詢正在等候資源，請參閱[檢查查詢是否正在等候資源][]。

### 步驟 2：尋找查詢計劃的最長執行步驟

使用要求 ID，從 [sys.dm\_pdw\_request\_steps][] 擷取查詢計畫步驟的清單。藉由查看總經過時間，尋找長時間執行的步驟。

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

檢查長時間執行查詢步驟的 *operation\_type* 資料行，並且記下**步驟索引**：

- 針對下列 **SQL 作業**繼續執行步驟 3a：OnOperation、RemoteOperation、ReturnOperation。
- 針對下列**資料移動作業**繼續執行步驟 3b：ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation。

### 步驟 3a：尋找 SQL 步驟的執行進度

使用要求 ID 及步驟索引，從 [sys.dm\_pdw\_sql\_requests][] 擷取詳細資料，其中包含每個分散式 SQL Server 執行個體上查詢執行的資訊。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

如果查詢正在執行，則可以使用 [DBCC PDW\_SHOWEXECUTIONPLAN][] 針對特定散發內目前執行中的 SQL 步驟從 SQL Server 計畫快取擷取 SQL Server 預估的計畫。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### 步驟 3b：尋找 DMS 步驟的執行進度

使用要求 ID 和步驟索引，從 [sys.dm\_pdw\_dms\_workers][] 擷取在每個散發上執行的資料移動步驟的相關資訊。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- 檢查 *total\_elapsed\_time* 資料行，查看是否有特定散發，在資料移動上比其他散發用了更多時間。
- 如果是長時間執行的散發，請檢查 *rows\_processed* 資料行，查看從該散發移動的資料列數是否遠多過其他散發。若是如此，這可能表示基礎資料的扭曲。

如果查詢正在執行，則可以使用 [DBCC PDW\_SHOWEXECUTIONPLAN][] 針對特定散發內目前執行中的 SQL 步驟從 SQL Server 計畫快取擷取 SQL Server 預估的計畫。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## 監視等候中的查詢

如果您發現您的查詢因為正在等候資源而沒有進度，以下查詢可顯示查詢正在等候的所有資源。

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

如果查詢正在主動等候另一個查詢的資源，則狀態會是 **AcquireResources**。如果查詢具有全部的所需資源，則狀態會是 **Granted**。

## 後續步驟
如需有關動態管理檢視 (DMV) 的詳細資訊，請參閱[系統檢視][]。如需管理 SQL 資料倉儲的秘訣，請參閱[管理概觀][]。如需最佳作法，請參閱 [SQL 資料倉儲最佳作法][]。

<!--Image references-->

<!--Article references-->
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[SQL 資料倉儲最佳作法]: ./sql-data-warehouse-best-practices.md
[系統檢視]: ./sql-data-warehouse-reference-tsql-system-views.md
[並行存取和工作負載管理]: ./sql-data-warehouse-develop-concurrency.md
[檢查查詢是否正在等候資源]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[Sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0810_2016---->