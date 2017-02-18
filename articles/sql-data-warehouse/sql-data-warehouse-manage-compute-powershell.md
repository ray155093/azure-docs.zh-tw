---
title: "管理 Azure SQL 資料倉儲中的計算能力 (PowerShell) | Microsoft Docs"
description: "管理計算能力的 PowerShell 工作。 透過調整 DWU 以調整計算資源。 或者，暫停和繼續計算資源以節省成本。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: f9814fc0011153a25489d60d696bb567edfcebc4
ms.openlocfilehash: a4cde433850ab3627f870b51f5a897c6f846c2f0


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>管理 Azure SQL 資料倉儲中的計算能力 (PowerShell)
> [!div class="op_single_selector"]
> * [概觀](sql-data-warehouse-manage-compute-overview.md)
> * [入口網站](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

## <a name="before-you-begin"></a>開始之前
### <a name="install-the-latest-version-of-azure-powershell"></a>安裝最新版的 Azure PowerShell
> [!NOTE]
> 若要搭配使用 Azure Powershell 與 SQL 資料倉儲，需要有 Azure PowerShell 1.0.3 版或更高版本。  若要確認目前的版本，請執行命令 **Get-Module -ListAvailable -Name Azure**。 您可以從 [Microsoft Web Platform Installer][Microsoft Web Platform Installer] 安裝最新的版本。  如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][How to install and configure Azure PowerShell]。
> 
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>開始使用 Azure PowerShell Cmdlet
開始進行之前：

1. 開啟 Azure PowerShell。
2. 在 PowerShell 提示中，執行下列命令來登入 Azure Resource Manager，並選取您的訂用帳戶。
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>調整計算能力
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWU，請使用 [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase] PowerShell Cmdlet。 下例會將裝載在 MyServer 伺服器上的資料庫 MySQLDW 的服務等級目標設定為 DW1000。

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暫停計算
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，請使用 [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase] Cmdlet。 下例會暫停裝載在 Server01 伺服器上的 Database02 資料庫。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

> [!NOTE]
> 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 PowerShell Cmdlet 中的 -ServerName。
> 
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
一種變異，這個範例會將資料庫擷取至 $database 物件。 然後將物件輸送到 [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]。 結果會儲存在物件 resultDatabase 中。 最終的命令會顯示結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>繼續計算
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

若要啟動資料庫，請使用 [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase] Cmdlet。 下例會啟動裝載在 Server01 伺服器上的 Database02 資料庫。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

一種變異，這個範例會將資料庫擷取至 $database 物件。 接著將物件輸送到 [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]，並將結果儲存在 $resultDatabase 中。 最終的命令會顯示結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟
如需其他管理工作的詳細資訊，請參閱[管理概觀][Management overview]。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/



<!--HONumber=Jan17_HO3-->


