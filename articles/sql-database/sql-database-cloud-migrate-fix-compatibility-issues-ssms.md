---
title: "在移轉至 SQL Database 之前，使用 SQL Server Management Studio 修正 SQL Server 資料庫相容性問題 | Microsoft Docs"
description: "Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5f7d3544-b07e-415a-a2ae-96e49bf5d756
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 355353fb15a00860573699cc652543b61c62c2c1


---
# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>在移轉至 SQL Database 之前，使用 SQL Server Management Studio 修正 SQL Server 資料庫相容性問題
> [!div class="op_single_selector"]
> * 使用 [SQL Azure 移轉精靈](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * 使用 [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * 使用 [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> 
> 

進階使用者在移轉至 Azure SQL Database 之前，可以先使用 SQL Server Management Studio 來修正 SQL Server 資料庫相容性問題。

> [!IMPORTANT]
> 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio
使用 SQL Server Management Studio 以各種 Transact-SQL 命令修正相容性問題，例如 **ALTER DATABASE**。 這個方法主要適用於擅長在即時資料庫上操作 Transact-SQL 的進階使用者。 否則，建議您使用 SSDT。 

## <a name="next-steps"></a>後續步驟
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [將相容 SQL Server 資料庫移轉到 SQL Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他資源
* [SQL Database 功能](sql-database-features.md)
* [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
* [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


