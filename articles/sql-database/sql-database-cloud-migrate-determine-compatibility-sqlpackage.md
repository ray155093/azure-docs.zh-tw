---
title: "使用 SqlPackage.exe 判斷 SQL Database 相容性 | Microsoft Docs"
description: "Microsoft Azure SQL Database, 資料庫移轉, SQL Database 相容性, SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: a3e43c6cb8e26daf7359f935816648bf498407ec


---
# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>使用 SqlPackage.exe 判斷 SQL Database 相容性
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 

在本文中，您將學習判斷 SQL Server 資料庫是否相容，以使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式將其移轉到 SQL Database。

## <a name="using-sqlpackageexe"></a>使用 SqlPackage.exe
1. 開啟命令提示字元並變更包含最新版 sqlpackage.exe 的目錄。 此公用程式隨附於最新版的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，或者您也可以直接從 Microsoft 下載中心下載最新版的 [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876)。
2. 以環境的下列引數執行下列 SqlPackage 命令：

```   
    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1
```   
   | 引數 | 說明 |
   | --- | --- |
   | < server_name > |來源伺服器名稱 |
   | < database_name > |來源資料庫名稱 |
   | < target_file > |BACPAC 檔案的檔案名稱和位置 |
   | < schema_name.table_name > |其資料要輸出到目標檔案的資料表 |
   | < output_file > |具有錯誤的輸出檔案的檔案名稱和位置 (若有的話) |
   
    The reason for the /p:TableName argument is that we only want to test for database compatibility for export to Azure SQL DB V12 rather than export the data from all tables. Unfortunately, the export argument for sqlpackage.exe does not support extracting zero tables. You need to specify at least one table, such as a single small table. The < output_file > contains the report of any errors. The "> 2>&1" string pipes both the standard output and the standard error resulting from the command execution to specified output file.
   
    ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)
3. 開啟輸出檔案並檢視相容性錯誤 (如果有的話)。 
   
    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>後續步驟
* [最新版的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
  [最新版的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [修正資料庫移轉相容性問題](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [將相容 SQL Server 資料庫移轉到 SQL Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他資源
* [SQL Database 功能](sql-database-features.md)
* [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
* [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


