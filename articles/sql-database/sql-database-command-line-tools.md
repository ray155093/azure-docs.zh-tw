<properties
	pageTitle="使用 PowerShell 管理 Azure SQL Database | Microsoft Azure"
	description="使用 PowerShell 管理 Azure SQL Database。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="sstein"/>

# 使用 PowerShell 管理 Azure SQL Database


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

本主題提供 PowerShell 命令來執行許多 Azure SQL Database 工作。

[AZURE.INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]


## 建立資源群組

建立會包含伺服器的資源群組。您可以編輯下一個命令，以使用任何有效位置。

如需有效的 SQL Database 伺服器位置清單，請執行下列 Cmdlet：

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

如果您已經有資源群組，您可以跳到下一個區段 (「建立伺服器」)。您也可以編輯或執行以下命令來建立新的資源群組：

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## 建立伺服器

若要建立新的 12 版伺服器，請使用 [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) Cmdlet。將 *server12* 取代為您伺服器的名稱。如果該伺服器名稱已被使用，您將會收到錯誤訊息。另外值得注意的是，此命令可能需要數分鐘才能完成。成功建立伺服器後，會出現伺服器詳細資料和 PowerShell 提示字元。您可以編輯命令以使用任何有效位置。

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

執行此命令時，系統將會提示您輸入使用者名稱和密碼。請不要在此輸入您的 Azure 認證。請改為輸入您要為新伺服器建立的系統管理員認證使用者名稱和密碼。

## 建立伺服器防火牆規則

若要建立存取伺服器的防火牆規則，請使用 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) 命令。執行以下命令，以您用戶端的有效值取代開頭和結尾 IP 位址。

如果您的伺服器需要允許對其他 Azure 服務的存取，請新增 **-AllowAllAzureIPs** 切換。這會新增特殊的防火牆規則，並允許所有 Azure 流量存取伺服器。

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

如需詳細資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)。

## 建立 SQL 資料庫

若要建立資料庫，請使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) 命令。您需要伺服器才能建立資料庫。下列範例會建立名為 TestDB12 的 SQL 資料庫。資料庫會建立為標準 S1 資料庫。

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## 變更 SQL 資料庫的效能層級

您可以使用 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) 命令向上或向下調整您的資料庫。下列範例會將名為 TestDB12 的 SQL 資料庫，從其目前的效能層級擴充至標準 S3 層級。

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## 刪除 SQL 資料庫

您可使用 [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) 命令刪除 SQL 資料庫。下列範例會刪除名為 TestDB12 的 SQL 資料庫。

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## 刪除伺服器

您也可以使用 [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) 命令刪除伺服器。下列範例會刪除名為 server12 的伺服器。


>[AZURE.NOTE]  刪除作業為非同步，並可能需要花費一些時間。此在執行需要完全刪除伺服器的任何其他作業 (例如建立具有相同名稱的新伺服器) 之前，確認刪除作業已完成。


	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"




## 後續步驟

結合命令和自動化。例如，以您的值取代引號中的任何內容 (包括 "<" 和 ">" 字元)，以建立伺服器、防火牆規則和資料庫：


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## 相關資訊

- [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0803_2016-->