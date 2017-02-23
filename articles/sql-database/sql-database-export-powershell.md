---
title: "PowerShell：將 Azure SQL Database 匯出到 BACPAC 檔案 | Microsoft Docs"
description: "使用 PowerShell 將 Azure SQL Database 匯出到 BACPAC 檔案"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: 162147607baa36de0487cebc06e7ada20f3dd0c0


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>使用 PowerShell 將 Azure SQL 資料庫或 SQL Server 匯出到 BACPAC 檔案

本文提供指示，說明如何使用 PowerShell，將 Azure SQL 資料庫或 SQL Server 資料庫匯出到 BACPAC 檔案 (儲存於 Azure Blob 儲存體中)。 如需有關匯出至 BACPAC 檔案的概觀，請參閱[匯出至 BACPAC](sql-database-export.md)。

> [!NOTE]
> 您也可以使用 [Azure 入口網站](sql-database-export-portal.md)、[SQL Server Management Studio](sql-database-export-ssms.md) 或 [SQLPackage](sql-database-export-sqlpackage.md)，將 Azure SQL 資料庫檔案匯出到 BACPAC 檔案。
>

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要下列項目：

* Azure 訂用帳戶。
* Azure SQL Database。
* 用來在標準儲存體中儲存 BACPAC 的 [Azure 標準儲存體帳戶](../storage/storage-create-storage-account.md)與 Blob 容器。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>匯出您的資料庫
[New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx) Cmdlet 會向服務提交匯出資料庫要求。 視資料庫大小而定，匯出作業可能需要一些時間才能完成。

> [!IMPORTANT]
> 若要保證在交易上一致的 BACPAC 檔案，您應該先 [建立您的資料庫複本](sql-database-copy-powershell.md)，然後匯出資料庫複本。
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>監視匯出作業的進度
執行 [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx) 之後，您即可透過執行 [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx) 來檢查要求的狀態。 如果在要求後立即執行此 Cmdlet，通常會傳回 **Status : InProgress**。 當您看見 **Status: Succeeded** 時，便代表匯出已完成。

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>匯出 SQL Database 範例
下列範例會將現有 SQL Database 匯出到 BACPAC，並示範檢查匯出作業的方法。

若要執行範例，您必須將幾個變數取代為您資料庫和儲存體帳戶的特定值。 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的儲存體帳戶以取得儲存體帳戶名稱、Blob 容器名稱及金鑰值。 您可以按一下儲存體帳戶刀鋒視窗上的 [存取金鑰]  來找到金鑰。

使用您特定 Azure 資源的值來取代下列 `VARIABLE-VALUES` 。 資料庫名稱便是您想要匯出的現有資料庫。

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
    $creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>使用 Azure 自動化進行自動匯出

Azure SQL 資料庫自動匯出目前為預覽狀態，並會在 2017 年 3 月 1 日停用。 2016 年 12 月 1 日開始，您將無法再於任何 SQL Database 上設定自動匯出。 所有現有的自動匯出作業會繼續運作至 2017 年 3 月 1 日。 2016 年 12 月 1 日之後，您可以使用[長期的備份保留](sql-database-long-term-retention.md)或[Azure 自動化](../automation/automation-intro.md)，根據您選擇的排程使用 PowerShell 定期封存 SQL Database。 如需範例指令碼，您可以[從 GitHub 下載範例指令碼](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)。 


## <a name="next-steps"></a>後續步驟
* 若要了解如何使用 PowerShell 匯入 Azure SQL Database，請參閱 [使用 PowerShell 匯入 BACPAC](sql-database-import-powershell.md)。
* 若要了解使用 SQLPackage 匯入 BACPAC，請參閱[使用 SqlPackage 將 BACPAC 匯入 Azure SQL Database](sql-database-import-sqlpackage.md)
* 若要了解使用 Azure 入口網站匯入 BACPAC，請參閱[使用 Azure 入口網站將 BACPAC 匯入 Azure SQL Database](sql-database-import-portal.md)
* 如需有關整個 SQL Server 資料庫移轉程序的討論，包括效能建議，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。
* 若要了解除了匯出的資料庫，還有 Azure SQL 資料庫備份的長期備份保留也可達到封存目的，請參閱[長期備份保留](sql-database-long-term-retention.md)
* 若要了解如何將 BACPAC 匯入 SQL Server 資料庫，請參閱 [將 BACPAC 匯入 SQL Server 資料庫](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>其他資源
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)




<!--HONumber=Feb17_HO2-->


