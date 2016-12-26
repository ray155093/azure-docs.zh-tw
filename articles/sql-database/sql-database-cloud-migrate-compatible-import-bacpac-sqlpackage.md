---
title: "使用 SqlPackage 從 BACPAC 檔案匯入 SQL Database"
description: "Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 匯入 BACPAC 檔案, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
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
ms.openlocfilehash: d1f701bc5e4028db4d97ac2e7097afedf46b1f1f


---
# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 從 BACPAC 檔案匯入 SQL Database
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Azure 入口網站](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

本文說明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式，從 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案匯入 SQL 資料庫。 此公用程式隨附於最新版的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，或者您也可以直接從 Microsoft 下載中心下載最新版的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

> [!NOTE]
> 下列步驟假設您已佈建 SQL Database 伺服器、手上有連接資訊並已確認來源資料庫相容。
> 
> 

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>使用 SqlPackage 從 BACPAC 檔案匯入至 Azure SQL Database
使用下列步驟來使用 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式，從 BACPAC 檔案匯入相容的 SQL Server 資料庫 (或 Azure SQL Database)。

> [!NOTE]
> 下列步驟假設您已佈建 Azure SQL Database 伺服器並且手邊有連接資訊。
> 
> 

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。
2. 以環境的下列引數執行下列 sqlpackage.exe 命令：
   
    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`
   
   | 引數 | 說明 |
   | --- | --- |
   | < server_name > |目標伺服器名稱 |
   | < database_name > |目標資料庫名稱 |
   | < user_name > |目標伺服器中的使用者名稱 |
   | < password > |使用者的密碼 |
   | < source_file > |匯入的 BACPAC 檔案的檔案名稱和位置 |
   
    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>後續步驟
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>其他資源
* [SQL Database 功能](sql-database-features.md)
* [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
* [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


