<properties
   pageTitle="SQL 資料倉儲中的透明資料加密 (T-SQL) | Microsoft Azure"
   description="SQL 資料倉儲中的透明資料加密 (TDE) (T-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# 開始使用透明資料加密 (TDE)


> [AZURE.SELECTOR]
- [安全性概觀](sql-data-warehouse-overview-manage-security.md)
- [驗證](sql-data-warehouse-authentication.md)
- [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
- [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## 需要的權限

您必須是系統管理員或 dbmanager 角色的成員，才能啟用透明資料加密 (TDE)。

## 啟用加密

請遵循下列步驟來啟用 SQL 資料倉儲的 TDE：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到裝載資料庫之伺服器上的 *master* 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## 停用加密

請遵循下列步驟來停用 SQL 資料倉儲的 TDE：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到 *master* 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] 變更 TDE 設定之前，必須先恢復暫停的 SQL 資料倉儲。

## 確認加密

若要確認 SQL 資料倉儲的加密狀態，請遵循下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到 *master* 或執行個體資料庫
2. 執行下列陳述式來加密資料庫。

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

```1``` 的結果表示加密的資料庫，```0``` 表示未加密的資料庫。

## 加密 DMV  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->