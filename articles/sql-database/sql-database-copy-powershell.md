---
title: "使用 PowerShell 複製 Azure SQL Database | Microsoft Docs"
description: "使用 PowerShell 建立 Azure SQL Database 的複本"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6d9839d7-9303-48d2-be0f-21ce84f95a94
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: edfbc0d94d9d5b33a25788772a98173187e394b8
ms.openlocfilehash: 031a17d179cee8d6cceb9ed3aefd993add1958b8


---
# <a name="copy-an-azure-sql-database-using-powershell"></a>使用 PowerShell 複製 Azure SQL Database

本文說明如何使用 PowerShell，將 SQL 資料庫複製到相同的伺服器或不同的伺服器，或是將資料庫複製到 [彈性集區](sql-database-elastic-pool.md)。 資料庫複製作業會使用 [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx) Cmdlet。 

> [!NOTE]
> 您也可以使用 [Azure 入口網站](sql-database-copy-portal.md)或 [Transact-SQL](sql-database-copy-transact-sql.md) 複製 SQL 資料庫。
>

若要完成本文，您需要下列項目：

* Azure SQL Database (要複製的資料庫)。 如果您沒有 SQL Database，請遵循此文章中的步驟來建立： [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
* 最新版的 Azure PowerShell。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

SQL Database 的許多新功能只有在您使用 [Azure Resource Manager 部署模型](../azure-resource-manager/resource-group-overview.md)時才支援，所以範例會使用適用於 Resource Manager 的 [Azure SQL Database PowerShell Cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)。 現有的傳統部署模型 [Azure SQL Database (傳統) Cmdlet](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) 支援回溯相容性，但是建議使用 Resource Manager Cmdlet。

> [!NOTE]
> 視資料庫大小而定，複製作業可能需要一些時間才能完成。
> 
> 

## <a name="copy-a-sql-database-to-the-same-server"></a>將 SQL Database 複製到相同伺服器
若要在相同的伺服器上建立複本，請省略 `-CopyServerName` 參數 (或將它設定為相同的伺服器)。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>將 SQL Database 複製到不同伺服器
若要在不同的伺服器上建立複本，請包含 `-CopyServerName` 參數並將它設定為不同的伺服器。 「複製」  伺服器必須已經存在。 如果它是在不同的資源群組中，則必須一併包含 `-CopyResourceGroupName` 參數。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-pool"></a>將 SQL 資料庫複製到彈性集區
若要在集區中建立 SQL Database 的複本，請將 `-ElasticPoolName` 參數設定為現有的集區。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>解析登入
若要在複製作業完成之後解析登入，請參閱 [解析登入](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="example-powershell-script"></a>PowerShell 指令碼範例
下列指令碼會假設所有資源群組、伺服器及集區都已經存在 (請以您現有的資源取代變數值)。 除了資料庫複本之外，所有項目都必須存在。

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

    # Copy a database into an elastic pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## <a name="next-steps"></a>後續步驟
* 若要了解將資料庫複製到不同的邏輯伺服器時如何管理使用者與登入，請參閱[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。
* 若要使用 PowerShell 將資料庫匯出至 BACPAC 檔案，請參閱[使用 PowerShell 將資料庫匯出至 BACPAC](sql-database-export-powershell.md)。
* [業務續航力概觀](sql-database-business-continuity.md)
* [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

## <a name="additional-resources"></a>其他資源
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx)
* [Get AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687\(v=azure.300\).aspx)
* [管理登入](sql-database-manage-logins.md)
* [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)
* [將資料庫匯出至 BACPAC](sql-database-export.md)
* [商務持續性概觀](sql-database-business-continuity.md)
* [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)
* [Azure SQL Database PowerShell Cmdlet 參考](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)




<!--HONumber=Feb17_HO2-->


