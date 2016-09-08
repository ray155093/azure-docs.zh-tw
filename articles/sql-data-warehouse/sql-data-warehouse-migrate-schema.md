<properties
   pageTitle="將您的結構描述移轉至 SQL 資料倉儲 | Microsoft Azure"
   description="將您的結構描述移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。"
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
   ms.date="08/17/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 將您的結構描述移轉至 SQL 資料倉儲#

下列摘要可協助您了解 SQL Server 與「SQL 資料倉儲」之間的差異，以幫助您移轉資料庫。

### 資料表功能
SQL 資料倉儲不使用或支援下列功能：

- 主索引鍵
- 外部索引鍵
- 檢查條件約束
- 唯一條件約束
- 唯一索引
- 計算資料行
- 疏鬆資料行
- 使用者定義的類型
- 索引檢視表
- 身分識別
- 順序
- 觸發程序
- 同義字


### 資料類型的差異
SQL 資料倉儲支援常見的商務資料類型：

- bigint
- binary
- bit
- char
- 日期
- datetime
- datetime2
- datetimeoffset
- decimal
- float
- int
- money
- nchar
- nvarchar
- numeric
- real
- smalldatetime
- smallint
- smallmoney
- sysname
- 分析
- tinyint
- varbinary
- varchar
- uniqueidentifier

您可以使用這個查詢來識別資料倉儲中包含不相容類型的資料行：

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

該查詢包含任何使用者定義的資料類型，而系統也不支援這些資料類型。

如果您的資料庫中有不支援的類型，請不要擔心。以下提出一些您可以改用的替代方案。

不要使用：

- **geometry**，使用 varbinary 類型
- **geography**，使用 varbinary 類型
- **hierarchyid**，不支援這個 CLR 類型
- **image**、**text**、**ntext**，使用 varchar/nvarchar (越小越好)
- **sql\_variant**，將資料行分割成數個強型別資料行
- **table**，轉換成暫時資料表
- **timestamp**，修改程式碼來使用 datetime2 和 `CURRENT_TIMESTAMP` 函數。請注意，您不能使用 current\_timestamp 作為預設條件約束。如果您需要從 timestamp 類型資料行移轉 rowversion 值，請使用 binary(8) 或 varbinary(8) 作為 NOT NULL 或 NULL 資料列版本值。
- **使用者定義類型**，可能的話，轉換回其原生類型
- **xml**，使用 varchar(max) 或更小，效能將會更好。必要的話，分割資料行

若要取得更加的效能，與其使用：

- nvarchar(max)，使用 nvarchar(4000) 或更小，效能將會更好
- varchar(max)，使用 varchar(8000) 或更小，效能將會更好

部分支援：

- 預設條件約束僅支援常值和常數。不支援不具決定性的運算式或函式，例如 `GETDATE()` 或 `CURRENT_TIMESTAMP`。

> [AZURE.NOTE] 定義您的資料表，使得在使用 PolyBase 來執行載入時，資料列大小上限不會超過 32,767 個位元組。請務必記得資料列大小上限包括任何可變長度資料行的完整長度。雖然您可以定義可變長度資料超過此數字的資料列，但是目前無法使用 PolyBase 來載入此資料。作為暫時的因應措施，請使用 BCP 來載入寬資料列。最後，請嘗試限制可變長度資料行的大小，以便在執行查詢時獲得更大的輸送量。

## 後續步驟
將資料庫結構描述成功移轉到「SQL 資料倉儲」之後，您就可以繼續閱讀下列其中一篇文章：

- [移轉資料][]
- [移轉程式碼][]

如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[移轉程式碼]: sql-data-warehouse-migrate-code.md
[移轉資料]: sql-data-warehouse-migrate-data.md
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->