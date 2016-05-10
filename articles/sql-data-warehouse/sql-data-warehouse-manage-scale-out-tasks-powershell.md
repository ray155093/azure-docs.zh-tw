<properties
   pageTitle="Azure SQL 資料倉儲中相應放大計算資源的工作 | Microsoft Azure"
   description="了解如何暫停 (暫止) 或啟動 (繼續) Azure SQL 資料倉儲資料庫的計算資源，以及如何相應放大或縮減服務等級目標 (SLO) 的 DWU 設定。這些工作會使用 Azure PowerShell Cmdlet。"
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
   ms.date="04/21/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL 資料倉儲中相應放大或縮減計算資源的工作

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)


彈性相應放大計算資源和記憶體，以滿足不斷變動的工作負載，並在非尖峰時間相應縮減資源以節省成本。

這個工作集合會使用 PowerShell Cmdlet 來：

- 暫停計算資源
- 繼續計算資源
- 調整 DWU 以變更計算資源

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

## 工作 #1︰暫停計算

[AZURE.INCLUDE [SQL 資料倉儲暫停描述](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，請使用 [Suspend-AzureRmSqlDatabase][] Cmdlet。下例會暫停裝載在 Server01 伺服器上的 Database02 資料庫。此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

> [AZURE.NOTE] 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 PowerShell Cmdlet 中的 -ServerName。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
一種變異，這個範例會將資料庫擷取至 $database 物件。然後將物件輸送到 [Suspend-AzureRmSqlDatabase][]。結果會儲存在物件 resultDatabase 中。最終的命令會顯示結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## 工作 #2︰繼續計算

[AZURE.INCLUDE [SQL 資料倉儲繼續描述](../../includes/sql-data-warehouse-resume-description.md)]

若要啟動資料庫，請使用 [Resume-AzureRmSqlDatabase][] Cmdlet。下例會啟動裝載在 Server01 伺服器上的 Database02 資料庫。此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

一種變異，這個範例會將資料庫擷取至 $database 物件。接著將物件輸送到 [Resume-AzureRmSqlDatabase][]，並將結果儲存在 $resultDatabase 中。最終的命令會顯示結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```


## 工作 #3︰調整 DWU

[AZURE.INCLUDE [SQL 資料倉儲調整 DWU 描述](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWU，請使用 [Set-AzureRmSqlDatabase][] PowerShell Cmdlet。下例會將裝載在 MyServer 伺服器上的資料庫 MySQLDW 的服務等級目標設定為 DW1000。

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

## 後續步驟

其他管理工作請參閱 [管理概觀][]。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[管理概觀]: ./sql-data-warehouse-overview-manage.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0427_2016-->