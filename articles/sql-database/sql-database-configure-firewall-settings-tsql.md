---
title: "使用 T-SQL 的 Azure SQL Database 伺服器層級和資料庫層級防火牆規則 | Microsoft Docs"
description: "了解如何設定存取 Azure SQL Database 之 IP 位址的防火牆。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: a49bc9cd8fb50dcc753fbb04bf7f2d96cf79f3ab


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>使用 T-SQL 設定 Azure SQL Database 伺服器層級和資料庫層級防火牆規則
> [!div class="op_single_selector"]
> * [概觀](sql-database-firewall-configure.md)
> * [Azure 入口網站](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Microsoft Azure SQL Database 使用防火牆規則以允許連接到您的伺服器和資料庫。 您可以在 Azure SQL Database 伺服器中，針對主要或使用者資料庫定義伺服器層級和資料庫層級的防火牆設定，以選擇性地允許存取資料庫。

> [!IMPORTANT]
> 若要允許應用程式從 Azure 連接到您的資料庫伺服器，必須啟用 Azure 連接。 如需關於防火牆規則及啟用來自 Azure 連接的詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。 如果您是在 Azure 雲端界限內進行連接，您可能必須開啟一些其他的 TCP 連接埠。 如需詳細資訊，請參閱 **針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠** 的＜ [SQL Database V12：內部與外部](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="server-level-firewall-rules"></a>伺服器層級防火牆規則
只有伺服器層級主體登入或 Azure Active Directory 系統管理員可以使用 TRANSACT-SQL 建立伺服器層級的防火牆規則。

1. 啟動查詢視窗，並使用 SQL Server Management Studio 連接到虛擬 master 資料庫。
2. 可從查詢視窗中選取、建立、更新或刪除伺服器層級防火牆規則。
3. 若要建立或更新伺服器層級防火牆規則，請執行 `sp_set_firewall_rule`預存程序。 下列範例會在伺服器 Contoso 上啟用某個範圍的 IP 位址。<br/>從查看哪些規則已存在開始。
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    接著，加入防火牆規則。
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    若要刪除伺服器層級防火牆規則，請執行 sp_delete_firewall_rule 預存程序。 下列範例會刪除名為 ContosoFirewallRule 的規則。
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   如需這些預存程序的詳細資訊，請參閱 [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) 和 [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)。

## <a name="database-level-firewall-rules"></a>資料庫層級防火牆規則
只有具備 **控制** 權限的資料庫使用者 (例如資料庫擁有者) 可以建立資料庫層級的防火牆規則。

1. 建立您的 IP 位址的伺服器層級防火牆之後，請透過傳統入口網站或 SQL Server Management Studio 啟動查詢視窗。
2. 連接到要建立資料庫層級防火牆規則的資料庫。
   
    若要建立新的或更新現有的資料庫層級防火牆規則，請執行 `sp_set_database_firewall_rule` 預存程序。 下列範例會建立名為 ContosoFirewallRule 的新防火牆規則。
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    若要刪除現有的資料庫層級防火牆規則，請執行 `sp_delete_database_firewall_rule` 預存程序。 下列範例會刪除名為 ContosoFirewallRule 的規則。
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

如需這些預存程序的詳細資訊，請參閱 [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) 和 [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx)。

## <a name="next-steps"></a>後續步驟
如需使用其他方法建立伺服器層級防火牆規則的做法文章，請參閱： 

* [使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)
* [使用 PowerShell 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-powershell.md)
* [使用 REST API 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-rest.md)

如需建立資料庫的教學課程，請參閱 [使用 Azure 入口網站在幾分鐘內建立 SQL Database](sql-database-get-started.md)。
如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱 [SQL Database 的用戶端快速入門程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要了解如何瀏覽至資料庫，請參閱 [管理資料庫存取與登入安全性](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他資源
* [保護您的資料庫](sql-database-security-overview.md)
* [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)




<!--HONumber=Dec16_HO4-->


