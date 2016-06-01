<properties
   pageTitle="管理 Azure SQL 資料倉儲中的計算能力 (PowerShell) | Microsoft Azure"
   description="管理計算能力的 PowerShell 工作。透過調整 DWU 以調整計算資源。或者，暫停和繼續計算資源以節省成本。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# 管理 Azure SQL 資料倉儲中的計算能力 (PowerShell)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-manage-compute.md)
- [入口網站](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


透過相應放大計算資源和記憶體來調整效能，以滿足工作負載不斷改變的需求。透過在非尖峰時間縮減資源或直接暫停計算以節省成本。

這個工作集合會使用 Azure 入口網站來：

- 調整計算
- 暫停計算
- 繼續計算

若要深入了解，請參閱[管理計算能力概觀][]。


## 開始之前

### 安裝最新版的 Azure PowerShell

> [AZURE.NOTE]  若要搭配使用 Azure Powershell 與 SQL 資料倉儲，需要有 Azure PowerShell 1.0.3 版或更高版本。若要確認目前的版本，請執行命令 **Get-Module -ListAvailable -Name Azure**。您可以從 [Microsoft Web Platform Installer][] 安裝最新的版本。如需詳細資訊，請參閱＜如何安裝及設定 Azure PowerShell＞。

### 開始使用 Azure PowerShell Cmdlet

開始進行之前：

1. 開啟 Azure PowerShell。 
2. 在 PowerShell 提示中，執行下列命令來登入 Azure Resource Manager，並選取您的訂用帳戶。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## 調整計算能力

[AZURE.INCLUDE [SQL 資料倉儲調整 DWU 描述](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWU，請使用 [Set-AzureRmSqlDatabase][] PowerShell Cmdlet。下例會將裝載在 MyServer 伺服器上的資料庫 MySQLDW 的服務等級目標設定為 DW1000。

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## 暫停計算

[AZURE.INCLUDE [SQL 資料倉儲暫停描述](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，請使用 [Suspend-AzureRmSqlDatabase][] Cmdlet。下例會暫停裝載在 Server01 伺服器上的 Database02 資料庫。此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

> [AZURE.NOTE] 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 PowerShell Cmdlet 中的 -ServerName。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
一種變異，這個範例會將資料庫擷取至 $database 物件。然後將物件輸送到 [Suspend-AzureRmSqlDatabase][]。結果會儲存在物件 resultDatabase 中。最終的命令會顯示結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## 繼續計算

[AZURE.INCLUDE [SQL 資料倉儲繼續描述](../../includes/sql-data-warehouse-resume-description.md)]

若要啟動資料庫，請使用 [Resume-AzureRmSqlDatabase][] Cmdlet。下例會啟動裝載在 Server01 伺服器上的 Database02 資料庫。此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

一種變異，這個範例會將資料庫擷取至 $database 物件。接著將物件輸送到 [Resume-AzureRmSqlDatabase][]，並將結果儲存在 $resultDatabase 中。最終的命令會顯示結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## 後續步驟

如需其他管理工作的詳細資訊，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[Performance scalability overview]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->