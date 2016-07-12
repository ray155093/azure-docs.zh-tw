<properties
   pageTitle="使用 Powershell 建立 SQL 資料倉儲 | Microsoft Azure"
   description="使用 Powershell 建立 SQL 資料倉儲"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# 使用 Powershell 建立 SQL 資料倉儲

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## 必要條件
開始之前，請確定您已備妥下列必要條件。

- **Azure 帳戶**︰請參閱 [Azure 免費試用][]或 [MSDN Azure 點數][]以建立帳戶。
- **V12 Azure SQL Server**︰請參閱[使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器][]或[使用 PowerShell 建立 Azure SQL Database 邏輯伺服器][]。
- **資源群組名稱**︰使用和 V12 Azure SQL Server 相同的資源群組，或參閱[資源群組][]來建立新的資源群組。
- **PowerShell 1.0.3 版或更新版本**：您可以執行 **Get-Module -ListAvailable -Name Azure** 來檢查您的版本。可透過 [Microsoft Web Platform Installer][] 安裝最新的版本。如需安裝最新版本的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][]。

> [AZURE.NOTE] 建立新的 SQL 資料倉儲可能會導致新的可計費服務。如需價格的詳細資訊，請參閱 [SQL 資料倉儲價格][]。

## 建立 SQL 資料倉儲資料庫
1. 開啟 Windows PowerShell。
2. 執行此 Cmdlet 來登入 Azure 資源管理員。

	```Powershell
	Login-AzureRmAccount
	```
	
3. 選取目前的工作階段要使用的訂用帳戶。

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  建立資料庫。這個範例會使用服務目標等級 "DW400" 建立名為 "mynewsqldw" 的新資料庫，以部署到 "mywesteuroperesgp1" 資源群組中名為 "sqldwserver1" 的伺服器。

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

此 Cmdlet 所需的參數：

- **RequestedServiceObjectiveName**：您要求的 [DWU][] 數量。支援的值為︰DW100、DW200、DW300、DW400、DW500、DW600、DW1000、DW1200、DW1500 和 DW2000。
- **DatabaseName**：您要建立的 SQL 資料倉儲的名稱。
- **ServerName**：您用來建立的伺服器名稱 (必須是 V12)。
- **ResourceGroupName**：您使用的資源群組。若要尋找訂用帳戶中可用的資源，請使用 Get-AzureResource。
- **版本**：您必須將版本設定為 "DataWarehouse"，才能建立 SQL 資料倉儲。

如需參數選項的詳細資訊，請參閱[建立資料庫 (Azure SQL 資料倉儲)][]。如需命令參考，請參閱 [New-AzureRmSqlDatabase][]

## 後續步驟
SQL 資料倉儲完成佈建之後，建議您試著[載入範例資料][]，或查看如何[開發][]、[載入][]或[移轉][]。

如果您有興趣進一步了解如何以程式設計方式管理 SQL 資料倉儲，請查看我們的文章中有關 [PowerShell Cmdlet 和 REST API][] 的使用方式。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[移轉]: ./sql-data-warehouse-overview-migrate.md
[開發]: ./sql-data-warehouse-overview-develop.md
[載入]: ./sql-data-warehouse-load-with-bcp.md
[載入範例資料]: ./sql-data-warehouse-get-started-load-sample-databases.md
[PowerShell Cmdlet 和 REST API]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[如何安裝和設定 Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[使用 PowerShell 建立 Azure SQL Database 邏輯伺服器]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[資源群組]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[建立資料庫 (Azure SQL 資料倉儲)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL 資料倉儲價格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免費試用]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 點數]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0706_2016-->