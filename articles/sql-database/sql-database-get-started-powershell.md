---
title: "Azure PowerShell︰建立 SQL Database | Microsoft Docs"
description: "了解如何在 Azure 入口網站中，建立 SQL Database 邏輯伺服器、伺服器層級防火牆規則和資料庫。"
keywords: "sql database 教學課程, 建立 sql database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7f75b57c5d409ad9c4c79c48e4b7ee0021e7846b
ms.lasthandoff: 04/12/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>使用 PowerShell 建立單一 Azure SQL Database

PowerShell 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本指南詳述如何使用 PowerShell 在 [Azure SQL Database 邏輯伺服器](sql-database-features.md)的 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)中部署 Azure SQL Database。

若要完成本教學課程，請確定您已安裝最新的 [Azure PowerShell](/powershell/azureps-cmdlets-docs)。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-azure"></a>登入 Azure

使用 [Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) 命令建立 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>建立邏輯伺服器

使用 [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) 命令建立 [Azure SQL Database 邏輯伺服器](sql-database-features.md)。 邏輯伺服器包含一組當作群組管理的資料庫。 下列範例會使用名為 `ServerAdmin` 的系統管理員登入和密碼 `ChangeYourAdminPassword1` 在資源群組中建立隨機命名的伺服器。 視需要取代這些預先定義的值。

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>設定伺服器防火牆規則

使用 [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) 命令建立 [Azure SQL Database 伺服器層級防火牆規則](sql-database-firewall-configure.md)。 伺服器層級防火牆規則可讓外部應用程式 (例如 SQL Server Management Studio 或 SQLCMD 公用程式) 穿過 SQL Database 服務防火牆連線到 SQL Database。 在下列範例中，只會針對其他 Azure 資源開啟防火牆。 若要啟用外部連線，請將 IP 位址變更為適合您環境的地址。 若要開啟所有 IP 位址，請使用 0.0.0.0 作為起始 IP 位址，並使用 255.255.255.255 作為結束位址。

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!NOTE]
> SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 1433，否則將無法連線至 Azure SQL Database 伺服器。
>

## <a name="create-a-blank-database"></a>建立空白資料庫

在伺服器中使用 [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) 命令建立具有 [S0 效能等級](sql-database-service-tiers.md)的空白 SQL Database。 下列範例會建立名為 `mySampleDatabase` 的資料庫。 視需要取代此預先定義的值。

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門會建置在本快速入門。 如果您打算繼續進行後續的快速入門或教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列命令來刪除本快速入門建立的所有資源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

- 若要使用 SQL Server Management Studio 來連線和查詢，請參閱[使用 SSMS 連線及查詢](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio Code 進行連線和查詢，請參閱[使用 Visual Studio Code 進行連線和查詢](sql-database-connect-query-vscode.md)。
- 若要使用 .NET 進行連線和查詢，請參閱[使用 .NET 進行連線和查詢](sql-database-connect-query-dotnet.md)。
- 若要使用 PHP 進行連線和查詢，請參閱[使用 PHP 進行連線和查詢](sql-database-connect-query-php.md)。
- 若要使用 Node.js 進行連線和查詢，請參閱[使用 Node.js 進行連線和查詢](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 進行連線和查詢，請參閱[使用 Java 進行連線和查詢](sql-database-connect-query-java.md)。
- 若要使用 Python 進行連線和查詢，請參閱[使用 Python 進行連線和查詢](sql-database-connect-query-python.md)。
- 若要使用 Ruby 進行連線和查詢，請參閱[使用 Ruby 進行連線和查詢](sql-database-connect-query-ruby.md)。
