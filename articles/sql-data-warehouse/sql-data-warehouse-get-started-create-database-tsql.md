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
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# 使用 Transact-SQL (TSQL) 建立 SQL 資料倉儲資料庫

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

本文說明如何使用 T-SQL 建立 SQL 資料倉儲。

## 必要條件

若要開始，您需要：

- **Azure 帳戶**︰請瀏覽 [Azure 免費試用][]或 [MSDN Azure 點數][]以建立帳戶。
- **Azure SQL Server**︰如需詳細資訊，請參閱[使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器][]或[使用 PowerShell 建立 Azure SQL Database 邏輯伺服器][]。
- **資源群組**︰使用與 Azure SQL Server 相同的資源群組，或參閱[如何建立資源群組][]。
- **執行 T-SQL 的環境**︰您可以使用 [Visual Studio][Installing Visual Studio and SSDT]、[sqlcmd][] 或 [SSMS][] 執行 T-SQL。

> [AZURE.NOTE] 建立 SQL 資料倉儲可能會導致新的可計費服務。如需價格的詳細資訊，請參閱 [SQL 資料倉儲價格][]。

## 使用 Visual Studio 建立資料庫

如果您不熟悉 Visual Studio，請參閱[查詢 Azure SQL 資料倉儲 (Visual Studio)][] 一文。若要開始，請在 Visual Studio 中開啟 SQL Server 物件總管，並連接到將要裝載 SQL 資料倉儲資料庫的伺服器。連接後，您即可對 **master** 資料庫執行下列 SQL 命令來建立 SQL 資料倉儲。此命令會建立服務目標為 DW400 的資料庫 MySqlDwDb，並允許此資料庫成長至大小上限 10 TB。

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## 使用 sqlcmd 建立資料庫

或者，您可以在命令提示字元執行下列命令，以使用 sqlcmd 執行相同的命令。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

未指定定序時的預設值為 COLLATE SQL\_Latin1\_General\_CP1\_CI\_AS。`MAXSIZE` 可以介於 250 GB 與 240 TB 之間。`SERVICE_OBJECTIVE` 可以介於 DW100 與 DW2000 [DWU][] 之間。如需所有有效值的清單，請參閱 MSDN 文件中的 [CREATE DATABASE][]。使用 [ALTER DATABASE][] T-SQL 命令可以變更 MAXSIZE 和 SERVICE\_OBJECTIVE。建立資料庫定序之後，就無法進行變更。變更 SERVICE\_OBJECTIVE 時應格外小心，因為變更 DWU 會導致服務重新啟動，而取消所有進行中的查詢。變更 MAXSIZE 並不會重新啟動服務，因為這只是簡單的中繼資料作業。

## 後續步驟

您的 SQL 資料倉儲完成佈建之後，您可以[載入範例資料][]或查看如何[開發][]、[載入][]，或[移轉][]。

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[查詢 Azure SQL 資料倉儲 (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[移轉]: sql-data-warehouse-overview-migrate.md
[開發]: sql-data-warehouse-overview-develop.md
[載入]: sql-data-warehouse-overview-load.md
[載入範例資料]: sql-data-warehouse-load-sample-databases.md
[使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[使用 PowerShell 建立 Azure SQL Database 邏輯伺服器]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[如何建立資源群組]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL 資料倉儲價格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免費試用]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 點數]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0831_2016-->