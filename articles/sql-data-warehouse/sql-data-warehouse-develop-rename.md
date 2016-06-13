<properties
   pageTitle="在 SQL 資料倉儲中重新命名 | Microsoft Azure"
   description="在「Azure SQL 資料倉儲」中重新命名資料表以開發解決方案的秘訣。"
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
   ms.date="05/28/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# 在 SQL 資料倉儲中重新命名
「SQL 資料倉儲」使用 [RENAME][] 陳述式來重新命名資料表。這與 SQL Server 不同，SQL Server 使用的是 `sp_rename`。目前只能重新命名使用者資料表。無法重新命名資料庫和外部資料表。

## 重新命名資料表

重新命名資料表時，會將與資料表關聯的所有物件和屬性都更新成參考新的資料表名稱。例如，會更新資料表定義、索引、條件約束及權限。不會更新檢視。

重新命名資料表的語法是：

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

## 變更資料表結構描述

若要變更物件所屬的結構描述，請使用 ALTER SCHEMA：

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## 資料表重新命名需要獨佔的資料表鎖定

您無法在資料表處於使用中時將它重新命名。重新命名資料表時，需要該資料表的獨佔鎖定。如果資料表處於使用中，您可能需要終止使用該資料表的工作階段。請使用 [KILL][] 命令來終止工作階段。例如 `KILL 'SID1234'`。使用 KILL 時請小心，因為終止工作階段時，會復原所有未認可的異動工作。如需[工作階段和要求][]的詳細資訊，請參閱連線文件。如需有關終止異動查詢之影響及復原之影響的詳細資訊，請參閱[最佳化 SQL 資料倉儲的交易][]。


## 後續步驟
如需更多 T-SQL 參考，請參閱 [T-SQL 參考][]。

<!--Image references-->

<!--Article references-->
[development overview]: ./sql-data-warehouse-overview-develop.md
[工作階段和要求]: ./sql-data-warehouse-develop-connections.md#sessions-and-requests
[T-SQL 參考]: ./sql-data-warehouse-reference-tsql-statements.md
[最佳化 SQL 資料倉儲的交易]: ./sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!---HONumber=AcomDC_0601_2016-->