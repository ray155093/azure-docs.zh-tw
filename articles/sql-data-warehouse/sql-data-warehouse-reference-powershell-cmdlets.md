<properties
   pageTitle="Azure SQL 資料倉儲的 PowerShell Cmdlet"
   description="尋找 Azure SQL 資料倉儲的前幾個 PowerShell Cmdlet，包括如何暫停和繼續資料庫。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="sonyama;barbkess;mausher"/>

# 適用於 SQL 資料倉儲的 PowerShell Cmdlet 和 REST API

您可使用 Azure PowerShell Cmdlet 或 REST API 管理許多 SQL 資料倉儲系統管理工作。下列為一些在 SQL 資料倉儲中使用 PowerShell 命令來自動化一般工作的範例。如需一些良好的 REST 範例，請參閱[使用 REST 管理延展性][]一文。

> [AZURE.NOTE]  若要搭配使用 Azure Powershell 與 SQL 資料倉儲，需要有 Azure PowerShell 1.0.3 版或更高版本。您可以執行 **Get-Module -ListAvailable -Name Azure** 來檢查您的版本。可透過 [Microsoft Web Platform Installer][] 安裝最新的版本。如需安裝最新版本的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][]。

## 開始使用 Azure PowerShell Cmdlet

1. 開啟 Windows PowerShell。
2. 在 PowerShell 提示中，執行下列命令來登入 Azure Resource Manager，並選取您的訂用帳戶。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## 暫停 SQL 資料倉儲範例

暫停 "Server01" 伺服器上託管的 "Database02" 資料庫。 此伺服器位於「ResourceGroup1」這個 Azure 資源群組。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
在以下另一種變化的範例中，會將擷取的物件輸送到 [Suspend-AzureRmSqlDatabase][]。結果就是暫停資料庫。最終的命令會顯示結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## 啟動 SQL 資料倉儲範例

繼續 "Server01" 伺服器上託管之 "Database02" 資料庫的作業。 此伺服器包含在「ResourceGroup1」這個資源群組中。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

一種變化，此範例會從 "ResourceGroup1" 資源群組包含的 "Server01" 伺服器中，擷取 "Database02" 資料庫。 它將擷取的物件輸送到 [Resume-AzureRmSqlDatabase][]。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 PowerShell Cmdlet 中的 -ServerName。

## 經常使用的 PowerShell Cmdlet

這些 PowerShell Cmdlet 經常與 Azure SQL 資料倉儲搭配使用。

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [New-AzureRmSqlDatabase][]
- [Remove-AzureRmSqlDatabase][]
- [Restore-AzureRmSqlDatabase][]
- [Resume-AzureRmSqlDatabase][]
- [Select-AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspend-AzureRmSqlDatabase][]

## 後續步驟
如需更多 PowerShell 範例，請參閱：

- [使用 PowerShell 建立 SQL 資料倉儲][]
- [資料庫還原][]

如需可以使用 PowerShell 來自動化的完整工作清單，請參閱 [Azure SQL Database Cmdlet][]。如需可以使用 REST 來自動化的工作清單，請參閱 [Azure SQL Database 的作業][]。

<!--Image references-->

<!--Article references-->
[如何安裝和設定 Azure PowerShell]: ./powershell-install-configure.md
[使用 PowerShell 建立 SQL 資料倉儲]: ./sql-data-warehouse-get-started-provision-powershell.md
[資料庫還原]: ./sql-data-warehouse-manage-database-restore-powershell.md
[使用 REST 管理延展性]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlet]: https://msdn.microsoft.com/library/mt574084.aspx
[Azure SQL Database 的作業]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0810_2016-->