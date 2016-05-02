<properties
   pageTitle="管理 Azure SQL 資料倉儲中的資料表大小 | Microsoft Azure"
   description="了解您資料表和資料分割的大小。" 
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
   ms.date="04/13/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 管理 Azure SQL 資料倉儲中的資料表大小

本文識別資料表分割區的大小，並幫助您了解資料庫的大小。


## 步驟 1︰建立可彙總資料表大小的檢視

建立此檢視來識別哪些資料表有資料扭曲。

```sql
CREATE VIEW dbo.vPartitionSizing
AS

WITH base
AS
(
SELECT 
	SUBSTRING(@@version,34,4)																AS build_number
,	DB_NAME()																				AS database_name
,	s.name																					AS schema_name
,	t.name																					AS table_name
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)													AS two_part_name
,	nt.name																					AS node_table_name
,   i.[type]                                                                                AS table_type
,   i.[type_desc]                                                                           AS table_type_desc
,	tp.distribution_policy_desc	                        									AS distribution_policy_name
,	pn.pdw_node_id                                                                          AS pdw_node_id
,	pn.[type]																				AS pdw_node_type
,	pn.name																					AS pdw_node_name
,   di.distribution_id                                                                      AS dist_id
,   di.name                                                                                 AS dist_name
,   di.position                                                                             AS dist_position
,	nps.partition_number																	AS partition_nmbr
,	nps.reserved_page_count																	AS reserved_space_page_count
,	nps.reserved_page_count - nps.used_page_count											AS unused_space_page_count
,	nps.in_row_data_page_count + nps.row_overflow_used_page_count + nps.lob_used_page_count	AS  data_space_page_count
,	nps.reserved_page_count 
	- (nps.reserved_page_count - nps.used_page_count) 
	- (in_row_data_page_count+row_overflow_used_page_count+lob_used_page_count)				AS index_space_page_count
,	nps.[row_count]                                                                         AS row_count
from sys.schemas s
join sys.tables t								ON	s.schema_id			= t.schema_id
JOIN sys.indexes i                              ON  t.object_id         = i.object_id
                                                AND i.index_id          <= 1
join sys.pdw_table_distribution_properties	tp	ON	t.object_id			= tp.object_id
join sys.pdw_table_mappings tm					ON	t.object_id			= tm.object_id
join sys.pdw_nodes_tables nt					ON	tm.physical_name	= nt.name
join sys.dm_pdw_nodes pn 						ON  nt.pdw_node_id		= pn.pdw_node_id
join sys.pdw_distributions di                   ON  nt.distribution_id  = di.distribution_id
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.object_id		= nps.object_id
												AND nt.pdw_node_id		= nps.pdw_node_id
                                                AND nt.distribution_id  = nps.distribution_id
)
, size
AS
(
SELECT	build_number
,		database_name
,		schema_name
,		table_name
,		two_part_name
,		node_table_name
,       table_type
,       table_type_desc
,		distribution_policy_name
,       dist_id
,       dist_name
,       dist_position
,		pdw_node_id
,		pdw_node_type
,		pdw_node_name
,		partition_nmbr
,		reserved_space_page_count
,		unused_space_page_count
,		data_space_page_count
,		index_space_page_count
,		row_count
,		(reserved_space_page_count * 8.0)				AS reserved_space_KB
,		(reserved_space_page_count * 8.0)/1000			AS reserved_space_MB
,		(reserved_space_page_count * 8.0)/1000000		AS reserved_space_GB
,		(reserved_space_page_count * 8.0)/1000000000	AS reserved_space_TB
,		(unused_space_page_count * 8.0)					AS unused_space_KB
,		(unused_space_page_count * 8.0)/1000			AS unused_space_MB
,		(unused_space_page_count * 8.0)/1000000			AS unused_space_GB
,		(unused_space_page_count * 8.0)/1000000000		AS unused_space_TB
,		(data_space_page_count * 8.0)					AS data_space_KB
,		(data_space_page_count * 8.0)/1000				AS data_space_MB
,		(data_space_page_count * 8.0)/1000000			AS data_space_GB
,		(data_space_page_count * 8.0)/1000000000		AS data_space_TB
,		(index_space_page_count * 8.0)	    			AS index_space_KB
,		(index_space_page_count * 8.0)/1000				AS index_space_MB
,		(index_space_page_count * 8.0)/1000000			AS index_space_GB
,		(index_space_page_count * 8.0)/1000000000		AS index_space_TB
FROM	base
)
SELECT	* 
FROM	size
;
```

## 步驟 2︰查詢檢視

現在，您已建立檢視，請執行這些範例查詢來深入了解資料表大小︰

### 資料庫中所用資料表空間的摘要

```sql
SELECT
	database_name
,	SUM(row_count)				as total_row_count
,	SUM(reserved_space_MB)		as total_reserved_space_MB
,	SUM(data_space_MB)			as total_data_space_MB
,	SUM(index_space_MB)			as total_index_space_MB
,	SUM(unused_space_MB)		as total_unused_space_MB
FROM dbo.vPartitionSizing
GROUP BY database_name
;
```

### 資料表所使用的資料表空間

```sql
SELECT 
	two_part_name
,	SUM(row_count)				as table_row_count
,	SUM(reserved_space_GB)		as table_reserved_space_GB
,	SUM(data_space_GB)			as table_data_space_GB
,	SUM(index_space_GB)			as table_index_space_GB
,	SUM(unused_space_GB)		as table_unused_space_GB
FROM dbo.vPartitionSizing
GROUP BY two_part_name
;
```

### 依資料表幾何類型的資料表空間

```sql
SELECT 
   table_type_desc
,	SUM(row_count)				as table_type_row_count
,	SUM(reserved_space_GB)		as table_type_reserved_space_GB
,	SUM(data_space_GB)			as table_type_data_space_GB
,	SUM(index_space_GB)			as table_type_index_space_GB
,	SUM(unused_space_GB)		as table_type_unused_space_GB
FROM dbo.vPartitionSizing
GROUP BY table_type_desc
;
```

### 依散發的資料表空間

```sql
SELECT 
	pdw_node_id
,	distribution_policy_name
,	dist_name
,	SUM(row_count)				as total_node_distribution_row_count
,	SUM(reserved_space_MB)		as total_node_distribution_reserved_space_MB
,	SUM(data_space_MB)			as total_node_distribution_data_space_MB
,	SUM(index_space_MB)			as total_node_distribution_index_space_MB
,	SUM(unused_space_MB)		as total_node_distribution_unused_space_MB
FROM dbo.vPartitionSizing
GROUP BY 	distribution_policy_name
,			dist_name
,			pdw_node_id
ORDER BY    pdw_node_id
,           distribution_policy_name
,		    dist_name
;
```

## 步驟 3︰管理資料表

您是否發現一些不再需要的資料表？ 請考慮卸除它們，或使用 [CREATE EXTERNAL TABLE AS SELECT (CETAS)][] 將它們匯出至 Blob 儲存體。

有些資料表是否大於您想像的大小？ 您可以考慮分割它們。如需詳細資訊，請參閱 [資料表分割區][] 文章。

您是否將達到設定的最大資料庫大小？ 請使用 [ALTER DATABASE][] 進行變更。

## 後續步驟
如需資料表散發的詳細資訊，請參閱下列文章：

* [資料表設計][]
* [雜湊散發][]

<!--Image references-->

<!--Article references-->
[資料表設計]: sql-data-warehouse-develop-table-design.md
[雜湊散發]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->