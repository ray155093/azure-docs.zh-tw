---
title: "使用 SqlPackage 將 SQL Server 資料庫匯出到 BACPAC 檔案 | Microsoft Docs"
description: "Microsoft Azure SQL Database, 資料庫移轉, 匯出資料庫, 匯出 BACPAC 檔案, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
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
ms.openlocfilehash: 47c4c2e78432746a0a03f98692ddcd4602637212


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 將的 SQL Server Database 匯出到 BACPAC 檔案
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

本文說明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式，將 SQL Server 資料庫匯出成 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。 此公用程式隨附於最新版的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，或者您也可以直接從 Microsoft 下載中心下載最新版的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。 使用電腦上的搜尋功能來尋找您環境中的路徑。
2. 以環境的下列引數執行下列 sqlpackage.exe 命令：
   
```    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >
```
   
   | Argument | Description |
   | --- | --- |
   | < server_name > |source server name |
   | < database_name > |source database name |
   | < target_file > |file name and location for BACPAC file |
   
   ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps
* [Newest version of SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Newest version of SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Import a BACPAC to Azure SQL Database using SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Import a BACPAC to Azure SQL Database SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Import a BACPAC to Azure SQL Database Azure portal](sql-database-import.md)
* [Import a BACPAC to Azure SQL Database PowerShell](sql-database-import-powershell.md)

## Additional resources
* [SQL Database features](sql-database-features.md)
* [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


