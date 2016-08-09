<properties
    pageTitle="使用 PowerShell 新增 SQL Database 設定| Microsoft Azure"
    description="了解如何使用 PowerShell 建立新的 SQL Database。透過 PowerShell cmdlet 可以管理一般資料庫設定工作。"
    keywords="建立新的 sql database,資料庫設定"
	services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/09/2016"
    ms.author="sstein"/>

# 建立新的 SQL Database 並使用 PowerShell Cmdlet 執行一般資料庫設定工作


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



了解如何使用 PowerShell Cmdlet 建立新的 SQL Database。(如需建立彈性資料庫，請參閱[使用 PowerShell 建立新的彈性資料庫集區](sql-database-elastic-pool-create-powershell.md))。


[AZURE.INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]

## 資料庫設定：建立資源群組、伺服器和防火牆規則

您有權在選取的 Azure 訂用帳戶下執行 Cmdlet 後，下一步是建立含有伺服器的資源群組，以在伺服器中建立資料庫。為了使用您選擇的任何有效位置，您可以編輯下一個命令。執行 **(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location** 以取得有效位置的清單。

執行下列命令以建立新的資源群組：

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

成功建立新的資源群組之後，您會看到以下訊息：**ProvisioningState : Succeeded**。


### 建立伺服器

SQL Database 會建立在 Azure SQL Database 伺服器內。執行 **New-AzureRmSqlServer** 以建立新的伺服器。用您的伺服器名稱取代 *ServerName*。此名稱對於所有 Azure SQL Database 伺服器必須是唯一。如果該伺服器名稱已被使用，您將會收到錯誤訊息。另外值得注意的是，此命令可能需要數分鐘才能完成。您可以編輯此命令以使用您選擇的任何有效位置，但您應該使用您在上一個步驟中建立資源群組時使用的相同位置。

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

執行此命令時，系統將會提示您輸入使用者名稱和密碼。請不要輸入您的 Azure 認證。請改為輸入您要為新伺服器建立的系統管理員認證使用者名稱和密碼。

成功建立伺服器後，會出現伺服器詳細資料。

### 設定伺服器防火牆規則以允許存取伺服器

建立可存取伺服器的防火牆規則。執行以下命令，用對您電腦有效的值，取代開頭和結尾 IP 位址。

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

成功建立防火牆規則後，會出現規則詳細資料。

若要允許其他 Azure 服務存取伺服器，則新增防火牆規則並且將 tartIpAddress 和 EndIpAddress 都設為 0.0.0.0。請注意，這可讓來自任何 Azure 訂用帳戶的 Azure 流量存取伺服器。

如需詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。


## 建立新的 SQL Database

現在您已擁有資源群組、伺服器和設定完成的防火牆規則，便可以存取伺服器。

下列命令會在具有 S1 效能層級的標準服務層建立新的 (空白) SQL Database︰


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


成功建立資料庫後，會出現資料庫詳細資料。

## 建立新的 SQL Database PowerShell 指令碼

以下是新的 SQL Database PowerShell 指令碼：

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"

    $ServerName = "uniqueservername"

    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"

    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"

    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp

    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel

    $SqlDatabase



## 後續步驟
建立新的 SQL Database 並執行基本的資料庫設定工作之後，您就可以執行下列作業：

- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)。


## 其他資源

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0803_2016-->