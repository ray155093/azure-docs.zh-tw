---
title: "使用 PowerShell 建立 SQL 資料倉儲 | Microsoft Docs"
description: "使用 PowerShell 建立 SQL 資料倉儲"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3d13d4a0dd1d6e0b7361a57e167b06f0b717bfb4


---
# <a name="create-sql-data-warehouse-using-powershell"></a>使用 PowerShell 建立 SQL 資料倉儲
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

本文說明如何使用 PowerShell 建立 SQL 資料倉儲。

## <a name="prerequisites"></a>必要條件
若要開始，您需要：

* **Azure 帳戶**︰請瀏覽 [Azure 免費試用][Azure 免費試用]或 [MSDN Azure 點數][MSDN Azure 點數]以建立帳戶。
* **Azure SQL Server**：如需詳細資訊，請參閱[使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器][使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器]或[使用 PowerShell 建立 Azure SQL Database 邏輯伺服器][使用 PowerShell 建立 Azure SQL Database 邏輯伺服器]。
* **資源群組**︰使用與 Azure SQL Server 相同的資源群組，或參閱[如何建立資源群組][如何建立資源群組]。
* **PowerShell 1.0.3 版或更新版本**：您可以執行 **Get-Module -ListAvailable -Name Azure** 來檢查您的版本。  可透過 [Microsoft Web Platform Installer][Microsoft Web Platform Installer]安裝最新的版本。  如需安裝最新版本的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]。

> [!NOTE]
> 建立 SQL 資料倉儲可能會導致新的可計費服務。  如需價格的詳細資訊，請參閱 [SQL 資料倉儲價格][SQL 資料倉儲價格]。
> 
> 

## <a name="create-a-sql-data-warehouse"></a>建立 SQL 資料倉儲
1. 開啟 Windows PowerShell。
2. 執行此 Cmdlet 來登入 Azure 資源管理員。
   
    ```Powershell
    Login-AzureRmAccount
    ```
3. 選取目前的工作階段要使用的訂用帳戶。
   
    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. 建立資料庫。 這個範例會使用服務目標等級 "DW400" 建立名為 "mynewsqldw" 的資料庫，以部署到 "mywesteuroperesgp1" 資源群組中名為 "sqldwserver1" 的伺服器。
   
   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

必要參數如下：

* **RequestedServiceObjectiveName**：您要求的 [DWU][DWU] 數量。  支援的值為︰DW100、DW200、DW300、DW400、DW500、DW600、DW1000、DW1200、DW1500、DW2000、DW3000 和 DW6000。
* **DatabaseName**：您要建立的 SQL 資料倉儲的名稱。
* **ServerName**：您用來建立的伺服器名稱 (必須是 V12)。
* **ResourceGroupName**：您使用的資源群組。  若要尋找訂用帳戶中可用的資源，請使用 Get-AzureResource。
* **版本**：必須是 "DataWarehouse"，才能建立 SQL 資料倉儲。

選擇性參數如下：

* **CollationName**：未指定定序時的預設值為 SQL_Latin1_General_CP1_CI_AS。  無法變更資料庫的定序。
* **MaxSizeBytes**︰資料庫的預設大小上限為 10 GB。

如需參數選項的詳細資訊，請參閱 [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] 和[建立資料庫 (Azure SQL 資料倉儲)][建立資料庫 (Azure SQL 資料倉儲)]。

## <a name="next-steps"></a>後續步驟
您的 SQL 資料倉儲完成佈建之後，您可以嘗試[載入範例資料][載入範例資料]或查看如何[開發][開發]、[載入][載入]，或[移轉][移轉]。

如果您有興趣進一步了解如何以程式設計方式管理 SQL 資料倉儲，請查看我們的文章中有關 [PowerShell Cmdlet 和 REST API][PowerShell Cmdlet 和 REST API] 的使用方式。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[移轉]: ./sql-data-warehouse-overview-migrate.md
[開發]: ./sql-data-warehouse-overview-develop.md
[載入]: ./sql-data-warehouse-load-with-bcp.md
[載入範例資料]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell Cmdlet 和 REST API]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[防火牆規則]: ../sql-database-configure-firewall-settings.md

[如何安裝和設定 Azure PowerShell]: ../powershell-install-configure.md
[從 Azure 入口網站建立 SQL 資料倉儲]: ./sql-data-warehouse-get-started-provision.md
[使用 Azure 入口網站建立 Azure SQL Database 邏輯伺服器]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[使用 PowerShell 建立 Azure SQL Database 邏輯伺服器]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[如何建立資源群組]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[建立資料庫 (Azure SQL 資料倉儲)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL 資料倉儲價格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免費試用]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 點數]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


