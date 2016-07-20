<properties 
    pageTitle="使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database | Microsoft Azure" 
    description="使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database" 
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
    ms.date="07/06/2016"
    ms.author="sstein"/>

# 使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

本文提供使用 PowerShell 匯入 BACPAC 以建立 Azure SQL Database 的說明。

BACPAC 是一種包含資料庫結構描述和資料的 .bacpac 檔案。如需詳細資訊，請參閱[資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx)中的備份封裝 (.bacpac)。

資料庫是透過由 Azure 儲存體 Blob 容器匯入的 BACPAC 來建立。如果您的 Azure 儲存體沒有 .bacpac 檔案，可依照[使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案](sql-database-export-powershell.md)中的步驟，加以建立。

> [AZURE.NOTE] Azure SQL Database 會自動為每個使用者資料庫建立並維護可供還原的備份。如需詳細資訊，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。


若要匯入 SQL Database，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- 您要匯入之資料庫的 .bacpac 檔案 (BACPAC)。BACPAC 必須位於 [Azure 儲存體帳戶 (傳統)](../storage/storage-create-storage-account.md) Blob 容器內。



[AZURE.INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]



## 為您的環境設定變數

在下列幾個變數中，您要將範例值取代為您的資料庫和儲存體帳戶的特定值。

伺服器名稱必須為目前存在於上一個步驟所選取之訂用帳戶中的伺服器，而且是您要在其中建立資料庫的目的地伺服器。請注意，不支援直接將資料庫匯入至彈性集區，但可以先匯入至單一資料庫，再將資料庫移到集區。

資料庫名稱是您想要為新資料庫命名的名稱。

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


下列變數是來自您 BACPAC 所在的儲存體帳戶。在 [Azure 入口網站](https://portal.azure.com)中，瀏覽您的儲存體帳戶以取得這些值。您可以在儲存體帳戶的刀鋒視窗中，依序按一下 [**所有設定**] 與 [**金鑰**]，以找到主要便捷鍵。

Blob 名稱是您想要用來建立資料庫之現有的 .bacpac 檔案名稱。您需要包含 .bacpac 副檔名。

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


執行 **Get-Credential** Cmdlet 時會開啟一個視窗，以詢問您的使用者名稱和密碼。請輸入 SQL Database 伺服器的系統管理員登入和密碼 (上述的 $ServerName)，而不是您的 Azure 帳戶使用者名稱和密碼。

    $credential = Get-Credential


## 匯入資料庫

這個命令會將匯入資料庫要求提交給服務。視資料庫大小而定，匯入作業可能需要一些時間才能完成。

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
    

## 監視作業的進度

執行 **New-AzureRmSqlDatabaseImport** 之後，您即可檢查要求的狀態。

如果您在要求之後立即檢查狀態，通常會傳回**擱置**或**執行中**狀態，並提供目前完成的百分比，您可以多次執行這項作業，直到在輸出中看到**狀態：已完成**為止。

執行此命令時，會提示您輸入密碼。請輸入您的 SQL Server 系統管理員登入和密碼。


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink
 


## SQL Database PowerShell 匯入指令碼


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
 
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink

    

## 後續步驟

- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)




## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0706_2016-->