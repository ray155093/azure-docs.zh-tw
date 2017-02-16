---
title: "使用 PowerShell 管理 Azure SQL Database | Microsoft 文件"
description: "使用 PowerShell 管理 Azure SQL Database。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: adad6b8e27e0996559d5e6dacb8dd60fbf52a631
ms.openlocfilehash: 0c1ce1c29e447d9db4ef0df7873ef89cb835abee


---
# <a name="managing-azure-sql-database-using-powershell"></a>使用 PowerShell 管理 Azure SQL Database
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-manage-portal.md)
> * [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

本主題說明用來執行許多 Azure SQL Database 工作的 PowerShell Cmdlet。 如需完整的清單，請參閱 [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)。

> [!TIP]
> 如需示範如何建立伺服器、建立伺服器型防火牆、檢視伺服器屬性、連接和查詢 Master 資料庫、建立範例資料庫和空白資料庫、查詢資料庫屬性、連接和查詢範例資料庫的教學課程，請參閱[開始使用教學課程](sql-database-get-started-powershell.md)。
>

## <a name="how-do-i-create-a-resource-group"></a>如何建立資源群組？
若要為 SQL Database 及相關的 Azure 資源建立資源群組，請使用 [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx) Cmdlet。

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

如需詳細資訊，請參閱 [將 Azure PowerShell 與 Azure 資源管理員搭配使用](../powershell-azure-resource-manager.md)。
如需完整的教學課程，請參閱[使用 Azure PowerShell 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started-powershell.md)。

## <a name="how-do-i-create-a-sql-database-server"></a>如何建立 SQL Database 伺服器？
若要建立 SQL Database 伺服器，請使用 [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx) Cmdlet。 將 *server1* 取代為您伺服器的名稱。 伺服器名稱在所有 Azure SQL Database 伺服器之中必須是唯一的。 如果該伺服器名稱已被使用，您會收到錯誤訊息。 此命令可能需要幾分鐘的時間才能完成。 資源群組必須已經存在於您的訂用帳戶中。

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

如需伺服器的詳細資訊，請參閱 [SQL Database 功能](sql-database-features.md)。 如需完整的教學課程，請參閱[使用 Azure PowerShell 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started-powershell.md)。

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>如何建立 SQL Database 伺服器防火牆規則？
若要建立存取伺服器的防火牆規則，請使用 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) Cmdlet。 執行以下命令，以您用戶端的有效值取代開頭和結尾 IP 位址。 資源群組和伺服器必須已經存在於您的訂用帳戶中。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

若要允許其他 Azure 服務存取您的伺服器，請建立防火牆規則，並將 `-StartIpAddress` 和 `-EndIpAddress` 都設定為 **0.0.0.0**。 這個特殊的防火牆規則會允許所有 Azure 流量存取伺服器。

如需詳細資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)。 如需完整的教學課程，請參閱[使用 Azure PowerShell 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started-powershell.md)。

## <a name="how-do-i-create-a-sql-database"></a>如何建立 SQL Database？
若要建立 SQL Database，請使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) Cmdlet。 資源群組和伺服器必須已經存在於您的訂用帳戶中。 

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

如需詳細資訊，請參閱 [什麼是 SQL Database](sql-database-technical-overview.md)。 如需完整的教學課程，請參閱[使用 Azure PowerShell 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started-powershell.md)。

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>如何變更 SQL Database 的效能層級？
若要變更效能層級，以相應增加或減少方式調整您的資料庫，請使用 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) Cmdlet。 資源群組、伺服器及資料庫必須已經存在於您的訂用帳戶中。 針對「基本層」，將 `-RequestedServiceObjectiveName` 設定為單一空間 (如以下程式碼片段)。 像先前其他層的範例一樣，將它設定為 *S0*、*S1*、*P1*、*P6* 等。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

如需詳細資訊，請參閱 [SQL Database 選項和效能：了解每個服務層中可用的項目](sql-database-service-tiers.md)。 如需範例指令碼，請參閱 [變更您的 SQL Database 之服務層級和效能等級的範例 PowerShell 指令碼](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database)。

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>如何將 SQL Database 複製到相同伺服器？
若要將 SQL Database 複製到相同的伺服器，請使用 [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx) Cmdlet。 將 `-CopyServerName` 和 `-CopyResourceGroupName` 設定為與來源資料庫伺服器及資源群組相同的值。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

如需詳細資訊，請參閱 [複製 Azure SQL Database](sql-database-copy.md)。 如需範例指令碼，請參閱 [複製 SQL Database PowerShell 指令碼](sql-database-copy-powershell.md#example-powershell-script)。

## <a name="how-do-i-delete-a-sql-database"></a>如何刪除 SQL Database？
若要刪除 SQL Database， 請使用 [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx) Cmdlet。 資源群組、伺服器及資料庫必須已經存在於您的訂用帳戶中。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>如何刪除 SQL Database 伺服器？
若要刪除 SQL Database 伺服器，請使用 [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx) Cmdlet。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>如何使用 PowerShell 來建立及管理彈性集區？
如需有關使用 PowerShell 來建立彈性集區的詳細資料，請參閱[使用 PowerShell 建立新的彈性集區](sql-database-elastic-pool-create-powershell.md)。

如需有關使用 PowerShell 來管理彈性集區的詳細資料，請參閱[使用 PowerShell 監視和管理彈性集區](sql-database-elastic-pool-manage-powershell.md)。

## <a name="related-information"></a>相關資訊
* [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Dec16_HO3-->


