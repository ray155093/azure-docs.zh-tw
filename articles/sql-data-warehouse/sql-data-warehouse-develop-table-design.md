<properties
   pageTitle="SQL 資料倉儲中的資料表設計 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中設計資料表以便開發解決方案的秘訣。"
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
   ms.date="05/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 資料倉儲中的資料表設計 #
SQL 資料倉儲是大量平行處理 (MPP) 分散式資料庫系統。它會將資料儲存在許多不同的位置 (也稱為**散發**)。每個**散發**都像是一個貯體；可在資料倉儲中儲存唯一的資料子集。將資料和處理能力分割於多個節點之間，SQL 資料倉儲就能夠提供極大的延展性 - 遠超過任何單一系統。

在 SQL 資料倉儲中建立資料表時，資料表實際上會散佈於所有的散發。

本文涵蓋下列主題：

- 支援的資料類型
- 資料散發原則
- 循環配置資源散發
- 雜湊散發
- 資料表分割
- 統計資料
- 不支援的功能

## 支援的資料類型
SQL 資料倉儲支援常見的商務資料類型：

- **bigint**
- **binary**
- **bit**
- **char**
- **date**
- **datetime**
- **datetime2**
- **datetimeoffset**
- **decimal**
- **float**
- **int**
- **money**
- **nchar**
- **nvarchar**
- **real**
- **smalldatetime**
- **smallint**
- **smallmoney**
- **sysname**
- **time**
- **tinyint**
- **uniqueidentifier**
- **varbinary**
- **varchar**

您可以使用下列查詢來識別資料倉儲中包含不相容類型的資料行：

```sql
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'sql_variant'
                ,   'text'
                ,   'timestamp'
                ,   'xml'
                )
AND  y.[is_user_defined] = 1
;

```

此查詢包含任何不支援的使用者定義資料類型。以下是一些您可以用來取代不支援的資料類型的替代方案。

不要使用：

- **geometry**，使用 varbinary 類型
- **geography**，使用 varbinary 類型
- **hierarchyid**，CLR 類型不是原生的
- **image**、**text**、**ntext** (以文字為主時)，使用 varchar/nvarchar (越小越好)
- **sql\_variant**，將資料行分割成數個強型別資料行
- **table**，轉換成暫時資料表
- **timestamp**，修改程式碼來使用 datetime2 和 `CURRENT_TIMESTAMP` 函式。請注意，您不能以 current\_timestamp 做為預設條件約束，因為值不會自動更新。如果您需要從 timestamp 類型資料行移轉 rowversion 值，請對 NOT NULL 或 NULL 資料列版本值使用 BINARY(8) 或 VARBINARY(8)。
- **使用者定義型別**，可能的話，轉換回原生型別
- **xml**，使用 varchar(max) 或更小，效能將會更好

若要取得更加的效能，與其使用：

- **nvarchar(max)**，使用 nvarchar(4000) 或更小，效能將會更好
- **varchar(max)**，使用 varchar(8000) 或更小，效能將會更好

部分支援：

- 預設條件約束僅支援常值和常數。不支援不具決定性的運算式或函式，例如 `GETDATE()` 或 `CURRENT_TIMESTAMP`。

> [AZURE.NOTE] 如果您正在使用 PolyBase 以載入您的資料表，請定義您的資料表，使最大可能的資料列大小 (包括可變長度資料行的完整長度) 不超過 32,767 個位元組。雖然您定義的資料列可以有超過此數據的可變長度資料，並透過 BCP 載入資料列，您尚無法使用 PolyBase 以載入此資料。PolyBase 針對廣泛資料列的支援將會於近期內新增。此外，也請限制可變長度資料行的大小，以便執行查詢時有更大的輸送量。

## 資料散發原則

有兩個選項可在 SQL 資料倉儲中散發資料：

1. 平均但隨機散發資料
2. 根據單一資料行中的雜湊值散發資料

在資料表層級決定資料散發。將所有資料表散發。您會在 SQL 資料倉儲資料庫中指派每個資料表的散發 。

第一個選項就是所謂的**循環配置資源**散發 - 有時也稱為隨機雜湊。您可以將此視為預設或保全選項。

第二個選項稱為**雜湊**散發。您可以將它視為資料散發的最佳化形式。當資料表叢集共用常見的聯結及/或彙總準則時，慣用這個選項。

## 循環配置資源散發

循環配置資源散發是儘可能讓資料平均散佈於所有散發的方法。含有資料列的緩衝區會依序配置 (因此是名稱循環配置資源) 到每個散發。此程序會重複，直到配置所有資料緩衝區為止。資料不會在循環配置資源分散式資料表中進行排序。基於這個理由，循環配置資源散發有時稱為隨機雜湊。資料會盡可能地平均散佈於所有散發。

以下是循環配置資源分散式資料表的範例：

```sql
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

這也是循環配置資源分散式資料表的範例：

```sql
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
)
;
```

> [AZURE.NOTE] 請注意，上述第二個範例並未提到散發索引鍵。循環配置資源是預設值，所以不是絕對必要。不過，清楚明確是相當好的做法，因為可確保同儕會在檢閱資料表設計時察覺您的用意。

沒有明顯的索引鍵資料行可雜湊資料時，通常會使用此資料表類型。較小或較不重要的資料表 (其移動成本可能不是很高) 也可使用此資料表類型。

將資料載入循環配置資源分散式資料表，通常比載入雜湊分散式資料表中還要快。使用循環配置資源分散式資料表，載入前不需要了解資料或執行雜湊。基於這個理由，循環配置資源資料表通常具有良好的載入目標。

> [AZURE.NOTE] 當資料進行循環配置資源散發後，資料就會在*緩衝區*層級配置到散發。

### 建議

在下列情況下，請考慮對資料表使用循環配置資源散發：

- 沒有明顯的聯結索引鍵時
- 如果不知道候選雜湊散發索引鍵
- 如果資料表並未與其他資料表共用常見的聯結索引鍵
- 如果此聯結比查詢中的其他聯結較不重要
- 當資料表是初始載入資料表時

## 雜湊散發

雜湊散發使用內部函式，藉由雜湊單一資料行來將資料集分散於各個散發。雜湊資料後，沒有明確的順序可將資料配置到散發。不過，雜湊本身是具決定性的程序。這會讓雜湊的結果變得可預測。例如，雜湊包含值 10 的整數資料行時，一定會產生相同的雜湊值。這表示***任何***包含值 10 的雜湊整數資料行最終都會配置到相同的散發。這適用於所有的資料表。

雜湊的可預測性非常重要。這表示散發資料的雜湊可能會使讀取資料和將資料表聯結在一起時的效能改進。

如您在下面所見，雜湊散發對於查詢最佳化非常有用。這就是它為何會被視為資料散發的最佳化形式。

> [AZURE.NOTE] 請記住！ 雜湊並不是只有基於資料的值。雜湊是值和資料類型的組合。

以下是依據 ProductKey 進行雜湊散發的資料表。

```sql
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
,   DISTRIBUTION = HASH([ProductKey])
)
;
```

> [AZURE.NOTE] 當資料進行雜湊散發後，資料就會在資料列層級配置到散發。

## 資料表的資料分割
支援資料表分割，並可輕鬆定義。

SQL 資料倉儲分割的 `CREATE TABLE` 命令範例：

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

請注意，定義中沒有資料分割函式或配置。SQL 資料倉儲使用與 SQL Server 稍有不同的簡化分割定義。您只需為已分割的資料行找出邊界點。

## 統計資料

當使用者查詢資料表時，SQL 資料倉儲會使用分散式查詢最佳化工具來建立適當的查詢計劃。一旦建立，查詢計劃會提供資料庫用來存取資料和履行使用者要求的策略和方法SQL 資料倉儲的查詢最佳化工具是以成本為基礎。換句話說，它會根據相對成本來比較各種選項 (計劃) 並選擇最有效率的可用計劃。因此，SQL 資料倉儲需要大量資訊，才能做出以成本為基礎的明智決策。它會保留有關資料表 (資料表大小) 的統計資訊，而在資料庫物件稱為 `STATISTICS`。

統計資料會真對索引或資料表的單一或多重資料行進行保留。這些資料可將值的基數和選擇性相關重要資訊，提供給以成本為基礎的最佳化工具。當最佳化工具需要評估查詢中的 JOIN、GROUP BY、HAVING 和 WHERE 子句時，這是特別有用。因此，這些統計資料物件內含的資訊一定要*精確地* 反映出資料表的目前狀態。請務必了解成本的精確度很很重要。如果統計資料精確地反映出資料表的狀態，即可比較計劃以找出最低的成本。如果統計資料不精確，則 SQL 資料倉儲可能會選擇錯誤的計劃。

SQL 資料倉儲中的資料行層級統計資料是由使用者定義。

換句話說，我們必須自行建立。我們剛才知道，這件事不容忽視。這是 SQL Server 與 SQL 資料倉儲間的重要差異。查詢資料行時，SQL Server 會自動建立統計資料。根據預設，SQL Server 也會自動更新這些統計資料。不過，在 SQL 資料倉儲中，需要以手動方式建立和管理統計資料。

### 建議

產生統計資料時，請採用下列建議：

1. 對使用於 `WHERE`、`JOIN`、`GROUP BY`、`ORDER BY` 和 `DISTINCT` 子句的資料行，建立單一資料行統計資料
2. 產生複合子句的多重資料行統計資料
3. 定期更新統計資料。請記住，此作業不會自動完成！

>[AZURE.NOTE] SQL Server 資料倉儲通常會完全依賴 `AUTOSTATS`，讓資料行統計資料保持最新狀態。對 SQL Server 資料倉儲而言，這不是最佳做法。20% 的變動率會觸發 `AUTOSTATS`，這對於包含數百萬或數十億個資料列的大型事實資料表而言可能不太足夠。因此，最好隨時掌握統計資料更新，以確保統計資料能精確地反映出資料表的基數。

## 不支援的功能
SQL 資料倉儲不使用或支援下列功能：

| 功能 | 因應措施 |
| --- | --- |
| 身分識別 | [指派 Surrogate 索引鍵] |
| 主索引鍵 | N/A |
| 外部索引鍵 | N/A |
| 檢查條件約束 | N/A |
| 唯一條件約束 | N/A |
| 唯一索引 | N/A |
| 計算資料行 | N/A |
| 疏鬆資料行 | N/A |
| 使用者定義的類型 | N/A |
| 索引檢視表 | N/A |
| 順序 | N/A |
| 觸發程序 | N/A |
| 同義字 | N/A |

## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。如需最佳做法的相關秘訣，請參閱 [SQL Data 資料倉儲最佳做法][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md
[指派 Surrogate 索引鍵]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[SQL Data 資料倉儲最佳做法]: sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->