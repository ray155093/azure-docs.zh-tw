---
title: 在移轉至 SQL Database 之前，使用 SQL Server Management Studio 修正 SQL Server 資料庫相容性問題 | Microsoft Docs
description: Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab

---
# 在移轉至 SQL Database 之前，使用 SQL Server Management Studio 修正 SQL Server 資料庫相容性問題
> [!div class="op_single_selector"]
> * 使用 [SQL Azure 移轉精靈](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * 使用 [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * 使用 [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> 
> 

進階使用者在移轉至 Azure SQL Database 之前，可以先使用 SQL Server Management Studio 來修正 SQL Server 資料庫相容性問題。

> [!IMPORTANT]
> 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。[更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## 使用 SQL Server Management Studio
使用 SQL Server Management Studio 以各種 Transact-SQL 命令修正相容性問題，例如 **ALTER DATABASE**。這個方法主要適用於擅長在即時資料庫上操作 Transact-SQL 的進階使用者。否則，建議您使用 SSDT。

## 後續步驟
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [將相容 SQL Server 資料庫移轉到 SQL Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## 其他資源
* [SQL Database V12](sql-database-v12-whats-new.md)
* [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
* [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->