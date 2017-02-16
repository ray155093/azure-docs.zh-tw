---
title: "Azure SQL Database 存取控制 | Microsoft Docs"
description: "了解 Microsoft Azure SQL Database 的存取控制。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 8e71b04c-bc38-4153-8f83-f2b14faa31d9
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/18/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 356cc4c6d8e25d36880e4b12bf471326e61990c3
ms.openlocfilehash: f12ed9d76e7c6db5e14ed3c00d7d4087dbd4069c


---
# <a name="azure-sql-database-access-control"></a>Azure SQL Database 存取控制
為了提供安全性，SQL Database 會透過以下機制來控制存取：依 IP 位址限制連線的防火牆規則、要求使用者證明其身分識別的驗證機制，以及將使用者限制在特定動作和資料的授權機制。 

> [!IMPORTANT]
> 如需 SQL Database 安全性功能的概觀，請參閱 [SQL 安全性概觀](sql-database-security-overview.md)。 如需使用 SQL Server 驗證的教學課程，請參閱 [SQL Database 教學課程︰SQL Server 驗證、登入和使用者帳戶、資料庫角色、權限、伺服器層級防火牆規則和資料庫層級防火牆規則](sql-database-control-access-sql-authentication-get-started.md)。 如需使用 Azure Active Directory 驗證的教學課程，請參閱 [SQL Database 教學課程︰AAD 驗證、登入和使用者帳戶、資料庫角色、權限、伺服器層級防火牆規則和資料庫層級防火牆規則](sql-database-control-access-aad-authentication-get-started.md)。

>

## <a name="firewall-and-firewall-rules"></a>防火牆與防火牆規則
Microsoft Azure SQL Database 為 Azure 和其他網際網路式應用程式提供關聯式資料庫服務。 為了協助保護您的資料，防火牆會防止對您的資料庫伺服器的所有存取，直到您指定哪些電腦擁有權限。 此防火牆會根據每一個要求的來源 IP 位址來授與資料庫存取權。 如需詳細資訊，請參閱 [Azure SQL Database 防火牆規則概觀](sql-database-firewall-configure.md)

Azure SQL Database 服務唯有透過 TCP 通訊埠 1433 才能使用。 若要從您的電腦存取 SQL Database，請務必確認您的用戶端電腦防火牆允許 TCP 連接埠 1433 上的傳出 TCP 通訊。 如果其他應用程式不需要，請封鎖 TCP 通訊埠 1433 的傳入連線。 

連接程序當中，從 Azure 虛擬機器的連接會被重新導向到不同的 IP 位址和連接埠，針對每個背景工作角色都是唯一。 連接埠號碼的範圍從 11000 到 11999。 如需 TCP 連接埠的詳細資訊，請參閱 [適用於 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。

## <a name="authentication"></a>驗證

SQL Database 支援兩種驗證類型：

* **SQL 驗證**，其需要使用者名稱和密碼。 當您為資料庫建立邏輯伺服器時，採取使用者名稱和密碼指定了「伺服器管理員」登入。 使用這些認證，您就可以使用資料庫擁有者或 "dbo" 的身分驗證該伺服器上的任何資料庫。 
* **Azure Active Directory 驗證**，它會使用由 Azure Active Directory 管理的身分識別，並支援受管理和整合的網域。 [盡可能](https://msdn.microsoft.com/library/ms144284.aspx)使用 Active Directory 驗證 (整合式安全性)。 如果您想要使用 Azure Active Directory 驗證，就必須建立另一個名為「Azure AD 管理員」的伺服器管理員，其能夠管理 Azure AD 使用者和群組。 此管理員也可以執行一般伺服器管理員可執行的所有作業。 如需如何建立 Azure AD 管理員以啟用 Azure Active Directory 驗證的逐步解說，請參閱 [使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md) 。

資料庫引擎會關閉保持閒置超過 30 分鐘的連接。 必須重新登入才能使用連接。 持續作用中的 SQL Database 連接，需要至少每 10 個小時重新授權一次 (由資料庫引擎執行)。 資料庫引擎嘗試重新授權時，會使用原本送出的密碼，但不需要使用者輸入。 基於效能考量，當在 SQL Database 中重設密碼時，並不會重新驗證連線，即使因連線共用而重設連線時也一樣。 這點與內部部署 SQL Server 的行為不同。 如果連接經過初始授權後，密碼已經變更，則必須終止連接並使用新密碼建立新的連接。 具有 KILL DATABASE CONNECTION 權限的使用者可以使用 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 命令，明確地終止 SQL Database 的連線。

使用者帳戶可以建立在主要資料庫中，並獲得伺服器上所有資料庫的權限，或者，可將帳戶建立在資料庫自身當中 (稱為自主使用者)。 如需建立和管理登入的資訊，請參閱[管理登入](sql-database-manage-logins.md)。 若要提升可攜性和延展性，請使用自主資料庫使用者。 如需自主使用者的詳細資訊，請參閱[自主的資料庫使用者 - 使資料庫可攜](https://msdn.microsoft.com/library/ff929188.aspx)、[CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) 及[自主資料庫](https://technet.microsoft.com/library/ff929071.aspx)。

最好的做法是，您的應用程式應該使用不同的帳戶來驗證，因為萬一應用程式的程式碼容易受到 SQL 插入式攻擊，您就可以限制授與應用程式的權限，並降低惡意活動的風險。 建議的方法是建立[自主資料庫使用者](https://msdn.microsoft.com/library/ff929188)，讓您的應用程式直接與資料庫進行驗證。 您可以藉由執行下列 T-SQL 命令，在以伺服器管理員身分登入連線到您的使用者資料庫時，建立使用 SQL 驗證的自主資料庫使用者：

## <a name="authorization"></a>Authorization

授權是指使用者可以在 Azure SQL Database 內執行的動作，這是由使用者帳戶的資料庫[角色成員資格](https://msdn.microsoft.com/library/ms189121)和[物件層級權限](https://msdn.microsoft.com/library/ms191291.aspx)所控制。 最好的作法是，您應該授與使用者所需的最低權限。 您所連線的伺服器管理帳戶是 db_owner 的成員，有權限在資料庫中執行任何動作。 請儲存此帳戶，以便部署結構描述升級及其他管理作業。 請使用具更多有限權限的 "ApplicationUser" 帳戶，從應用程式連線到具應用程式所需之最低權限的資料庫。 如需詳細資訊，請參閱[管理登入](sql-database-manage-logins.md)。

通常，只有系統管理員需要存取 master 資料庫。 對每個使用者資料庫的例行存取，應該透過每個資料庫中建立的非系統管理員的自主資料庫使用者來存取。 當您使用自主資料庫使用者時，您不需要在 master 資料庫中建立登入。 如需詳細資訊，請參閱 [自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)。

此外，這些功能可用來限制或提高權限。

* [模擬](https://msdn.microsoft.com/library/vstudio/bb669087)和[模組簽署](https://msdn.microsoft.com/library/bb669102)可用來安全地暫時提升權限。
* [資料列層級安全性](https://msdn.microsoft.com/library/dn765131) 可用於使用者可存取資料列的限制。
* [資料遮罩](sql-database-dynamic-data-masking-get-started.md) 可用來限制公開機密資料。
* [預存程序](https://msdn.microsoft.com/library/ms190782) 可用來限制對資料庫可採取的動作。

## <a name="next-steps"></a>後續步驟

- 如需所有 SQL Database 安全性功能的概觀，請參閱 [SQL 安全性概觀](sql-database-security-overview.md)。
- 若要深入了解防火牆規則，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。
- 若要了解使用者和登入，請參閱[管理登入](sql-database-manage-logins.md)。 
- 如需在 SQL Database 中使用資料保護功能的討論，請參閱[資料保護和安全性](sql-database-protect-data.md)。
- 如需主動式監視的討論，請參閱[開始使用 SQL Database 稽核](sql-database-auditing-get-started.md)和[開始使用 SQL Database 威脅偵測](sql-database-threat-detection-get-started.md)。
- 如需使用 SQL Server 驗證的教學課程，請參閱 [SQL Database 教學課程︰SQL Server 驗證、登入和使用者帳戶、資料庫角色、權限、伺服器層級防火牆規則和資料庫層級防火牆規則](sql-database-control-access-sql-authentication-get-started.md)。
- 如需使用 Azure Active Directory 驗證的教學課程，請參閱 [SQL Database 教學課程︰AAD 驗證、登入和使用者帳戶、資料庫角色、權限、伺服器層級防火牆規則和資料庫層級防火牆規則](sql-database-control-access-aad-authentication-get-started.md)。



<!--HONumber=Jan17_HO3-->


