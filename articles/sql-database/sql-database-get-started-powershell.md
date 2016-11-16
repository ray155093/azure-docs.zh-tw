---
title: "使用 PowerShell 新增 SQL Database 設定 | Microsoft Docs"
description: "了解如何使用 PowerShell 來建立 SQL Database。 透過 PowerShell cmdlet 可以管理一般資料庫設定工作。"
keywords: "建立新的 sql database,資料庫設定"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87e52fe29f659577d7dc0c9661ebde2c1c475cfc


---
# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>建立 SQL Database 並使用 PowerShell Cmdlet 執行一般資料庫設定工作
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-get-started.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> * [C#](sql-database-get-started-csharp.md)
> 
> 

了解如何使用 PowerShell Cmdlet 建立 SQL Database。 (如需建立彈性資料庫，請參閱 [使用 PowerShell 建立新的彈性資料庫集區](sql-database-elastic-pool-create-powershell.md))。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>資料庫設定：建立資源群組、伺服器和防火牆規則
您有權在選取的 Azure 訂用帳戶下執行 Cmdlet 後，下一步是建立含有伺服器的資源群組，以在伺服器中建立資料庫。 為了使用您選擇的任何有效位置，您可以編輯下一個命令。 執行 **(Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" }).Location** 以取得有效位置的清單。

執行下列命令以建立資源群組：

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>建立伺服器
SQL Database 會建立在 Azure SQL Database 伺服器內。 執行 [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx) 以建立伺服器。 對於所有 Azure SQL Database 伺服器，您的伺服器名稱必須是唯一的。 如果該伺服器名稱已被使用，您會收到錯誤訊息。 另外值得注意的是，此命令可能需要數分鐘才能完成。 您可以編輯此命令以使用您選擇的任何有效位置，但您應該使用您在上一個步驟中建立資源群組時使用的相同位置。

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

執行此命令時，系統將會提示您輸入使用者名稱和密碼。 請不要輸入您的 Azure 認證。 改為輸入要建立為伺服器系統管理員的使用者名稱和密碼。 在本文最後的指令碼會示範如何在程式碼中設定伺服器認證。

成功建立伺服器後，會出現伺服器詳細資料。

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>設定伺服器防火牆規則以允許存取伺服器
若要存取伺服器，您必須建立防火牆規則。 執行 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) 命令，用對您電腦有效的值，取代開頭和結尾 IP 位址。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

成功建立防火牆規則後，會出現規則詳細資料。

若要允許其他 Azure 服務存取伺服器，則新增防火牆規則並且將 tartIpAddress 和 EndIpAddress 都設為 0.0.0.0。 此規則可讓來自任何 Azure 訂用帳戶的 Azure 流量存取伺服器。

如需詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。

## <a name="create-a-sql-database"></a>建立 SQL 資料庫
現在您已擁有資源群組、伺服器和設定完成的防火牆規則，便可以存取伺服器。

下列 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) 命令會在具有 S1 效能層級的標準服務層建立 (空白) SQL Database︰

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


成功建立資料庫後，會出現資料庫詳細資料。

## <a name="create-a-sql-database-powershell-script"></a>建立 SQL Database PowerShell 指令碼
下列 PowerShell 指令碼會建立 SQL Database 及其所有相依資源。 以您的訂用帳戶與資源特有的值取代所有的 `{variables}` (在設定您的值時移除 **{}** )。

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation

    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"

    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds

    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip

    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp


    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"

    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo


    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>後續步驟
建立 SQL Database 並執行基本的資料庫設定工作之後，您就可以執行下列作業：

* [使用 PowerShell 管理 SQL Database](sql-database-manage-powershell.md)
* [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>其他資源
* [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Nov16_HO2-->


