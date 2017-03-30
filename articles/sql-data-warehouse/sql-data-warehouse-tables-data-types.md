---
title: "SQL 資料倉儲中的資料表的資料類型 | Microsoft Docs"
description: "開始使用 Azure SQL 資料倉儲資料表的資料類型。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7757de96601c426ff07e94cfa0c12d4dec8f06f5
ms.lasthandoff: 03/22/2017


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>SQL 資料倉儲中的資料表的資料類型
> [!div class="op_single_selector"]
> * [概觀][Overview]
> * [資料類型][Data Types]
> * [散發][Distribute]
> * [索引][Index]
> * [資料分割][Partition]
> * [統計資料][Statistics]
> * [暫存][Temporary]
> 
> 

SQL 資料倉儲支援最常用的資料類型。  以下是 SQL 資料倉儲支援的資料類型清單。  如需有關資料類型支援的其他詳細資訊，請參閱[建立資料表][create table]。

| **支援的資料類型** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>資料類型的最佳作法
 在定義資料行類型時，使用可支援您的資料的最小資料類型，將能夠改善查詢效能。 這對 CHAR 和 VARCHAR 資料行尤其重要。 如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。 避免將所有字元資料行定義為較大的預設長度。 此外，將資料行定義為 VARCHAR (當它只需要這樣的大小時) 而非 [NVARCHAR][NVARCHAR]。  儘可能使用 NVARCHAR(4000) 或 VARCHAR(8000)，而非 NVARCHAR(MAX) 或 VARCHAR(MAX)。

## <a name="polybase-limitation"></a>Polybase 限制
如果您使用 Polybase 來載入資料表，請確定資料長度不超過 1 MB。  雖然您定義的資料列可以有超過此寬度的可變長度資料，並透過 BCP 載入資料列，您將無法使用 PolyBase 載入此資料。  

## <a name="unsupported-data-types"></a>不支援的資料類型
如果您從另一個 SQL 平台 (例如 Azure SQL Database) 移轉您的資料庫，在移轉時，您可能會遇到 SQL 資料倉儲不支援的某些資料類型。  以下是不支援的資料類型，以及一些您可以用來取代不支援的資料類型的替代方案。

| 資料類型 | 因應措施 |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |將資料行分割成數個強型別資料行。 |
| [table][table] |轉換成暫存資料表。 |
| [timestamp][timestamp] |修改程式碼來使用 [datetime2][datetime2] 和 `CURRENT_TIMESTAMP` 函式。  僅支援常數做為預設值，因此 current_timestamp 不可定義為預設條件約束。 如果您需要從 timestamp 類型資料行移轉資料列版本值，請對 NOT NULL 或 NULL 資料列版本值使用 [BINARY][BINARY](8) 或 [VARBINARY][BINARY](8)。 |
| [xml][xml] |[varchar][varchar] |
| [使用者定義型別][user defined types] |可能的話，轉換回原生型別 |
| 預設值 |預設值僅支援常值和常數。  不支援不具決定性的運算式或函式，例如 `GETDATE()` 或 `CURRENT_TIMESTAMP`。 |

在您目前的 SQL 資料庫上，可執行以下 SQL 來識別 Azure SQL 資料倉儲不支援的資料行︰

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>後續步驟
若要深入了解，請參閱[資料表概觀][Overview]、[散發資料表][Distribute]、[編製資料表的索引][Index]、[分割資料表][Partition]、[維護資料表統計資料][Statistics]和[暫存資料表][Temporary]等文章。  若要深入了解最佳作法，請參閱 [SQL Data 資料倉儲最佳作法][SQL Data Warehouse Best Practices]。

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx

