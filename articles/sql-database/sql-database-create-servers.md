---
title: "建立 Azure SQL Database 伺服器 | Microsoft Docs"
description: "如何使用 Azure 入口網站和 PowerShell 建立 Azure SQL Database 伺服器的快速參考。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: e8fe805da2446895b6616926fe1326dc30b9d0d0


---

# <a name="create-azure-sql-database-servers"></a>建立 Azure SQL Database 伺服器

您可以使用 [Azure 入口網站](https://portal.azure.com/)、PowerShell、REST API 或 C# 來建立 Azure SQL Database 伺服器。 

## <a name="create-an-azure-sql-database-server-using-the-azure-portal"></a>使用 Azure 入口網站建立 Azure SQL Database 伺服器

1. 開啟 [Azure 入口網站](https://portal.azure.com/)中的 [SQL 伺服器] 刀鋒視窗。 

    ![SQL Server](./media/sql-database-get-started/new-sql-server.png)

2. 按一下 [新增] 來建立 SQL Server

    ![新增 SQL Server](./media/sql-database-get-started/new-sql-server-add.png)

> [!TIP]
> 如需使用 Azure 入口網站和 SQL Server Management Studio 的開始使用教學課程，請參閱[藉由使用 Azure 入口網站和 SQL Server Management Studio 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started.md)。
>

## <a name="create-an-azure-sql-database-server-using-powershell"></a>使用 PowerShell 建立 Azure SQL Database 伺服器

若要建立 SQL Database 伺服器，請使用 [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver) Cmdlet。 將 *server1* 取代為您伺服器的名稱。 伺服器名稱在所有 Azure SQL Database 伺服器之中必須是唯一的。 如果該伺服器名稱已被使用，您會收到錯誤訊息。 此命令可能需要幾分鐘的時間才能完成。 資源群組必須已經存在於您的訂用帳戶中。

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

> [!TIP]
> 如需範例指令碼，請參閱 [建立 SQL Database PowerShell 指令碼](sql-database-get-started-powershell.md)。
>

## <a name="additional-resources"></a>其他資源
* 如需管理工具的概觀，請參閱[管理工具概觀](sql-database-manage-overview.md)
* 若要了解如何使用 Azure 入口網站執行管理工作，請參閱[使用 Azure 入口網站管理 Azure SQL Database](sql-database-manage-portal.md)
* 若要了解如何使用 PowerShell 執行管理工作，請參閱[使用 PowerShell 管理 Azure SQL Database](sql-database-manage-powershell.md)
* 若要了解如何使用 SQL Server Management Studio 執行其他工作，請參閱 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 如需了解 SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database](sql-database-technical-overview.md)。 
* 如需了解 Azure 資料庫伺服器和資料庫功能的相關資訊，請參閱[功能](sql-database-features.md)。



<!--HONumber=Dec16_HO3-->


