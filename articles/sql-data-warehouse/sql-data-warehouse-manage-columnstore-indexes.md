<properties
   pageTitle="管理 Azure SQL 資料倉儲中的資料行存放區索引 | Microsoft Azure"
   description="管理資料行存放區索引，以改善 Azure SQL 資料倉儲中的壓縮和查詢效能的教學課程。"
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
   ms.date="04/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 管理 Azure SQL 資料倉儲中的資料行存放區索引

本教學課程說明如何管理資料行存放區索引，以改進查詢效能。

當索引將資料列一起壓縮為一百萬個資料列 (確切數字為 1,048,576) 的「資料列群組」時，可在資料行存放區索引上達到最佳的查詢執行效能。這可提供最佳的壓縮和最佳的查詢效能。不過，可能會發生導致資料列群組大幅少於一百萬個資料列的狀況。當資料列群組未密集填入資料列時，您應該考慮進行調整。

在本教學課程中，您將了解如何：

- 使用中繼資料來判斷每個資料列群組的資料列平均數目
- 請思考未密集填入資料列群組的根本原因
- 重建資料行存放區索引，將所有資料列重新壓縮至新的資料列群組中 

若要了解有關資料行存放區索引的基本概念，請參閱[資料行存放區指南](https://msdn.microsoft.com/library/gg492088.aspx)。

## 步驟 1：建立顯示資料列群組中繼資料的檢視

此檢視會計算每個資料列群組的平均資料列。它也會顯示資料列群組的其他資訊。

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

## 步驟 2︰查詢檢視

既然您已經建立檢視，請執行此範例查詢以查看您的資料行存放區索引的資料列群組中繼資料。

```sql
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## 步驟 3︰分析結果

一旦您執行查詢，就可以開始查看資料，並分析您的結果。此表格會說明在您的資料列群組分析中要尋找的項目。


| 欄 | 如何使用這項資料 |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | 如果資料表已分割，您可能會預期看到較高的開放資料列群組計數。散發套件中的每個分割在理論上有與其相關聯的開放資料列群組。將這個因素納入您的分析。已分割的小型資料表可以藉由移除分割進行最佳化，因為這樣會改善壓縮。 |
| [row\_count\_total] | 資料表的資料列計數。例如，您可以使用此值來計算資料列的百分比 (壓縮的狀態)。 |
| [row\_count\_per\_distribution\_MAX] | 如果所有資料列平均分配，這個值會是每個散發的目標資料列數目。比較此值與 compressed\_rowgroup\_count。 |
| [COMPRESSED\_rowgroup\_rows] | 資料表的資料行存放區格式中的資料列總數。 |
| [COMPRESSED\_rowgroup\_rows\_AVG] | 如果平均資料列數目遠小於資料群組最大的資料列數目，則可考慮使用 CTAS 或 ALTER INDEX REBUILD 重新壓縮資料 |
| [COMPRESSED\_rowgroup\_count] | 資料行存放區格式中的資料列群組數目。如果相對於資料表的這個數目很高，表示資料行存放區密度很低。 |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | 資料行存放區格式中的資料列會以邏輯方式刪除。如果相對於資料表大小的這個數目很高，請考慮重新建立分割或重建索引，因為這樣會將其實際移除。 |
| [COMPRESSED\_rowgroup\_rows\_MIN] | 將它與 AVG 和 MAX 資料行搭配使用，以了解資料行存放區中資料列群組的值範圍。載入臨界值上較低的數目 (每個分割對齊散發套件 102,400) 表示資料載入可進行最佳化 |
| [COMPRESSED\_rowgroup\_rows\_MAX] | 同上。 |
| [OPEN\_rowgroup\_count] | 開放資料列群組都正常。每個資料表散發都應該有一個開放資料列群組 (60)。過多的數目表示資料跨分割載入。重複檢查分割策略並確定它是正確的 |
| [OPEN\_rowgroup\_rows] | 每個資料列群組可以有最多 1,048,576 個資料列。使用這個值查看開放資料列群組目前的飽和度 |
| [OPEN\_rowgroup\_rows\_MIN] | 開放群組會指出資料是緩慢移動載入資料表，或是先前的載入將剩餘的資料列溢出到此資料列群組。使用 MIN、MAX、AVG 欄位查看多少資料位於開放資料列群組。對於小型資料表，可能是所有資料的 100%！ 在此情況下，ALTER INDEX REBUILD 來強制資料進入資料行存放區。 |
| [OPEN\_rowgroup\_rows\_MAX] | 同上。 |
| [OPEN\_rowgroup\_rows\_AVG] | 同上。 |
| [CLOSED\_rowgroup\_rows] | 查看關閉的資料列群組資料列做為例行性檢查。如果 |
| [CLOSED\_rowgroup\_count] | 如果發現任何關閉資料列群組，其數目應該很小。關閉資料列群組可以使用 ALTER INDEX 轉換成壓縮資料列群組...REORGANISE 命令。不過，通常並不需要。關閉群組會透過背景 "tuple mover" 程序自動轉換成資料行存放區的資料列群組。 |
| [CLOSED\_rowgroup\_rows\_MIN] | 關閉資料列群組應該具有極高的填滿率。如果關閉資料列群組的填滿率很低，就需要進一步分析資料行存放區。 |
| [CLOSED\_rowgroup\_rows\_MAX] | 同上。 |
| [CLOSED\_rowgroup\_rows\_AVG] | 同上。 |


## 步驟 4︰檢查根本原因

檢查根本原因可協助您了解您是否可以進行資料表設計、載入或其他可能改善資料列群組中資料列密度的程序變更；藉此改善壓縮和查詢效能。

這些因素可能會導致資料行存放區索引在每個資料列群組中的資料列大幅少於 1,048,576 個。它們也會造成資料列移至差異資料列群組，而不是壓縮的資料列群組。

1. 繁重的 DML 作業
2. 小型或緩慢移動的載入作業
3. 太多資料分割

### 繁重的 DML 作業** 

更新和刪除資料列之繁重的 DML 作業，會造成資料行存放區沒有效率。這在資料列群組中大部分的資料列都已修改時，更是如此。

- 從壓縮的資料列群組刪除資料列僅會以邏輯方式將資料列標示為已刪除。資料列會保留在壓縮的資料列群組中，直到重建資料分割或資料表為止。
- 插入資料列會將資料列新增至名為差異資料列群組的內部資料列存放區資料表。在差異資料列群組已滿且標示為已關閉之前，插入的資料列不會轉換成資料行存放區。一旦達到 1,048,576 個資料列的容量上限，資料列群組就會關閉。 
- 更新資料行存放區格式的資料列會做為邏輯刪除和插入來處理。插入的資料列會儲存在差異存放區。

超出已對齊分佈之每個資料分割 102,400 個資料列大量臨界值的批次更新和插入作業，將會直接寫入資料行存放區格式。不過，假設在平均分佈情況下，您將需要在單一作業中修改超過 6.144 百萬個資料列才會發生這種情況。如果對齊分佈之資料分割的給定資料列數目少於 102,400 個，資料列將會移至差異存放區，且在插入足夠的資料列、修改資料列以關閉資料列群組或已建立索引之前，都會存放於差異存放區。

### 小型或緩慢移動的載入作業

流入 SQL 資料倉儲的小型負載，有時也稱為緩慢移動的負載。它們通常代表系統接近連續擷取的串流。不過，因為這個串流已接近連續狀態，所以資料列的容量並沒有特別大。通常資料遠低於直接載入資料行存放區格式所需的閾值。

在這些情況下，最好先將資料登陸到 Azure Blob 儲存體中，並讓它在載入之前累積。這項技術通常稱為*微批次處理*。

### 太多資料分割

您可能有太多的資料分割。在大量平行處理 (MPP) 架構中，使用者定義的資料表是以 60 個資料表的形式分散與實作。因此，每個資料行存放區索引會實作為 60 個資料行存放區索引。同樣地，每個資料分割會跨這 60 個資料行存放區索引實作。這與 SQL Server 有**很大的差別**，SQL Server 具有對稱式多處理 (SMP) 架構。

如果您將 1,048,576 個資料列載入 SMP SQL Server 資料表的一個資料分割中，它會壓縮到資料行存放區中。不過，如果您將 1,048,576 個資料列載入 SQL 資料倉儲資料表的一個資料分割時，60 個分布區域的每一個分布區域僅會有 17,467 個資料列登陸 (假設資料平均散發)。由於 17,467 少於每個分散 102,400 個資料列的閾值，所以 SQL 資料倉儲會將資料保留在差異存放區資料列群組中。因此，若要直接將資料列載入壓縮的資料行存放區格式，您必須在 SQL 資料倉儲資料表的單一資料分割中載入超過 6.1444 百萬個資料列 (60 x 102,400)。

## 步驟 5：配置額外的計算資源

重建索引 (特別是在大型資料表上) 通常需要額外的資源。SQL 資料倉儲有工作負載管理功能，可以將更多的記憶體配置給使用者。若要了解如何保留更多記憶體給您的索引重建，請參閱[並行][]一文的工作負載管理一節。

## 步驟 6：重建索引以改善每個資料列群組的平均資料列數目

若要將現有的壓縮資料列群組與強制差異資料列群組合併至資料行存放區，您需要重建索引。通常有太多資料而無法重建完整的索引，因此最好重建一個或多個資料分割。在 SQL 資料倉儲中，您可以用下列兩種方式之一重建索引：

1. 使用 [ALTER INDEX][] 重建資料分割。
2. 使用 [CTAS][] 以新的表格重新建立資料分割，然後使用資料分割切換來將資料分割移回原始的資料表。

哪一種方式最好？ 針對大量的資料，[CTAS][] 的速度通常比 [ALTER INDEX][] 來得快。針對較小量的資料，[ALTER INDEX][] 較為容易使用。

### 方法 #1：使用 ALTER INDEX 離線重建小量資料

這些範例示範如何重建整個資料行存放區索引和重建單一資料分割。在大型資料表上，適合重建單一資料分割。這是一種離線作業。

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

如需詳細資訊，請參閱[資料行存放區索引重組](https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1)中的＜ALTER INDEX REBUILD＞一節和語法主題 [ALTER INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188388.aspx)。

### 方法 #2：使用 CTAS 線上重建大量資料的資料分割切換

這個範例會使用 [CTAS][] 和資料分割切換重建資料表分割區。


```sql
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

如需使用 `CTAS` 重新建立資料分割的更多詳細資料，請參閱[資料表分割][]和[並行][]。


## 後續步驟

如需索引管理的詳細建議，請檢閱[管理索引][]一文。

如需更多管理祕訣，請前往[管理][]概觀。

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[資料表分割]: sql-data-warehouse-develop-table-partitions.md
[並行]: sql-data-warehouse-develop-concurrency.md
[管理]: sql-data-warehouse-manage-monitor.md
[管理索引]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->