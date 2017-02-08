---
title: "Azure SQL Database 邏輯伺服器概觀 | Microsoft Docs"
description: "此頁面提供使用 Azure SQL 邏輯伺服器的考量和指導方針。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 17e2830dceeaa313dd0fd7d406bf68a75b6f900e


---
# <a name="azure-sql-database-logical-servers"></a>Azure SQL Database 邏輯伺服器

本主題提供使用 Azure SQL 邏輯伺服器的考量和指導方針。 如需 Azure SQL 資料庫的詳細資訊，請參閱 [SQL Database](sql-database-overview.md)。

## <a name="what-is-an-azure-sql-database-logical-server"></a>什麼是 Azure SQL Database 邏輯伺服器？
Azure SQL Database 邏輯伺服器做為多個資料庫的中央管理點。 在 SQL Database 中，伺服器是邏輯建構，不同於您可能已熟悉運用在內部部署世界中的 SQL Server 執行個體。 具體來說，SQL Database 服務對於其邏輯伺服器相關之資料庫位置不提供任何保證，且不公開任何執行個體層級存取權或功能。 如需 Azure SQL 邏輯伺服器的詳細資訊，請參閱[邏輯伺服器](sql-database-server-overview.md)。 

Azure 資料庫邏輯伺服器：

- 建立在 Azure 訂用帳戶內，但可以使用其所包含的資源移至另一個訂用帳戶
- 是資料庫的父資源、彈性集區和資料倉儲
- 提供資料庫的命名空間、彈性集區和資料倉儲
- 是具有強式存留期語意 - 刪除伺服器的邏輯容器，就刪除自主資料庫、彈性集區、資料倉儲
- 參與 Azure 角色型存取控制 (RBAC)；伺服器內的資料庫、彈性集區會從伺服器繼承存取權限
- 是供 Azure 資源管理的資料庫身分識別和彈性集區的高序位項目 (請參閱資料庫和集區的 URL 配置)
- 區域中的共置資源
- 提供用來存取資料庫的連接端點 (<serverName>.database.windows.net)
- 藉由連接到 master 資料庫提供存取有關透過 DMV 內含資源的中繼資料 
- 提供適用於其資料庫的管理原則範圍︰登入、防火牆、稽核、威脅偵測等等。 
- 會受父訂用帳戶內的配額限制 (每個訂用帳戶六部伺服器 - [在此參閱訂用帳戶限制](../azure-subscription-service-limits.md))
- 提供其包含的資料庫配額和 DTU 配額範圍 (例如在 V12 中 45000 DTU)
- 內含資源 (最新版本是 V12) 上啟用功能的版本控制範圍
- 伺服器層級主體登入可以管理伺服器上的所有資料庫
- 可以包含類似內部部署之 SQL Server 執行個體中的登入，其在伺服器上一或多個資料庫被授與存取，且可以授與有限的系統管理權限。 如需詳細資訊，請參閱[登入](sql-database-manage-logins.md)。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>如何連接及驗證 Azure SQL Database 邏輯伺服器？

- **驗證和授權**：Azure SQL Database 支援 SQL 驗證及 Azure Active Directory 驗證 (有特定限制 - 請參閱 [使用 Azure Active Directory 驗證連接到 SQL Database](sql-database-aad-authentication.md) 進行驗證。 您可以透過伺服器的 master 資料庫連接並驗證 Azure SQL Database，或直接連接並驗證使用者資料庫。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的資料庫和登入](sql-database-manage-logins.md)。 不支援 Windows 驗證。 
- **TDS**：Microsoft Azure SQL Database 支援表格式資料流 (TDS) 通訊協定用戶端 7.3 版或更新版本。
- **TCP/IP**：僅允許 TCP/IP 連線。
- **SQL Database 防火牆**：為了協助保護您的資料，SQL Database 防火牆會防止對您的資料庫伺服器或其資料庫的所有存取，直到您指定哪些電腦擁有權限。 請參閱[防火牆](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>支援哪些定序？

Microsoft Azure SQL Database (包含 master 資料庫) 使用的預設資料庫定序是 **SQL_LATIN1_GENERAL_CP1_CI_AS**，其中 **LATIN1_GENERAL** 是英文 (美國)，**CP1** 是代碼頁 1252，**CI** 不區分大小寫，**AS** 區分重音。 不建議在建立之後變更 V12 資料庫的定序。 如需定序的詳細資訊，請參閱＜[COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)＞。

## <a name="what-are-the-naming-requirements-for-database-objects"></a>資料庫物件的命名需求有哪些？

所有新物件的名稱都必須遵循識別碼的 SQL Server 規則。 如需詳細資訊，請參閱 [識別碼](https://msdn.microsoft.com/library/ms175874.aspx)。

## <a name="what-features-are-supported"></a>支援哪些功能？

如需支援功能的相關資訊，請參閱「[功能](sql-database-features.md)」。 另請參閱 [Azure SQL Database Transact-SQL 的差異](sql-database-transact-sql-information.md)，了解缺乏特定類型功能支援的原因詳細背景。

## <a name="how-do-i-manage-a-logical-server"></a>如何管理邏輯伺服器？

您可以使用幾種方法管理 Azure SQL Database 邏輯伺服器︰
- [Azure 入口網站](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>後續步驟

- 如需了解 Azure SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database？](sql-database-technical-overview.md)
- 如需支援功能的相關資訊，請參閱「[功能](sql-database-features.md)」
- 如需了解 Azure SQL Database 的概觀，請參閱 [SQL Database 概觀](sql-database-overview.md)
- 如需了解 Transact-SQL 支援與差異的詳細資訊，請參閱 [ 的差異](sql-database-transact-sql-information.md)。
- 如需根據您**服務層**的特定資源配額和限制。 如需服務層級的概觀，請參閱 [SQL Database 服務層級](sql-database-service-tiers.md)。
- 如需安全性概觀，請參閱 [Azure SQL Database 安全性概觀](sql-database-security-overview.md)。
- 如需驅動程式的可用性和 SQL Database 支援的相關資訊，請參閱＜ [SQL Database 與 SQL Server 的連線庫](sql-database-libraries.md)＞。




<!--HONumber=Dec16_HO4-->


