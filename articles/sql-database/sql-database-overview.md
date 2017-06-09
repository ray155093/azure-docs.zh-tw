---
title: "什麼是 Azure SQL Database？ | Microsoft Docs"
description: "本文提供 Azure SQL Database 的概觀。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: ebfc2a6a2e16140953abbb9da3b81702fcc26e60
ms.contentlocale: zh-tw
ms.lasthandoff: 04/15/2017


---
# <a name="azure-sql-database-overview"></a>Azure SQL database 概述
此主題提供 Azure SQL Database 的概觀。 如需 Azure SQL 邏輯伺服器的詳細資訊，請參閱[邏輯伺服器](sql-database-server-overview.md)。

## <a name="what-is-azure-sql-database"></a>什麼是 Azure SQL Database？
Azure SQL Database 的每個資料庫會與邏輯伺服器相關聯。 資料庫可以是︰

- 單一資料庫包含其[自有資源集](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- [彈性集區](sql-database-elastic-pool.md)的一部分，[共用一個資源集](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU)
- [分區化資料庫向外延展集](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)的一部分，可以是單一或集區資料庫
- 參與[多租用戶 SaaS 設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)的資料庫集一部分，其資料庫可以是單一值或集區資料庫 (或兩者) 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>如何連接及驗證 Azure SQL Database？

- **驗證和授權**：Azure SQL Database 支援 SQL 驗證及 Azure Active Directory 驗證 (有特定限制 - 請參閱 [使用 Azure Active Directory 驗證連接到 SQL Database](sql-database-aad-authentication.md) 進行驗證。 您可以透過伺服器的 master 資料庫連接並驗證 Azure SQL Database，或直接連接並驗證使用者資料庫。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的資料庫和登入](sql-database-manage-logins.md)。 不支援 Windows 驗證。 
- **TDS**：Microsoft Azure SQL Database 支援表格式資料流 (TDS) 通訊協定用戶端 7.3 版或更新版本。
- **TCP/IP**：僅允許 TCP/IP 連線。
- **SQL Database 防火牆**：為了協助保護您的資料，SQL Database 防火牆會防止對您的資料庫伺服器或其資料庫的所有存取，直到您指定哪些電腦擁有權限。 請參閱[防火牆](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>支援哪些定序？
Microsoft Azure SQL Database 使用的預設資料庫定序是 **SQL_LATIN1_GENERAL_CP1_CI_AS**，其中 **LATIN1_GENERAL** 是英文 (美國)，**CP1** 是代碼頁 1252，**CI** 不區分大小寫，**AS** 區分重音。 如需如何設定定序的詳細資訊，請參閱＜ [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)＞。

## <a name="what-are-the-naming-requirements-for-database-objects"></a>資料庫物件的命名需求有哪些？

所有新物件的名稱都必須遵循識別碼的 SQL Server 規則。 如需詳細資訊，請參閱 [識別碼](https://msdn.microsoft.com/library/ms175874.aspx)。

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Azure SQL Database 支援哪些功能？

如需支援功能的相關資訊，請參閱「[功能](sql-database-features.md)」。 另請參閱 [Azure SQL Database Transact-SQL 的差異](sql-database-transact-sql-information.md)，了解缺乏特定類型功能支援的原因詳細背景。

## <a name="how-do-i-manage-an-azure-sql-database"></a>如何管理 Azure SQL Database？

您可以使用幾種方法管理 Azure SQL Database 邏輯伺服器︰
- [Azure 入口網站](sql-database-manage-overview.md)
- [PowerShell](sql-database-manage-overview.md)
- [Transact-SQL](sql-database-connect-query-ssms.md)
- [Visual Studio Code](sql-database-connect-query-vscode.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>後續步驟

- 如需了解 Azure SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database？](sql-database-technical-overview.md)
- 如需支援功能的相關資訊，請參閱「[功能](sql-database-features.md)」
- 如需了解 Azure SQL 邏輯伺服器的概觀，請參閱 [SQL Database 邏輯伺服器概觀](sql-database-server-overview.md)
- 如需了解 Transact-SQL 支援與差異的詳細資訊，請參閱 [ 的差異](sql-database-transact-sql-information.md)。
- 如需根據您**服務層**的特定資源配額和限制。 如需服務層級的概觀，請參閱 [SQL Database 服務層級](sql-database-service-tiers.md)。
- 如需安全性概觀，請參閱 [Azure SQL Database 安全性概觀](sql-database-security-overview.md)。
- 如需驅動程式的可用性和 SQL Database 支援的相關資訊，請參閱＜ [SQL Database 與 SQL Server 的連線庫](sql-database-libraries.md)＞。


