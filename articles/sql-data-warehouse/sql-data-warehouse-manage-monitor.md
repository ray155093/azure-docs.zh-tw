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
   ms.date="08/28/2016"
   ms.author="sonyama;barbkess"/>

# 使用 DMV 監視工作負載

本文說明如何使用動態管理檢視 (DMV)，在 Azure SQL 資料倉儲中監視工作負載及調查查詢執行。

## 監視連接

所有的 SQL 資料倉儲登入都會記錄到 [sys.dm\_pdw\_exec\_sessions][]。這個 DMV 會包含最後 10,000 筆登入。Session\_id 是主索引鍵，並依序指派給每個新的登入。

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## 監視查詢執行

SQL 資料倉儲上執行的所有查詢會都記錄到 [sys.dm\_pdw\_exec\_requests][]。這個 DMV 會包含最後 10,000 筆執行的查詢。Request\_id 可唯一識別每筆查詢，而且是此 DMV 的主索引鍵。Request\_id 會依序指派給每筆新查詢，並加上 QID 代表查詢識別碼。查詢此 DMV 來尋找指定的 session\_id，即會顯示指定登入的所有查詢。

>[AZURE.NOTE] 預存程序會使用多個要求 ID。要求 ID 是依序指派。

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

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

從前述的查詢結果中，記下您想要調查之查詢的**要求 ID**。

狀態的查詢會因為並行限制而進入佇列。這些查詢也會顯示在 sys.dm\_pdw\_waits 等候查詢中，類型為 UserConcurrencyResourceType。如需並行限制的詳細資訊，請參閱[並行和工作負載管理][]。查詢也會因其他原因 (例如物件鎖定) 而等候。如果您的查詢正在等候資源，請參閱本文稍後的[檢查查詢是否正在等候資源][]。

若要簡化 sys.dm\_pdw\_exec\_requests 資料表中查詢的查詢方式，請使用可在 sys.dm\_pdw\_exec\_requests 檢視中查詢的[標籤][]為您的查詢指派註解。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### 步驟 2︰ 調查查詢計劃

使用要求 ID，從 [sys.dm\_pdw\_request\_steps][] 擷取查詢的分散式 SQL (DSQL) 計劃。

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

當 DSQL 計劃所花的時間超出預期時，有可能是含有許多 DSQL 步驟的複雜計劃所導致，或只是某個步驟需要長時間處理。如果計劃是含有數個移動作業的許多步驟，請考慮最佳化您的資料表散發以減少資料移動。 [資料表散發][]一文說明為何需要移動資料才能解決查詢，並說明最小化資料移動的一些散發策略。

若要進一步調查單一步驟 (長時間執行查詢步驟的 *operation\_type* 資料行) 的詳細資料，並且記下**步驟索引**：

- 針對下列 **SQL 作業**繼續執行步驟 3a：OnOperation、RemoteOperation、ReturnOperation。
- 針對下列**資料移動作業**繼續執行步驟 3b：ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation。

### 步驟 3a︰調查分散式資料庫上的 SQL

使用要求 ID 及步驟索引，從 [sys.dm\_pdw\_sql\_requests][] 擷取詳細資料，其中包含所有分散式資料庫上的查詢步驟的執行資訊。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

如果查詢步驟正在執行，則可以使用 [DBCC PDW\_SHOWEXECUTIONPLAN][] 針對特定散發內執行中的步驟從 SQL Server 計劃快取擷取 SQL Server 預估的計劃。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### 步驟 3b︰調查分散式資料庫的資料移動

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
[資料表散發]: ./sql-data-warehouse-tables-distribute.md
[並行和工作負載管理]: ./sql-data-warehouse-develop-concurrency.md
[檢查查詢是否正在等候資源]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[標籤]: https://msdn.microsoft.com/library/ms190322.aspx

<!---HONumber=AcomDC_0831_2016-->