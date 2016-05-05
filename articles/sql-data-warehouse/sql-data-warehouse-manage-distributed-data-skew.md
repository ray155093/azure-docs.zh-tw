<properties
   pageTitle="在 Azure SQL 資料倉儲中管理散發資料表的資料扭曲 | Microsoft Azure"
   description="當資料列不平均地散發到 Azure SQL 資料倉儲中雜湊散發資料表的所有散發時，尋找並修正資料扭曲。" 
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
   ms.date="04/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 在 Azure SQL 資料倉儲中管理散發資料表的資料扭曲
本教學課程會識別雜湊散發資料表中的資料扭曲，並提供修正問題的建議。

資料表的資料是使用雜湊散發方法來散發時，有些散發可能會扭曲，會比其他資料表具有更多資料。過多資料扭曲可能會影響查詢效能，因為在執行時間最長的散發完成之前，散發查詢的最終結果都無法就緒。視資料扭曲的程度而定，您可能需要處理它。

在本教學課程中，您將：

- 使用中繼資料來判斷哪些資料表有資料扭曲
- 了解判斷何時可解決資料扭曲的秘訣
- 重新建立資料表以解決資料扭曲

## DBCC PDW\_SHOWSPACEUSED

其中一種識別資料扭曲的方法是使用 [DBCC PDW\_SHOWSPACEUSED()][]

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

這個非常快速且簡單的方式，用來查看儲存在您資料庫中每 60 個散發內的資料表資料列數目。請記住，為了達到最平衡的效能，分散式資料表中的資料列應該平均分散到所有散發中。

不過，如果您查詢 Azure SQL 資料倉儲動態管理檢視 (DMV)，則可以執行更詳細的分析。本文的其餘部分會顯示如何執行這項作業。

## 步驟 1︰建立檢視，尋找資料扭曲

建立此檢視來識別哪些資料表有資料扭曲。

```sql
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
SELECT 
	SUBSTRING(@@version,34,4)															AS  [build_number]
,	GETDATE()																			AS  [execution_time]
,	DB_NAME()																			AS  [database_name]
,	s.name																				AS  [schema_name]
,	t.name																				AS  [table_name]
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)												AS  [two_part_name]
,	nt.[name]																			AS  [node_table_name]
,	ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))					AS  [node_table_name_seq]
,	tp.[distribution_policy_desc]														AS  [distribution_policy_name]
,	nt.[distribution_id]																AS  [distribution_id]
,	nt.[pdw_node_id]																	AS  [pdw_node_id]
,	pn.[type]																			AS	[pdw_node_type]
,	pn.[name]																			AS	[pdw_node_name]
,	nps.[partition_number]																AS	[partition_nmbr]
,	nps.[reserved_page_count]															AS	[reserved_space_page_count]
,	nps.[reserved_page_count] - nps.[used_page_count]									AS	[unused_space_page_count]
,	nps.[in_row_data_page_count] 
	+ nps.[row_overflow_used_page_count] + nps.[lob_used_page_count]					AS  [data_space_page_count]
,	nps.[reserved_page_count] 
	- (nps.[reserved_page_count] - nps.[used_page_count]) 
	- ([in_row_data_page_count]+[row_overflow_used_page_count]+[lob_used_page_count])	AS  [index_space_page_count]
,	nps.[row_count]																		AS  [row_count]
from sys.schemas s
join sys.tables t								ON	s.[schema_id]			= t.[schema_id]
join sys.pdw_table_distribution_properties	tp	ON	t.[object_id]			= tp.[object_id]
join sys.pdw_table_mappings tm					ON	t.[object_id]			= tm.[object_id]
join sys.pdw_nodes_tables nt					ON	tm.[physical_name]		= nt.[name]
join sys.dm_pdw_nodes pn 						ON  nt.[pdw_node_id]		= pn.[pdw_node_id]
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.[object_id]			= nps.[object_id]
												AND nt.[pdw_node_id]		= nps.[pdw_node_id]
												AND nt.[distribution_id]	= nps.[distribution_id]
)
, size
AS
(
SELECT	[build_number]
,		[execution_time]
,		[database_name]
,		[schema_name]
,		[table_name]
,		[two_part_name]
,		[node_table_name]
,		[node_table_name_seq]
,		[distribution_policy_name]
,		[distribution_id]
,		[pdw_node_id]
,		[pdw_node_type]
,		[pdw_node_name]
,		[partition_nmbr]
,		[reserved_space_page_count]
,		[unused_space_page_count]
,		[data_space_page_count]
,		[index_space_page_count]
,		[row_count]
,		([reserved_space_page_count] * 8.0)				AS [reserved_space_KB]
,		([reserved_space_page_count] * 8.0)/1000		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1000000		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1000000000	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1000		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1000000		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1000000000	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1000		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1000000		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1000000000	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1000		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1000000		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1000000000	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

## 步驟 2︰查詢檢視

現在您已建立檢視，請執行這個範例查詢來識別哪些資料表有資料扭曲。

```sql
SELECT	[two_part_name]
,		[distribution_id]
,		[row_count]
,		[reserved_space_GB]
,		[unused_space_GB]
,		[data_space_GB]
,		[index_space_GB]
FROM	[dbo].[vDistributionSkew]
WHERE	[table_name] = 'FactInternetSales'
ORDER BY [row_count] DESC
```

>[AZURE.NOTE] 已散發 ROUND\_ROBIN 的資料表不應被扭曲。依照設計，資料會平均散發於節點上。

## 步驟 3︰決定您是否應該解決資料扭曲

若要決定是否應該解決資料表中的資料扭曲，您應該盡可能了解工作負載中的資料磁區和查詢。

散發資料就是找出將資料扭曲降至最低與將資料移動降至最低兩者之間的正確平衡點。這些可能是相反的目標，有時候您會想要保留資料扭曲，以減少資料移動。比方說，當散發資料行經常是聯結和彙總中的共用資料行時，您便會將資料移動降至最低。擁有最少的資料移動，所帶來的好處可能勝過具有資料扭曲的影響。

## 步驟 4︰解決資料扭曲

解決資料扭曲有兩個可行的方式。如果您已決定應該解決扭曲，請使用其中一種。

### 方法 1︰重新建立具有不同散發資料行的資料表

解決資料扭曲的一般方式，是重新建立具有不同散發資料行的資料表。如需選取雜湊散發資料行的指引，請參閱[雜湊散發][]。此範例會使用 [CTAS][] 來重新建立具有不同散發資料行的資料表。

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### 方法 2︰使用循環配置資源散發重新建立資料表

此範例會使用循環配置資源來重新建立資料表，而不是使用雜湊散發。這項變更將會產生平均的資料散發。不過，它通常會增加查詢的資料移動。

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## 後續步驟
如需資料表散發的詳細資訊，請參閱下列文章：

* [資料表設計][]
* [雜湊散發][]

<!--Image references-->

<!--Article references-->
[資料表設計]: sql-data-warehouse-develop-table-design.md
[雜湊散發]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->
[DBCC PDW\_SHOWSPACEUSED()]: https://msdn.microsoft.com/zh-TW/library/mt204028.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->