---
title: "建立 Azure SQL Database | Microsoft Docs"
description: "如何使用 Azure 入口網站、PowerShell、Transact-SQL 建立 Azure SQL Database 的快速參考。"
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
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: aca33871d83a34769fd2b09be4b382c872a65f0a


---
# <a name="create-an-azure-sql-database"></a>建立 Azure SQL 資料庫

您可以使用 [Azure 入口網站](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API 或 C# 來建立 Azure SQL 資料庫。 

## <a name="create-an-azure-sql-database-using-the-azure-portal"></a>使用 Azure 入口網站建立 Azure SQL Database

1. 開啟 [Azure 入口網站](https://portal.azure.com/)中的 [SQL Database] 刀鋒視窗。 

    ![SQL Database](./media/sql-database-get-started/sql-databases.png)
2. 在 SQL Database 刀鋒視窗中，按一下 [新增]。

    ![新增 SQL Database](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> 如需使用 Azure 入口網站和 SQL Server Management Studio 的開始使用教學課程，請參閱[藉由使用 Azure 入口網站和 SQL Server Management Studio 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started.md)。
>

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

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中使用 Transact-SQL 建立 Azure SQL Database

若要在 SQL Server Management Studio 中使用 Transact-SQL 建立 SQL Database：

1. 使用 SQL Server Management Studio，以伺服器層級主體登入或 **dbmanager** 角色成員的登入連接到 Azure 資料庫伺服器。 如需登入的詳細資訊，請參閱[管理登入](sql-database-manage-logins.md)。
2. 在 [物件總管] 中，開啟 Databases 資料夾、展開 [System Databases] 資料夾、在 [master] 上按一下滑鼠右鍵，然後按一下 [新增查詢]。
3. 使用 **CREATE DATABASE** 陳述式來建立資料庫。 如需詳細資訊，請參閱 [CREATE DATABASE (SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)。 下列陳述式會建立名為 **myTestDB** 的資料庫，並指定它是預設大小上限為 250 GB 的「標準 S0 版本」資料庫。
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. 按一下 [執行]  來執行查詢。
5. 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫] 節點，然後按一下 [重新整理]，以在 [物件總管] 中檢視新的資料庫。 

> [!TIP]
> 如需使用 Azure 入口網站和 SQL Server Management Studio 的開始使用教學課程，請參閱[藉由使用 Azure 入口網站和 SQL Server Management Studio 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started.md)。
>

## <a name="additional-resources"></a>其他資源
* 如需管理工具的概觀，請參閱[管理工具概觀](sql-database-manage-overview.md)。
* 若要了解如何使用 Azure 入口網站執行管理工作，請參閱[使用 Azure 入口網站管理 Azure SQL Database](sql-database-manage-portal.md)。
* 若要了解如何使用 PowerShell 執行管理工作，請參閱[使用 PowerShell 管理 Azure SQL Database](sql-database-manage-powershell.md)。
* 若要了解如何使用 SQL Server Management Studio 執行管理工作，請參閱 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 如需了解 SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database](sql-database-technical-overview.md)。 
* 如需了解 Azure 資料庫伺服器和資料庫功能的相關資訊，請參閱[功能](sql-database-features.md)。



<!--HONumber=Dec16_HO3-->


