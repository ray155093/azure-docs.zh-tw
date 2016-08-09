<properties
	pageTitle="SQL Database 教學課程：建立 SQL Database | Microsoft Azure"
	description="了解如何設定 SQL Database 邏輯伺服器、伺服器防火牆規則、SQL Database、範例資料、使用用戶端工具連接、設定使用者和資料庫防火牆規則。"
	keywords="sql database 教學課程, 建立 sql database"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="carlrab"/>

# SQL Database 教學課程：使用 Azure 入口網站在幾分鐘內建立 SQL Database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

在本教學課程裡，您將學習如何使用 Azure 入口網站來：

- 建立 SQL Database 邏輯伺服器以裝載 SQL Database
- 建立一個 SQL Database，其中不含任何資料、包含範例資料，或包含來自 SQL Database 備份的資料。
- 針對單一 IP 位址或某個 IP 位址範圍，建立伺服器層級防火牆規則。

透過下列連結，使用 [C#](sql-database-get-started-csharp.md) 或 [PowerShell](sql-database-get-started-powershell.md) 來執行相同的工作。

[AZURE.INCLUDE [登入](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [建立 SQL Database 邏輯伺服器](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [建立 SQL Database 資料庫](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [建立 SQL Database 資料庫](../../includes/sql-database-create-new-server-firewall-portal.md)]

## 後續步驟
現在您已完成本 SQL Database 教學課程並建立有一些範例資料的資料庫，您可以準備開始使用慣用的工具進行探索。

- 如果您熟悉 TRANSACT-SQL 和 SQL Server Management Studio，請了解如何[使用 SSMS 連接和查詢 SQL Database](sql-database-connect-query-ssms.md)。

- 如果您熟悉 Excel，請了解如何[使用 Excel 連接至 SQL Database](sql-database-connect-excel.md)。

- 如果您準備好開始撰寫程式碼，請在 [SQL Database 和 SQL Server 的連線庫](sql-database-libraries.md)選擇您的程式語言

- 如果您想要進一步了解如何將內部部署的 SQL Server 資料庫移動至 Azure，請參閱[將資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。

- 如果您想要使用 BCP 將某些資料從 CSV 檔案載入新資料表，請參閱[使用 BCP 將資料從 CSV 檔案載入 SQL 資料庫](sql-database-load-from-csv-with-bcp.md)。


## 其他資源

[什麼是 SQL Database？](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0803_2016-->