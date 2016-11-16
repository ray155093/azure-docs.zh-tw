---
title: "使用 TSQL 建立 SQL 資料倉儲 | Microsoft Docs"
description: "了解如何使用 TSQL 建立 Azure SQL 資料倉儲"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 443e74834a7181560e812ce31db460ef2c4ff911


---
# <a name="create-a-sql-data-warehouse-database-by-using-transactsql-tsql"></a>使用 Transact-SQL (TSQL) 建立 SQL 資料倉儲資料庫
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

本文說明如何使用 T-SQL 建立 SQL 資料倉儲。

## <a name="prerequisites"></a>必要條件
若要開始，您需要： 

* **Azure 帳戶**︰請瀏覽 [Azure 免費試用][Azure 免費試用]或 [MSDN Azure 點數][MSDN Azure 點數]以建立帳戶。
* **Azure SQL Server**：如需詳細資訊，請參閱[使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器][使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器]或[使用 PowerShell 建立 Azure SQL Database 邏輯伺服器][使用 PowerShell 建立 Azure SQL Database 邏輯伺服器]。
* **資源群組**︰使用與 Azure SQL Server 相同的資源群組，或參閱[如何建立資源群組][如何建立資源群組]。
* **執行 T-SQL 的環境**：您可以使用 [Visual Studio][安裝 Visual Studio 及 SSDT]、[sqlcmd][sqlcmd] 或 [SSMS][SSMS] 執行 T-SQL。

> [!NOTE]
> 建立 SQL 資料倉儲可能會導致新的可計費服務。  如需價格的詳細資訊，請參閱 [SQL 資料倉儲價格][SQL 資料倉儲價格]。
> 
> 

## <a name="create-a-database-with-visual-studio"></a>使用 Visual Studio 建立資料庫
如果您不熟悉 Visual Studio，請參閱[查詢 Azure SQL 資料倉儲 (Visual Studio)][查詢 Azure SQL 資料倉儲 (Visual Studio)] 一文。  若要開始，請在 Visual Studio 中開啟 SQL Server 物件總管，並連接到將要裝載 SQL 資料倉儲資料庫的伺服器。  連接後，您即可對 **master** 資料庫執行下列 SQL 命令來建立 SQL 資料倉儲。  此命令會建立服務目標為 DW400 的資料庫 MySqlDwDb，並允許此資料庫成長至大小上限 10 TB。

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>使用 sqlcmd 建立資料庫
或者，您可以在命令提示字元執行下列命令，以使用 sqlcmd 執行相同的命令。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

未指定定序時的預設值為 COLLATE SQL_Latin1_General_CP1_CI_AS。  `MAXSIZE` 可以介於 250 GB 與 240 TB 之間。  `SERVICE_OBJECTIVE` 可以介於 DW100 與 DW2000 [DWU][DWU]之間。  如需所有有效值的清單，請參閱 MSDN 文件中的 [CREATE DATABASE][CREATE DATABASE]。  使用 [ALTER DATABASE][ALTER DATABASE] T-SQL 命令可以變更 MAXSIZE 和 SERVICE_OBJECTIVE。  建立資料庫定序之後，就無法進行變更。   變更 SERVICE_OBJECTIVE 時應格外小心，因為變更 DWU 會導致服務重新啟動，而取消所有進行中的查詢。  變更 MAXSIZE 並不會重新啟動服務，因為這只是簡單的中繼資料作業。

## <a name="next-steps"></a>後續步驟
您的 SQL 資料倉儲完成佈建之後，您可以[載入範例資料][載入範例資料]或查看如何[開發][開發]、[載入][載入]，或[移轉][移轉]。

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[從 Azure 入口網站建立 SQL 資料倉儲]: sql-data-warehouse-get-started-provision.md
[查詢 Azure SQL 資料倉儲 (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[移轉]: sql-data-warehouse-overview-migrate.md
[開發]: sql-data-warehouse-overview-develop.md
[載入]: sql-data-warehouse-overview-load.md
[載入範例資料]: sql-data-warehouse-load-sample-databases.md
[使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[使用 PowerShell 建立 Azure SQL Database 邏輯伺服器]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[如何建立資源群組]: ../resource-group-template-deploy-portal.md#create-resource-group
[安裝 Visual Studio 及 SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL 資料倉儲價格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免費試用]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 點數]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


