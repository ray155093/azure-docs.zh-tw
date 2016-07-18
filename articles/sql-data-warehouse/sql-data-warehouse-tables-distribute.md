<properties
   pageTitle="在 SQL 資料倉儲中散發資料表 | Microsoft Azure"
   description="開始在 Azure SQL 資料倉儲中散發資料表。"
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 在 SQL 資料倉儲中散發資料表

> [AZURE.SELECTOR]
- [概觀][]
- [資料類型][]
- [散發][]
- [Index][]
- [資料分割][]
- [統計資料][]
- [暫存][]

SQL 資料倉儲是大量平行處理 (MPP) 分散式資料庫系統。將資料和處理能力分割於多個節點之間，SQL 資料倉儲就能夠提供極大的延展性 - 遠超過任何單一系統。決定如何在 SQL 資料倉儲內散發資料是達到最佳效能的最重要因素之一。要達到最佳效能的關鍵是將資料移動降至最低，而將資料移動降至最低的關鍵是選取正確的散發策略。

## 選擇散發方法

SQL 資料倉儲會在幕後將您的資料分成 60 個資料庫。每個個別的資料庫都稱為**散發**。當資料載入每個資料表時，SQL 資料倉儲必須知道如何將資料分成這 60 個散發。

散發方法會定義於資料表層級，目前有兩個選擇︰

1. **循環配置資源**會平均但隨機散發資料。
2. **雜湊散發**會根據單一資料行的雜湊值散發資料

根據預設，如果未定義資料散發方式，您的資料表會使用**循環配置資源**散發方法進行散發。不過，當您日益熟練實作，您會考慮使用**雜湊散發**資料表將資料移動降至最低，進而使查詢效能達到最佳化。

### 循環配置資源資料表

使用循環配置資源方法來散發資料名符其實。載入資料後，每個資料列只會傳送到下一個散發。此種資料散發方法一律會將資料非常平均地隨機散發到所有的散發。也就是說，在放置資料的循環配置資源處理期間，不會進行任何排序。基於這個理由，循環配置資源散發有時稱為隨機雜湊。使用循環配置資源分散式資料表，不需要了解資料。基於這個理由，循環配置資源資料表通常具有良好的載入目標。

根據預設，如果未選擇散發方法，則會使用循環配置資源散發方法。不過，雖然循環配置資源資料表很容易使用，但因為資料會隨機分散於系統，這表示系統無法保證每個資料列位於哪個發散。因此，系統有時候需要叫用資料移動作業，才能在解析查詢前，更加妥善地組織您的資料。此額外步驟會使您的查詢變慢。

在下列情況下，請考慮對資料表使用循環配置資源散發：

- 以簡單的起點開始使用時
- 如果沒有明顯的聯結索引鍵
- 如果沒有合適的候選資料行可供雜湊散發資料表
- 如果資料表並未與其他資料表共用常見的聯結索引鍵
- 如果此聯結比查詢中的其他聯結較不重要
- 當資料表是暫存預備資料表時

兩個範例都會建立循環配置資源資料表︰

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] 雖然循環配置資源是預設資料表類型，但以您的 DDL 明確表示被視為最佳做法，讓其他人能夠清楚了解您的資料表配置的意圖。

### 雜湊散發資料表

使用**雜湊散發**演算法來散發您的資料表，可減少查詢時的資料移動，進而改善許多案例的效能。雜湊散發資料表是在您選取的單一資料行上使用雜湊演算法，分配於分散式資料庫間的資料表。散發資料行可決定如何將資料分配於您的分散式資料庫。雜湊函式會使用散發資料行將資料列指派給散發。雜湊演算法與所產生的散發具決定性。也就是，相同資料類型的相同值永遠都使用相同的散發。

這個範例會建立依照識別碼散發的資料表︰

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## 選擇散發資料行

當您選擇**雜湊散發**資料表時，您必須選取單一散發資料行。選取散發資料行時，需要考量三個主要因素。

選取具有下列特性的單一資料行︰

1. 不會更新
2. 平均散發資料，以避免資料扭曲
3. 最小化資料移動

### 選取不會更新的散發資料行

散發資料行不可更新，因此，選取具靜態值的資料行。如果資料行需要更新，該資料行通常不是理想的散發候選項目。如果您必須更新散發資料行，做法是先刪除資料列，然後插入新的資料列。

### 選取將平均散發資料的散發資料行

分散式系統的執行速度只與其最慢的散發一樣快，所以務必將工作平均分配於各散發，以便讓系統達到平衡的執行。工作會根據每個散發的資料所在位置，分配於分散式系統。這對於選取正確的散發資料行來散發資料非常重要，如此一來，每個散發都有相等的工作，而且完成其工作部分所需的時間相同。當工作妥善分配於系統時，這就叫做平衡的執行。當資料未平均分配於系統，而且不平衡時，我們將此稱為**資料扭曲**。

若要平均分配資料以避免資料扭曲，請在選取散發資料行時考量下列各項︰

1. 選取包含大量相異值的資料行。
2. 避免將資料散發於有一些值頻繁出現或 null 頻繁出現的資料行。
3. 避免在日期資料行上散發資料。
4. 避免散發於小於 60 個資料行

因為每個值會雜湊制 60 個散發之一，若要達到平均散發，您要選取極為獨特並提供超過 60 個唯一值的資料行。為了方便解說，請考慮資料行只有 40 個唯一值的極端案例。如果此資料行已獲選為散發索引鍵，則該資料表的資料只會分散於系統的一部分，以致 20 個散發不含任何資料，而且沒有要進行的處理。相反地，如果資料平均分散於 60 個散發，則其他 40 個散發有更多工作需要進行。

如果您在高度可為 Null 的資料行上散發資料表，則所有 Null 值會置於相同的散發，而該散發必須進行比其他散發更多的工作，這會讓整個系統變慢。在查詢對日期具高度選擇性，而且只有少數日期涉及查詢的情況下，散發於日期資料行也可能導致處理扭曲。

若沒有理想的候選資料行存在，則考慮使用循環配置資源做為散發方法。

### 選取會將資料移動降至最低的散發資料行

藉由選取適當散發資料行來將資料移動降至最低，是讓 SQL 資料倉儲的效能達到最佳化的最重要策略之一。資料移動最常發生於聯結資料表或執行彙總時。在通常聯結的資料行上雜湊散發大型事實資料表，是將資料移動降至最低的最有效方法之一。除了選取聯結資料行來避免資料移動以外，還必須符合一些準則，才能避免資料移動。若要避免資料移動︰

1. 聯結中的相關資料表必須雜湊散發於其中一個聯結資料行上。
2. 聯結資料行的資料類型必須相符。
3. 資料行必須以 equals 運算子聯結。
4. 聯結類型可能不是 `CROSS JOIN`。

用於 `JOIN`、`GROUP BY`、`DISTINCT`、`OVER` 和 `HAVING` 子句的資料行全都是理想的雜湊散發候選項目。另一方面，用於 `WHERE` 子句的資料行**不是**理想的雜湊資料行候選項目，因為它們會限制哪些散發參與查詢。一般而言，如果您有兩個經常涉入聯結的大型事實資料表，您最常會在其中一個聯結資料行上散發。如果您有永遠不會聯結到另一個大型事實資料表的資料表，則尋找經常出現在 `GROUP BY` 子句中的資料行。除非找不到理想的候選資料行可平均散發您的資料，否則選取散發資料行，而非使用預設的循環配置資源散發，通常對您的查詢帶來好處。


## 資料扭曲疑難排解

資料表的資料是使用雜湊散發方法來散發時，有些散發可能會扭曲，會比其他資料表具有更多資料。過多資料扭曲可能會影響查詢效能，因為散發查詢的最終結果必須等待執行時間最長的散發完成。視資料扭曲的程度而定，您可能需要處理它。

### 識別扭曲

將資料表識別為扭曲的簡單方法是使用 `DBCC PDW_SHOWSPACEUSED`。這個非常快速且簡單的方式，用來查看儲存在您資料庫中每 60 個散發內的資料表資料列數目。請記住，為了達到最平衡的效能，分散式資料表中的資料列應該平均分散到所有散發中。

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

不過，如果您查詢 Azure SQL 資料倉儲動態管理檢視 (DMV)，則可以執行更詳細的分析。若要開始，請使用[資料表概觀][Overview]一文中的 SQL 建立 [dbo.vTableSizes][] 檢視。建立檢視後，請執行此查詢來識別哪些資料表有 10% 以上的資料扭曲。

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vDistributionSkew 
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
	)
order by two_part_name, row_count
;
```

### 解決資料扭曲

並非所有的扭曲都足以批准修正。在某些情況下，某些查詢中的資料表效能可以超越資料扭曲的傷害。若要決定是否應該解決資料表中的資料扭曲，您應該盡可能了解工作負載中的資料磁區和查詢。查看扭曲影響的方法之一是使用[查詢監視][]一文中的步驟，監視扭曲對於查詢效能的影響，尤其是在個別散發上完成查詢所需時間的影響。

散發資料就是找出將資料扭曲降至最低與將資料移動降至最低兩者之間的正確平衡點。這些可能是相反的目標，有時候您會想要保留資料扭曲，以減少資料移動。比方說，當散發資料行經常是聯結和彙總中的共用資料行時，您便會將資料移動降至最低。擁有最少的資料移動，所帶來的好處可能勝過具有資料扭曲的影響。

解決資料扭曲的一般方式，是重新建立具有不同散發資料行的資料表。沒有任何方法可變更現有資料表上的散發資料行，所以變更資料表散發的方法是使用 [CTAS][] 重建資料表。以下是解決資料扭曲的兩個範例：

### 範例 1︰重建具有新散發資料行的資料表

此範例會使用 [CTAS][] 來重建具有不同雜湊資料行的資料表。

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

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### 範例 2︰使用循環配置資源散發重建資料表

此範例會使用 [CTAS][] 來重建具有循環配置資源 (而非雜湊散發) 的資料表。這項變更將會產生平均的資料散發，但代價是資料移動增加。

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

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## 後續步驟

若要深入了解資料表設計，請參閱[散發][]、[索引][]、[資料分割][]、[資料類型][]、[統計資料][]和[暫存資料表][Temporary]等文章。如需最佳做法的概觀，請參閱 [SQL Data 資料倉儲最佳做法][]。


<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[概觀]: ./sql-data-warehouse-tables-overview.md
[資料類型]: ./sql-data-warehouse-tables-data-types.md
[散發]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[索引]: ./sql-data-warehouse-tables-index.md
[資料分割]: ./sql-data-warehouse-tables-partition.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[暫存]: ./sql-data-warehouse-tables-temporary.md
[SQL Data 資料倉儲最佳做法]: ./sql-data-warehouse-best-practices.md
[查詢監視]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0706_2016-->