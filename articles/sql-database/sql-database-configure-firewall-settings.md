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
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: 1479595fb7de2e0a37520c7d323624142e624af1


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>使用 Azure 入口網站建立和管理 Azure SQL Database 伺服器層級防火牆規則
> [!div class="op_single_selector"]
> * [概觀](sql-database-firewall-configure.md)
> * [Azure 入口網站](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 

伺服器層級防火牆規則可讓系統管理員從指定的 IP 位址或 IP 位址範圍存取 SQL Database 伺服器。 當您有多個資料庫具有相同存取需求，且不想花時間個別設定每個資料庫時，您也可以讓使用者使用伺服器層級的防火牆規則。 Microsoft 建議在可行時使用資料庫層級防火牆規則來增強安全性，並且讓您的資料庫更具有可攜性。 如需 SQL Database 防火牆的概觀，請參閱 [SQL Database 防火牆規則的概觀](sql-database-firewall-configure.md).

> [!Note]
> 如需可讓業務持續運作的可攜式資料庫資訊，請參閱[災害復原的驗證需求](sql-database-geo-replication-security-config.md)。
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>透過 Azure 入口網站管理現有的伺服器層級防火牆規則
重複管理伺服器層級防火牆規則的步驟。

* 若要新增目前的電腦，按一下 [新增用戶端 IP]。
* 若要新增其他 IP 位址，請輸入「規則名稱」、「起始 IP 位址」和「結束 IP 位址」。
* 若要修改現有的規則，請按一下和修改規則中的任何欄位。
* 若要刪除現有的規則，將滑鼠停留在規則上方，直到資料列結尾出現 X。 按一下 X 移除規則。

按一下 [儲存]  儲存變更。

## <a name="next-steps"></a>後續步驟

- 如需入門教學課程，請參閱 [SQL Database 教學課程︰建立伺服器、伺服器層級防火牆規則、範例資料庫、資料庫層級防火牆規則，以及與 SQL Server 連接](sql-database-get-started.md)。
- 如需入門安全性教學課程相關資訊，請參閱[安全性入門](sql-database-get-started-security.md)
- 如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱 [SQL Database 的用戶端快速入門程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
- 若要了解如何建立其他可連線至資料庫的使用者，請參閱 [SQL Database 驗證和授權︰授與存取](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他資源
* [保護您的資料庫](sql-database-security.md)   
* [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Dec16_HO2-->


