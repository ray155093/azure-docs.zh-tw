<properties
   pageTitle="使用 SqlPackage 將的 SQL Server Database 匯出到 BACPAC 檔案"
   description="Microsoft Azure SQL Database, 資料庫移轉, 匯出資料庫, 匯出 BACPAC 檔案, sqlpackage"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# 使用 SqlPackage 將的 SQL Server Database 匯出到 BACPAC 檔案

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

本文說明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式，將 SQL Server 資料庫匯出成 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。此公用程式隨附於 Visual Studio 和 SQL Server。您也可以[下載](https://msdn.microsoft.com/library/mt204009.aspx)最新版的 SQL Server Data Tools 以取得此公用程式。

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。使用電腦上的搜尋功能來尋找您環境中的路徑。
2. 以環境的下列引數執行下列 sqlpackage.exe 命令：

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| 引數 | 說明 |
	|---|---|
	| < server_name > | 來源伺服器名稱 |
	| < database_name > | 來源資料庫名稱 |
	| < target_file > | BACPAC 檔案的檔案名稱和位置 |

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## 後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [使用 SSMS 將 BACPAC 匯入 Azure SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [使用 SqlPackage 將 BACPAC 匯入 Azure SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [使用 Azure 入口網站將 BACPAC 匯入 Azure SQL Database](sql-database-import.md)
- [使用 PowerShell 將 BACPAC 匯入 Azure SQL Database](sql-database-import-powershell.md)

## 詳細資訊

- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
- [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0601_2016-->