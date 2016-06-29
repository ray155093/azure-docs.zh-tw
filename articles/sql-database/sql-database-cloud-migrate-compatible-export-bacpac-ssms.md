
<properties
   pageTitle="使用 SQL Server Management Studio 將 SQL Server 資料庫匯出到 BACPAC 檔案 | Microsoft Azure"
   description="Microsoft Azure SQL Database, 資料庫移轉, 匯出資料庫, 匯出 BACPAC 檔案, 匯出資料層應用程式精靈"
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

# 使用 SQL Server Management Studio 將 SQL Server 資料庫匯出到 BACPAC 檔案

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
本文說明如何使用 SQL Server Management Studio 中的「匯出資料層應用程式精靈」將 SQL 伺服器資料庫匯出成 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。

1. 請確認您有最新版本的 SQL Server Management Studio。新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

	 > [AZURE.IMPORTANT] 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。[更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. 以滑鼠右鍵按一下 [物件總管] 中的來源資料庫，指向 [工作]，並按一下 [匯出資料層應用程式]。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 在匯出精靈中，將匯出設定為將 BACPAC 檔案儲存到本機磁碟位置或 Azure Blob。匯出的 BACPAC 一律會包含完整的資料庫結構描述，以及預設之所有資料表的資料。如果您想要排除部分或所有資料表的資料，請使用 [進階] 索引標籤。比方說，您可能會選擇只匯出參考資料表的資料，而不是所有資料表的資料。

***重要：*** 將 BACPAC 匯出至 Azure blob 儲存體時，請使用標準儲存體。不支援從進階儲存體匯入 BACPAC。

	![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


## 後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [使用 SSMS 將 BACPAC 匯入 Azure SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [使用 SqlPackage 將 BACPAC 匯入 Azure SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [使用 Azure 入口網站將 BACPAC 匯入 Azure SQL Database](sql-database-import.md)
- [使用 PowerShell 將 BACPAC 匯入 Azure SQL Database](sql-database-import-powershell.md)

## 其他資源

- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
- [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->