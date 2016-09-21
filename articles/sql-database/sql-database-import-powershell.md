<properties
    pageTitle="使用 PowerShell 匯入 BACPAC 檔案以建立 Azure SQL Database | Microsoft Azure"
    description="使用 PowerShell 匯入 BACPAC 檔案以建立 Azure SQL Database"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# 使用 PowerShell 匯入 BACPAC 檔案以建立 Azure SQL Database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

本文提供使用 PowerShell 匯入 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案以建立 Azure SQL Database 的說明。

資料庫是透過從 Azure 儲存體 Blob 容器匯入的 BACPAC 檔案 (.bacpac) 來建立。如果您的 Azure 儲存體沒有 BACPAC 檔案，請參閱[使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案](sql-database-export-powershell.md)。如果您已經有 BACPAC 檔案但不在 Azure 儲存體中，請參閱[使用 AzCopy 輕鬆地將它上傳至 Azure 儲存體帳戶](../storage/storage-use-azcopy.md#blob-upload)。

> [AZURE.NOTE] Azure SQL Database 會自動為每個使用者資料庫建立並維護可供還原的備份。如需詳細資訊，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。


若要匯入 SQL Database，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- 您要匯入之資料庫的 BACPAC 檔案。BACPAC 必須位於 [Azure 儲存體帳戶](../storage/storage-create-storage-account.md) Blob 容器內。



[AZURE.INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]



## 為您的環境設定變數

在下列幾個變數中，您要將範例值取代為您的資料庫和儲存體帳戶的特定值。

伺服器名稱應該為存在於上一個步驟所選取的訂用帳戶中的現有伺服器。它應該是您想要於其中建立資料庫的伺服器。不支援直接將資料庫匯入彈性集區。但您可以先匯入單一資料庫，然後將資料庫移到集區。

資料庫名稱是您想要為新資料庫命名的名稱。

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


下列變數是來自您 BACPAC 所在的儲存體帳戶。在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的儲存體帳戶以取得這些值。您可以在儲存體帳戶的刀鋒視窗中，依序按一下 [**所有設定**] 與 [**金鑰**]，以找到主要存取金鑰。

Blob 名稱是您想要用來建立資料庫之現有的 BACPAC 檔案名稱。您需要包含 .bacpac 副檔名。

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


執行 [Get-Credential](https://msdn.microsoft.com/library/hh849815.aspx) Cmdlet 時會開啟一個視窗，詢問您的使用者名稱和密碼。請輸入 SQL Database 伺服器的系統管理員登入和密碼 (上述的 $ServerName)，而不是您的 Azure 帳戶使用者名稱和密碼。

    $credential = Get-Credential


## 匯入資料庫

這個命令會將匯入資料庫要求提交給服務。視資料庫大小而定，匯入作業可能需要一些時間才能完成。

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## 監視作業的進度

執行 [New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/mt707793.aspx) 之後，您即可透過執行 [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx) 來檢查要求的狀態。

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## SQL Database PowerShell 匯入指令碼


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## 後續步驟

- 若要了解如何連接並查詢匯入的 SQL Database，請參閱[使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)

<!----HONumber=AcomDC_0907_2016-->