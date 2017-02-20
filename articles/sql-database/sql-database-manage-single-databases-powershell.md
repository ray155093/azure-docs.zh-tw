---
title: "PowerShell：建立及管理單一 Azure SQL 資料庫 | Microsoft Docs"
description: "有關如何使用 Azure 入口網站來建立及管理單一 Azure SQL 資料庫的快速參考資料"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>使用 PowerShell 來建立及管理單一 Azure SQL 資料庫

您可以使用 [Azure 入口網站](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API 或 C# 來建立及管理單一 Azure SQL 資料庫。 此主題是關於使用 PowerShell。 對於 Azure 入口網站，請參閱[使用 Azure 入口網站來建立及管理單一資料庫](sql-database-manage-single-databases-powershell.md)。 對於 Transact-SQL，請參閱[使用 Transact-SQL 來建立及管理單一資料庫](sql-database-manage-single-databases-tsql.md)。 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-azure-sql-database-using-powershell"></a>使用 PowerShell 建立 Azure SQL Database

若要建立 SQL Database，請使用 [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase) Cmdlet。 資源群組和伺服器必須已經存在於您的訂用帳戶中。 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

> [!TIP]
> 如需範例指令碼，請參閱 [建立 SQL Database PowerShell 指令碼](sql-database-get-started-powershell.md)。
>

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>變更單一資料庫的服務層與效能等級

執行 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) Cmdlet 並將 **-RequestedServiceObjectiveName** 設定為想要之定價層的效能等級；例如 *S0*、*S1*、*S2*、*S3*、*P1*、*P2*...

以您的值取代 ```{variables}``` (請勿包含大括號)。

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>後續步驟
* 如需管理工具的概觀，請參閱[管理工具概觀](sql-database-manage-overview.md)。
* 若要了解如何使用 Azure 入口網站執行管理工作，請參閱[使用 Azure 入口網站管理 Azure SQL Database](sql-database-manage-portal.md)。
* 若要了解如何使用 PowerShell 執行管理工作，請參閱[使用 PowerShell 管理 Azure SQL Database](sql-database-manage-powershell.md)。
* 若要了解如何使用 SQL Server Management Studio 執行管理工作，請參閱 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 如需了解 SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database](sql-database-technical-overview.md)。 
* 如需 Azure 資料庫伺服器和資料庫功能的相關資訊，請參閱[功能](sql-database-features.md)。



<!--HONumber=Feb17_HO2-->


