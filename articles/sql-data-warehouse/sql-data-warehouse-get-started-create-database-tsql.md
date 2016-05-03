<properties
   pageTitle="使用 TSQL 建立 SQL 資料倉儲 | Microsoft Azure"
   description="了解如何使用 TSQL 建立 Azure SQL 資料倉儲"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# 使用 Transact-SQL (TSQL) 建立 SQL 資料倉儲資料庫

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

本文將說明如何使用 Transact-SQL (TSQL) 建立 SQL 資料倉儲資料庫。

## 開始之前

若要完成這篇文章中的步驟，您需要下列項目︰

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- V12 邏輯 SQL Server。您將需要 V12 SQL Server 來建立 SQL 資料倉儲。如果您沒有 V12 邏輯 SQL Server，請參閱[如何從 Azure 入口網站建立 SQL 資料倉儲][]一文中的**設定和建立伺服器**。
- 。如需免費的 Visual Studio，請參閱 [Visual Studio 下載][]頁面。


> [AZURE.NOTE] 建立新的 SQL 資料倉儲可能會導致新的可計費服務。如需價格的詳細資訊，請參閱 [SQL 資料倉儲價格][]。

## 使用 Visual Studio 建立資料庫

如果您不熟悉 Visual Studio，請參閱[使用 Visual Studio 連接到 SQL 資料倉儲][]一文。若要開始，請在 Visual Studio 中開啟 SQL Server 物件總管，並連接到將要裝載 SQL 資料倉儲資料庫的伺服器。連接後，您即可對 **master** 資料庫執行下列 SQL 命令來建立 SQL 資料倉儲。此命令會建立服務目標為 DW400 的資料庫 MySqlDwDb，並允許此資料庫成長至大小上限 10 TB。

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## 使用 sqlcmd 建立資料庫

或者，您可以在命令提示字元執行下列命令，以使用 sqlcmd 執行相同的命令。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

**MAXSIZE** 和 **SERVICE\_OBJECTIVE** 參數可指定資料庫可使用的最大磁碟空間，以及配置給您的資料倉儲執行個體的計算資源。服務目標基本上是以線性方式隨著 DWU 大小調整的 CPU 和記憶體配置。

MAXSIZE 可以介於 250 GB 與 60 TB 之間。服務目標可以介於 DW100 與 DW2000 之間。如需所有 MAXSIZE 和 SERVICE\_OBJECTIVE 有效值的完整清單，請參閱 MSDN 文件中的 [CREATE DATABASE][]。使用 [ALTER DATABASE][] T-SQL 命令也可以變更 MAXSIZE 和 SERVICE\_OBJECTIVE。變更 SERVICE\_OBJECTIVE 時應使用警告，因為這會導致服務重新啟動而取消所有進行中的查詢。變更 MAXSIZE 時則不需要此警告，因為這只是簡單的中繼資料作業。

## 後續步驟
您的 SQL 資料倉儲完成佈建之後，您可以[載入範例資料][]或查看如何[開發][]、[載入][]，或[移轉][]。

<!--Article references-->
[如何從 Azure 入口網站建立 SQL 資料倉儲]: sql-data-warehouse-get-started-provision.md
[使用 Visual Studio 連接到 SQL 資料倉儲]: sql-data-warehouse-get-started-connect.md
[移轉]: sql-data-warehouse-overview-migrate.md
[開發]: sql-data-warehouse-overview-develop.md
[載入]: sql-data-warehouse-overview-load.md
[載入範例資料]: sql-data-warehouse-get-started-manually-load-samples.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[SQL 資料倉儲價格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Visual Studio 下載]: https://www.visualstudio.com/downloads/download-visual-studio-vs

<!---HONumber=AcomDC_0427_2016-->