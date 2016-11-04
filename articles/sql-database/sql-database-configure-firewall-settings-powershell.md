---
title: 使用 PowerShell 設定 Azure SQL Database 伺服器層級防火牆規則 | Microsoft Docs
description: 了解如何設定存取 Azure SQL Database 之 IP 位址的防火牆。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2016
ms.author: sstein

---
# 使用 PowerShell 設定 Azure SQL Database 伺服器層級防火牆規則
> [!div class="op_single_selector"]
> * [概觀](sql-database-firewall-configure.md)
> * [Azure 入口網站](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

SQL Database 使用防火牆規則以允許連接到您的伺服器和資料庫。您可以在 SQL Database 伺服器中，針對主要資料庫或使用者資料庫定義伺服器層級和資料庫層級的防火牆設定，以選擇性地允許存取資料庫。

> [!IMPORTANT]
> 若要允許應用程式從 Azure 連接到您的資料庫伺服器，必須啟用 Azure 連接。如需關於防火牆規則及啟用來自 Azure 連接的詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。如果您是在 Azure 雲端界限內進行連接，您可能必須開啟一些其他的 TCP 連接埠。如需詳細資訊，請參閱[針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的＜SQL Database V12：內部與外部＞一節。
> 
> 

[!INCLUDE [啟動 PowerShell 工作階段](../../includes/sql-database-powershell.md)]

## 建立伺服器防火牆規則
可使用 Azure PowerShell 建立、更新及刪除伺服器層級防火牆規則。

若要建立新的伺服器層級防火牆規則，請執行 New-AzureRmSqlServerFirewallRule Cmdlet。下列範例會在伺服器 Contoso 上啟用某個範圍的 IP 位址。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

若要修改現有的伺服器層級防火牆規則，請執行 Set-AzureSqlDatabaseServerFirewallRule Cmdlet。下列範例會變更名為 ContosoFirewallRule 的規則可接受的 IP 位址範圍。

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

若要刪除現有的伺服器層級防火牆規則，請執行 Remove-AzureSqlDatabaseServerFirewallRule Cmdlet。下列範例會刪除名為 ContosoFirewallRule 的規則。

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## 使用 PowerShell 管理防火牆規則
您也可以使用 PowerShell 來管理防火牆規則。如需詳細資訊，請參閱下列主題：

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603860.aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603588.aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603789.aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603586.aspx)

## 後續步驟
如需如何使用 Transact-SQL 建立伺服器層級和資料庫層級防火牆規則的相關資訊，請參閱[使用 T-SQL 設定 Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-configure-firewall-settings-tsql.md)。

如需使用其他方法建立伺服器層級防火牆規則的相關資訊，請參閱：

* [使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)
* [使用 REST API 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-rest.md)

如需建立資料庫的教學課程，請參閱[使用 Azure 入口網站在幾分鐘內建立 SQL Database](sql-database-get-started.md)。如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱 [SQL Database 的用戶端快速入門程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。若要了解如何瀏覽至資料庫，請參閱[管理資料庫存取與登入安全性](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## 其他資源
* [保護您的資料庫](sql-database-security.md)
* [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_0810_2016------>