---
title: "設定 SQL Database 伺服器層級防火牆規則 | Microsoft Docs"
description: "了解如何設定存取 Azure SQL Server 之 IP 位址的防火牆。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: ad72adf626fb5f59f239f57ff0158db101017a1b
ms.openlocfilehash: 7925663bd4233e368f54b38c94888ac1b0f5dcab


---
# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則
> [!div class="op_single_selector"]
> * [概觀](sql-database-firewall-configure.md)
> * [Azure 入口網站](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Azure SQL Server 使用防火牆規則以允許連接到您的伺服器和資料庫。 您可以在 Azure SQL Server 邏輯伺服器中，針對主要或使用者資料庫定義伺服器層級和資料庫層級的防火牆設定，以選擇性地允許存取資料庫。 本主題討論伺服器層級的防火牆規則。

> [!IMPORTANT]
> 若要允許應用程式從 Azure 連接到您的 Azure SQL Server，必須啟用 Azure 連接。 若要了解防火牆規則的運作方式，請參閱[如何設定 Azure SQL Server 防火牆\-概觀](sql-database-firewall-configure.md)。 如果您是在 Azure 雲端界限內進行連接，您可能必須開啟一些其他的 TCP 連接埠。 如需詳細資訊，請參閱 **針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠** 的＜ [SQL Database V12：內部與外部](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

**建議：** 當您有多個資料庫具有相同存取需求，且不想花時間個別設定每個資料庫時，請對系統管理員使用伺服器層級的防火牆規則。 Microsoft 建議在可行時使用資料庫層級防火牆規則來增強安全性，並且讓您的資料庫更具有可攜性。

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>透過 Azure 入口網站管理現有的伺服器層級防火牆規則
重複管理伺服器層級防火牆規則的步驟。

* 若要新增目前的電腦，按一下 [新增用戶端 IP]。
* 若要新增其他 IP 位址，請輸入「規則名稱」、「起始 IP 位址」和「結束 IP 位址」。
* 若要修改現有的規則，請按一下和修改規則中的任何欄位。
* 若要刪除現有的規則，將滑鼠停留在規則上方，直到資料列結尾出現 X。 按一下 X 移除規則。

按一下 [儲存]  儲存變更。

## <a name="next-steps"></a>後續步驟
如需如何使用 Transact-SQL 建立伺服器層級和資料庫層級防火牆規則的做法文章，請參閱 [使用 T-SQL 設定 Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-configure-firewall-settings-tsql.md)。 

如需使用其他方法建立伺服器層級防火牆規則的做法文章，請參閱： 

* [使用 PowerShell 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-powershell.md)
* [使用 REST API 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-rest.md)

如需建立資料庫的教學課程，請參閱 [使用 Azure 入口網站在幾分鐘內建立 SQL Database](sql-database-get-started.md)。   

若要了解如何建立其他可連線至資料庫的使用者，請參閱 [SQL Database 驗證和授權︰授與存取](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他資源
* [保護您的資料庫](sql-database-security.md)   
* [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Nov16_HO5-->


