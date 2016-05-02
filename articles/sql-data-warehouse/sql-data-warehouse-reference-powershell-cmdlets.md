<properties
   pageTitle="Azure SQL 資料倉儲的 PowerShell Cmdlet"
   description="尋找 Azure SQL 資料倉儲的前幾個 PowerShell Cmdlet，包括如何暫停和繼續資料庫。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/02/2016"
   ms.author="sonyama;barbkess;mausher"/>

# 適用於 SQL 資料倉儲的 PowerShell Cmdlet 和 REST API

您可使用 Azure PowerShell Cmdlet 或 REST API 管理 SQL 資料倉儲。

針對 **Azure SQL Database** 定義的大部分命令也可用於 **SQL 資料倉儲**。如需最新清單，請參閱 [Azure SQL Cmdlet](https://msdn.microsoft.com/library/mt574084.aspx)。[Suspend-AzureRmSqlDatabase][] 和 [Resume-AzureRmSqlDatabase][] 這兩個 Cmdlet 是針對 SQL 資料倉儲設計的新增命令。

同樣地，**SQL Azure Database** 的 REST API 也可用於 **SQL 資料倉儲**執行個體。如需最新清單，請參閱 [Azure SQL Databases 的作業](https://msdn.microsoft.com/library/azure/dn505719.aspx)。

## 開始使用 Azure PowerShell Cmdlet

1. 如要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://aka.ms/webpi-azps)。如需此安裝程式的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][]。
2. 若要啟動 PowerShell，請按一下 [啟動]，然後輸入 **Windows PowerShell**。
3. 在 PowerShell 提示中，執行下列命令來登入 Azure Resource Manager，並選取您的訂用帳戶。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```


## 常用的 PowerShell Cmdlet

這些 PowerShell Cmdlet 經常與 Azure SQL 資料倉儲搭配使用：


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


## SQL 資料倉儲範例

這些範例是針對只適用於 SQL 資料倉儲的功能。

### [Suspend-AzureRmSqlDatabase][]

暫停 "Server01" 伺服器上託管的 "Database02" 資料庫。 此伺服器位於「ResourceGroup1」這個 Azure 資源群組。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
一種變化，此範例會將擷取的物件輸送到 [Suspend-AzureRmSqlDatabase][]。結果就是暫停資料庫。最終的命令會顯示結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

### [Resume-AzureRmSqlDatabase][]

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


## 後續步驟
如需更多的參考資訊，請參閱 [SQL 資料倉儲參考概觀][]。如需更多 PowerShell 範例，請參閱：
- [使用 PowerShell 建立 SQL 資料倉儲](sql-data-warehouse-get-started-provision-powershell.md)
- [從快照集還原](sql-data-warehouse-backup-and-restore-from-snapshot.md)
- [從快照集異地還原](sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md)

<!--Image references-->

<!--Article references-->
[SQL 資料倉儲參考概觀]: sql-data-warehouse-overview-reference.md
[如何安裝和設定 Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureRmSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619337.aspx



<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->