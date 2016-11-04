---
title: 使用 PowerShell 複製 Azure SQL Database | Microsoft Docs
description: 使用 PowerShell 建立 Azure SQL Database 的複本
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# 使用 PowerShell 複製 Azure SQL Database
> [!div class="op_single_selector"]
> * [概觀](sql-database-copy.md)
> * [Azure 入口網站](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

本文說明如何使用 PowerShell，將 SQL Database 複製到相同的伺服器或不同的伺服器，或是將資料庫複製到[彈性資料庫集區](sql-database-elastic-pool.md)。資料庫複製作業會使用 [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx) Cmdlet。

若要完成本文，您需要下列項目：

* Azure SQL Database (要複製的資料庫)。如果您沒有 SQL Database，請遵循此文章中的步驟來建立：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
* 最新版的 Azure PowerShell。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

SQL Database 的許多新功能只有在使用 [Azure Resource Manager 部署模型](../resource-group-overview.md) 時才支援，所以範例會使用適用於 Resource Manager 的 [Azure SQL Database PowerShell Cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)。為了提供回溯相容性，也支援現有的傳統部署模型 [Azure SQL Database (傳統) Cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx)，但是建議使用 Resource Manager Cmdlet。

> [!NOTE]
> 視資料庫大小而定，複製作業可能需要一些時間才能完成。
> 
> 

## 將 SQL Database 複製到相同伺服器
若要在相同的伺服器上建立複本，請省略 `-CopyServerName` 參數 (或將它設定為相同的伺服器)。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## 將 SQL Database 複製到不同伺服器
若要在不同的伺服器上建立複本，請包含 `-CopyServerName` 參數並將它設定為不同的伺服器。「複製」伺服器必須已經存在。如果它是在不同的資源群組中，則必須一併包含 `-CopyResourceGroupName` 參數。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## 將 SQL Database 複製到彈性資料庫集區
若要在集區中建立 SQL Database 的複本，請將 `-ElasticPoolName` 參數設定為現有的集區。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## 解析登入
若要在複製作業完成之後解析登入，請參閱[解析登入](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## PowerShell 指令碼範例
下列指令碼會假設所有資源群組、伺服器及集區都已經存在 (請以您現有的資源取代變數值)。除了資料庫複本之外，所有項目都必須存在。

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId


    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"

    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"

    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName

    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName

    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## 後續步驟
* 如需複製 Azure SQL Database 的概觀，請參閱[複製 Azure SQL Database](sql-database-copy.md)。
* 若要使用 Azure 入口網站來複製資料庫，請參閱[使用 Azure 入口網站複製 Azure SQL Database](sql-database-copy-portal.md)。
* 若要使用 Transact-SQL 複製資料庫，請參閱[使用 T-SQL 複製 Azure SQL Database](sql-database-copy-transact-sql.md)。
* 請參閱[如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md)，以了解如何在將資料庫複製到不同的邏輯伺服器時管理使用者與登入。

## 其他資源
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
* [Get AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
* [管理登入](sql-database-manage-logins.md)
* [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)
* [將資料庫匯出至 BACPAC](sql-database-export.md)
* [商務持續性概觀](sql-database-business-continuity.md)
* [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)
* [Azure SQL Database PowerShell Cmdlet 參考](https://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0914_2016-->