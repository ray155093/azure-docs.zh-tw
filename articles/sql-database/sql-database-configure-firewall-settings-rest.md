<properties
	pageTitle="使用 REST API 的 Azure SQL Database 伺服器層級防火牆規則 | Microsoft Azure"
	description="了解如何設定存取 Azure SQL Database 之 IP 位址的防火牆。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="06/15/2016"
	ms.author="sstein"/>


#  使用 REST API 設定 Azure SQL Database 伺服器層級防火牆規則


> [AZURE.SELECTOR]
- [概觀](sql-database-firewall-configure.md)
- [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database 使用防火牆規則以允許連接到您的伺服器和資料庫。您可以在 Azure SQL Database 伺服器中，針對主要或使用者資料庫定義伺服器層級和資料庫層級的防火牆設定，以選擇性地允許存取資料庫。

> [AZURE.IMPORTANT] 若要允許應用程式從 Azure 連接到您的資料庫伺服器，必須啟用 Azure 連接。如需關於防火牆規則及啟用來自 Azure 連接的詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。如果您是在 Azure 雲端界限內建立連接，您可能必須開啟一些其他 TCP 連接埠。如需詳細資訊，請參閱[針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的＜**SQL Database V12：內部與外部**＞一節


## 透過 REST API 來管理伺服器層級防火牆規則
1. 透過 REST API 管理防火牆規則必須經過驗證。如需詳細資訊，請參閱「驗證服務管理要求」。
2. 伺服器層級規則可使用 REST API 建立、更新或刪除

	若要建立或更新伺服器層級防火牆規則，請使用下列命令執行 POST 方法：
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	要求本文

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	若要移除現有的伺服器層級防火牆規則，請使用下列命令執行 DELETE 方法：
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule


## 使用服務管理 REST API 管理防火牆規則

* [建立防火牆規則](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [刪除防火牆規則](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [取得防火牆規則](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [列出防火牆規則](https://msdn.microsoft.com/library/azure/dn505715.aspx)
 
## 後續步驟

如需如何使用 Transact-SQL 建立伺服器層級和資料庫層級防火牆規則的做法文章，請參閱[使用 T-SQL 設定 Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-configure-firewall-settings-tsql.md)。

如需使用其他方法建立伺服器層級防火牆規則的做法文章，請參閱：

- [使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)
- [使用 PowerShell 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-powershell.md)

如需建立資料庫的教學課程，請參閱[使用 Azure 入口網站在幾分鐘內建立 SQL Database](sql-database-get-started.md)。如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱 [SQL Database 的用戶端快速入門程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。若要了解如何瀏覽至資料庫，請參閱[管理資料庫存取與登入安全性](https://msdn.microsoft.com/library/azure/ee336235.aspx)。


## 其他資源

- [保護您的資料庫](sql-database-security.md)
- [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0622_2016-->