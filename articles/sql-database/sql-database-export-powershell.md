---
title: 使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案
description: 使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# 使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-export.md)
> * [PowerShell](sql-database-export-powershell.md)
> 
> 

本文提供指示，說明如何使用 PowerShell，將 Azure SQL Database 封存到 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案 (儲存於 Azure Blob 儲存體中)。

當您需要建立 Azure SQL Database 的封存檔時，可以將資料庫結構描述和資料匯出到 BACPAC 檔案。BACPAC 檔案就是副檔名為 .bacpac 的 ZIP 檔案。BACPAC 檔案可以稍後儲存在 Azure Blob 儲存體，或是位於內部部署位置的本機儲存體中。BACPAC 檔案也可以匯回 Azure SQL Database 或內部部署的 SQL Server 安裝。

**考量**

* 為了讓封存檔處於交易一致狀態，您必須確定在匯出期間未發生任何寫入活動，或者是從 Azure SQL Database 的[交易一致性複本](sql-database-copy.md)匯出。
* 封存到 Azure Blob 儲存體的 BACPAC 檔案大小上限為 200 GB。若要將較大的 BACPAC 檔案封存到本機儲存體，請使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式。此公用程式隨附於 Visual Studio 和 SQL Server。您也可以[下載](https://msdn.microsoft.com/library/mt204009.aspx)最新版的 SQL Server Data Tools 以取得此公用程式。
* 不支援使用 BACPAC 檔案封存 Azure 進階儲存體。
* 如果匯出作業超過 20 個小時，它可能會被取消。若要增加匯出期間的效能，您可以︰
  * 暫時提高您的服務等級。
  * 在匯出期間停止所有讀取及寫入活動。
  * 在所有大型資料表上搭配使用 [叢集索引](https://msdn.microsoft.com/library/ms190457.aspx)和非 null 值。若沒有叢集索引，如果要花超過 6-12 小時，匯出可能會失敗。這是因為匯出服務需要完成資料表掃描，以便嘗試匯出整份資料表。用來判斷資料表是否已針對匯出進行最佳化的最佳方式是執行 DBCC SHOW\_STATISTICS，並確定 RANGE\_HI\_KEY 不是 null 且其值具有良好的分佈。如需詳細資訊，請參閱 [DBCC SHOW\_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 並非用於備份和還原作業。Azure SQL Database 會自動為每個使用者資料庫建立備份。如需詳細資訊，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
> 
> 

若要完成本文，您需要下列項目：

* Azure 訂用帳戶。
* Azure SQL Database。
* 用來在標準儲存體中儲存 BACPAC 的 [Azure 標準儲存體帳戶](../storage/storage-create-storage-account.md)與 Blob 容器。

[!INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]

## 匯出您的資料庫
[New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx) Cmdlet 會向服務提交匯出資料庫要求。視資料庫大小而定，匯出作業可能需要一些時間才能完成。

> [!IMPORTANT]
> 若要保證在交易上一致的 BACPAC 檔案，您應該先[建立您的資料庫複本](sql-database-copy-powershell.md)，然後匯出資料庫複本。
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## 監視匯出作業的進度
執行 **New-AzureRmSqlDatabaseExport** 之後，您即可透過執行 [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx) 來檢查要求的狀態。如果在要求後立即執行此 Cmdlet，通常會傳回 **Status : InProgress**。當您看見 **Status : Succeeded** 時，便代表匯出已完成。

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## 匯出 SQL Database 範例
下列範例會將現有 SQL Database 匯出到 BACPAC，並示範檢查匯出作業的方法。

若要執行範例，您必須將幾個變數取代為您資料庫和儲存體帳戶的特定值。在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的儲存體帳戶以取得儲存體帳戶名稱、Blob 容器名稱及金鑰值。您可以按一下儲存體帳戶刀鋒視窗上的 [存取金鑰] 來找到金鑰。

使用您特定 Azure 資源的值來取代下列 `VARIABLE-VALUES`。資料庫名稱便是您想要匯出的現有資料庫。

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## 後續步驟
* 若要了解如何使用 PowerShell 匯入 Azure SQL Database，請參閱[使用 PowerShell 匯入 BACPAC](sql-database-import-powershell.md)。

## 其他資源
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx)

<!---HONumber=AcomDC_0817_2016-->