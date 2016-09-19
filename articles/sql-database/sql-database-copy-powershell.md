<properties 
    pageTitle="使用 PowerShell 複製 Azure SQL Database | Microsoft Azure" 
    description="使用 PowerShell 建立 Azure SQL Database 的複本" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 PowerShell 複製 Azure SQL Database


> [AZURE.SELECTOR]
- [概觀](sql-database-copy.md)
- [Azure 入口網站](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

下列這些步驟說明如何利用 PowerShell，將 SQL Database 複製到相同的伺服器或不同的伺服器。資料庫複製作業會使用 [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) Cmdlet。


若要完成本文，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database。如果您沒有 SQL Database，請遵循此文章中的步驟來建立：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- Azure PowerShell。您可以執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) 來下載和安裝 Azure PowerShell 模組。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。



## 複製您的 SQL Database

在下列幾個變數中，您要將範例值取代為您的資料庫和伺服器的特定值。以您的環境的值取代預留位置值：

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





### 將 SQL Database 複製到相同伺服器

這個命令會將複製資料庫要求提交給服務。視資料庫大小而定，複製作業可能需要一些時間才能完成。

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

使用 Azure Resource Manager Cmdlet：

    # Copy a database to the same server
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -CopyDatabaseName $PartnerDatabaseName

### 將 SQL Database 複製到不同伺服器

這個命令會將複製資料庫要求提交給服務。視資料庫大小而定，複製作業可能需要一些時間才能完成。

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
使用 Azure Resource Manager Cmdlet：

    # Copy a database to a different server
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -CopyServerName $PartnerServerName -CopyDatabaseName $PartnerDatabaseName

## 監視複製作業的進度

執行 **Start-AzureSqlDatabaseExport** 之後，您即可檢查複製要求的狀態。如果您在要求之後立即執行此作業，通常會傳回 [狀態：擱置] 或 [狀態：執行中]，以便您多次執行這項作業，直到您在輸出中看到 [狀態：已完成] 為止。


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName

## 解析登入

若要在複製作業完成之後解析登入，請參閱[解析登入](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## PowerShell 指令碼範例

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## 後續步驟

- 如需複製 Azure SQL Database 的概觀，請參閱[複製 Azure SQL Database](sql-database-copy.md)。
- 若要使用 Azure 入口網站複製資料庫，請參閱[使用 Azure 入口網站複製 Azure SQL Database](sql-database-copy-portal.md)。
- 若要使用 Transact-SQL 複製資料庫，請參閱[使用 T-SQL 複製 Azure SQL Database](sql-database-copy-transact-sql.md)。
- 請參閱[如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md)，以了解如何在將資料庫複製到不同的邏輯伺服器時管理使用者與登入。


## 其他資源

- [管理登入](sql-database-manage-logins.md)
- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)
- [將資料庫匯出至 BACPAC](sql-database-export.md)
- [商務持續性概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0907_2016-->