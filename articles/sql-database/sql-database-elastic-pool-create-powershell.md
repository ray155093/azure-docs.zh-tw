---
title: 使用 PowerShell 建立新的彈性資料庫集區 | Microsoft Docs
description: 了解如何藉由建立可調整的彈性資料庫集區來管理多個資料庫，進而使用 PowerShell 來相應放大Azure SQL Database 資源。
services: sql-database
documentationcenter: ''
author: srinia
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia

---
# 使用 PowerShell 建立新的彈性資料庫集區
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
> 
> 

了解如何使用 PowerShell Cmdlet 建立[彈性資料庫集區](sql-database-elastic-pool.md)。

如需常見的錯誤碼，請參閱 [SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題](sql-database-develop-error-messages.md)。

> [!NOTE]
> 彈性集區已在所有 Azure 區域中正式運作 (GA)，但美國中北部和印度西部除外，在這些區域目前是提供預覽版。我們將儘速在這些區域提供彈性集區的 GA。此外，彈性集區目前不支援使用[記憶體內部 OLTP 或記憶體內部分析](sql-database-in-memory.md)的資料庫。
> 
> 

您必須是執行 Azure PowerShell 1.0 或更高版本。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## 建立新的集區
[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) Cmdlet 會建立新集區。每個集區的 eDTU 值、最小和最大 Dtu 會受限於服務層值 (基本、標準或進階)。請參閱[彈性集區和彈性資料庫的 eDTU 和儲存體限制](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)。

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## 在集區中建立新的彈性資料庫
使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) Cmdlet，並將 **ElasticPoolName** 參數設定為目標集區。若要將現有的資料庫移入集區，請參閱[將資料庫移入彈性集區](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool)。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 建立集區並填入多個新資料庫
使用入口網站或一次只建立單一資料庫的 PowerShell Cmdlet，在集區中建立大量資料庫可能需要花費一些時間。若要自動建立成新的集區，請參閱 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。

## 範例：使用 PowerShell 建立集區
此指令碼會建立新的 Azure 資源群組和新的伺服器。出現提示時，提供適用於新伺服器的系統管理員使用者名稱和密碼 (而非您 Azure 認證)。

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## 後續步驟
* [管理集區](sql-database-elastic-pool-manage-powershell.md)
* [建立彈性工作](sql-database-elastic-jobs-overview.md) 彈性工作可讓您對集區中任意數目的資料庫執行 T-SQL 指令碼。
* [使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)︰使用彈性資料庫工具來相應放大。

<!---HONumber=AcomDC_0907_2016-->