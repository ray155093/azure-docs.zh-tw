---
title: "PowerShell：建立及管理 Azure SQL Database 伺服器 | Microsoft Docs"
description: "有關如何使用 PowerShell 來建立及管理 Azure SQL Database 伺服器的快速參考資料。"
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
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 144774c9106bf5a0e389c99075c822d1c5282692
ms.openlocfilehash: c22243db06c783bc4f5b311aa4d49ce12032ab02


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>使用 PowerShell 來建立及管理 Azure SQL Database 伺服器

您可以使用 [Azure 入口網站](https://portal.azure.com/)、PowerShell、REST API 或 C# 來建立及管理 Azure SQL Database 伺服器。 此主題是關於使用 PowerShell。 對於 Azure 入口網站，請參閱[使用 Azure 入口網站來建立及管理伺服器](sql-database-manage-servers-portal.md)。 

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
> 對於範例指令碼，請參閱[使用 PowerShell 指令碼來建立 SQL 資料庫](sql-database-get-started-powershell.md)。
>

## <a name="next-steps"></a>後續步驟
* 如需管理工具的概觀，請參閱[管理工具概觀](sql-database-manage-overview.md)
* 若要了解如何使用 Azure 入口網站執行管理工作，請參閱[使用 Azure 入口網站管理 Azure SQL Database](sql-database-manage-portal.md)
* 若要了解如何使用 PowerShell 執行管理工作，請參閱[使用 PowerShell 管理 Azure SQL Database](sql-database-manage-powershell.md)
* 若要了解如何使用 SQL Server Management Studio 執行其他工作，請參閱 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 如需了解 SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database](sql-database-technical-overview.md)。 
* 如需 Azure 資料庫伺服器和資料庫功能的相關資訊，請參閱[功能](sql-database-features.md)。



<!--HONumber=Feb17_HO3-->


