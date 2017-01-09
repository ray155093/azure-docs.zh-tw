---
title: "使用 REST API 的 Azure SQL Database 伺服器層級防火牆規則 | Microsoft Docs"
description: "了解如何設定存取 Azure SQL Database 之 IP 位址的防火牆。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: cc0faa49daaafe19c71d2c765b8e865be04f81e2


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>使用 REST API 設定 Azure SQL Database 伺服器層級防火牆規則
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

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>透過 REST API 來管理伺服器層級防火牆規則
1. 透過 REST API 管理防火牆規則必須經過驗證。 如需相關資訊，請參閱 [利用 Azure Resource Manager API 進行授權的開發人員指南](../azure-resource-manager/resource-manager-api-authentication.md)。
2. 伺服器層級規則可使用 REST API 建立、更新或刪除
   
    若要建立或更新伺服器層級防火牆規則，請使用下列命令執行 PUT 方法：
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    要求本文
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    若要移除現有的伺服器層級防火牆規則，請使用下列命令執行 DELETE 方法：

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>使用 REST API 管理防火牆規則
* [建立或更新防火牆規則](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [刪除防火牆規則](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [取得防火牆規則](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [列出所有防火牆規則](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>後續步驟
如需如何使用 Transact-SQL 建立伺服器層級和資料庫層級防火牆規則的做法文章，請參閱 [使用 T-SQL 設定 Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-configure-firewall-settings-tsql.md)。 

如需使用其他方法建立伺服器層級防火牆規則的做法文章，請參閱： 

* [使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)
* [使用 PowerShell 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-powershell.md)

如需建立資料庫的教學課程，請參閱 [使用 Azure 入口網站在幾分鐘內建立 SQL Database](sql-database-get-started.md)。
如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱 [SQL Database 的用戶端快速入門程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要了解如何瀏覽至資料庫，請參閱 [管理資料庫存取與登入安全性](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他資源
* [保護您的資料庫](sql-database-security-overview.md)
* [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->





<!--HONumber=Jan17_HO1-->


