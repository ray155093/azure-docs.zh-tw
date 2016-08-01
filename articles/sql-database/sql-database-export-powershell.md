<properties 
    pageTitle="使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案" 
    description="使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


本文提供指示，說明如何使用 PowerShell，將 Azure SQL Database 封存到 Azure Blob 儲存體中儲存的 BACPAC 檔案。

當您需要建立 Azure SQL Database 的封存檔時，可以將資料庫結構描述和資料匯出到 BACPAC 檔案。BACPAC 檔案就是副檔名為 BACPAC 的 ZIP 檔案。BACPAC 檔案可以稍後儲存在 Azure Blob 儲存體，或在內部部署位置的本機儲存體中，之後再匯入至 Azure SQL Database 或 SQL Server 內部部署安裝。

***注意事項***

- 為了讓封存檔處於交易一致狀態，您必須確定在匯出期間未發生任何寫入活動，或是從 Azure SQL Database 的[交易一致性複本](sql-database-copy.md)匯出
- 封存到 Azure Blob 儲存體的 BACPAC 檔案大小上限為 200 GB。請使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式，將較大的 BACPAC 檔案封存到本機儲存體。此公用程式隨附於 Visual Studio 和 SQL Server。您也可以[下載](https://msdn.microsoft.com/library/mt204009.aspx)最新版的 SQL Server Data Tools 以取得此公用程式。
- 不支援使用 BACPAC 檔案封存 Azure 進階儲存體。
- 如果匯出作業超過 20 個小時，它可能會被取消。若要增加匯出期間的效能，您可以︰
 - 暫時提高您的服務等級
 - 在匯出期間停止所有讀取及寫入活動
 - 在所有大型資料表上使用叢集索引。若沒有叢集索引，如果要花超過 6-12 小時，匯出可能會失敗。這是因為匯出服務需要完成資料表掃描，以便嘗試匯出整份資料表。
 
> [AZURE.NOTE] BACPAC 並非用於備份和還原作業。Azure SQL Database 會自動為每個使用者資料庫建立備份。如需詳細資訊，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。

若要完成本文，您需要下列項目：

- Azure 訂用帳戶。
- Azure SQL Database。
- 用來在標準儲存體中儲存 BACPAC 的 [Azure 標準儲存體帳戶](../storage/storage-create-storage-account.md)與 Blob 容器。


[AZURE.INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]


## 為您的特定環境設定變數

在下列幾個變數中，您要將範例值取代為您的資料庫和儲存體帳戶的特定值。

取代為您的特定值。

    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"


在 [Azure 入口網站](https://portal.azure.com)中，瀏覽您的儲存體帳戶以取得這些值。您可以在儲存體帳戶的刀鋒視窗中，依序按一下 [**所有設定**] 與 [**金鑰**]，以找到主要便捷鍵。

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


執行 **Get-Credential** Cmdlet 時會開啟一個視窗，以詢問您的使用者名稱和密碼。輸入 SQL Server 的系統管理員登入和密碼 (而不是 Azure 帳戶的使用者名稱和密碼)。

    $credential = Get-Credential

## 匯出您的資料庫

這個命令會將匯出資料庫要求提交給服務。視資料庫大小而定，匯出作業可能需要一些時間才能完成。

> [AZURE.IMPORTANT] 若要保證在交易上一致的 BACPAC 檔案，您應該先[建立您的資料庫複本](sql-database-copy-powershell.md)，然後匯出資料庫複本。


    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password
    

## 監視匯出作業的進度

執行 **New-AzureRmSqlDatabaseExport** 之後，您即可檢查要求的狀態。如果您在要求之後立即執行此作業，通常會傳回 **[狀態：擱置]** 或 **[狀態：執行中]**，您可以多次執行這項作業，直到在輸出中看到 **[狀態：已完成]** 為止。

執行此命令時，會提示您輸入密碼。請輸入您的 SQL Server 系統管理員密碼。


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink
    


## 匯出 SQL Database PowerShell 指令碼


    $ServerName = "servername"
    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName  –AdministratorPassword $credential.Password

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink

## 後續步驟

- [匯入 Azure SQL Database](sql-database-import-powershell.md)


## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0720_2016-->