---
title: "複製 Azure SQL Database | Microsoft Docs"
description: "建立 Azure SQL Database 的複本"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sstein; sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 21be71a1b4c79ecec8af02d08f65c41128c5ef73
ms.openlocfilehash: 265adbccee6764322148fad23f31aa1e279dfc9b


---
# <a name="copy-an-azure-sql-database"></a>複製 Azure SQL Database

您可以使用 [Azure 異地複寫](sql-database-geo-replication-overview.md)功能來建立 SQL Database 的複本。 但是它與異地複寫不同，會在植入階段完成時終止複寫連結。 因此，複製資料庫是發生複製要求時的來源資料庫快照。  

您可以在相同伺服器或不同伺服器上建立資料庫副本。 資料庫副本與來源資料庫的服務層和效能等級 (定價層) 預設會相同。 使用 API 時，您可以選取相同服務層 (版本) 內的不同效能等級。 複製完成之後，副本會變成功能完整的獨立資料庫。 此時，您可以將它升級或降級成任何版本。 可以個別管理登入、使用者和權限。  

當您將資料庫複製到相同的邏輯伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者 (DBO)。 所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。  

當您將資料庫複製到不同的邏輯伺服器時，新伺服器上的安全性主體就會變成新資料庫上的資料庫擁有者。 如果您使用[自主資料庫使用者](sql-database-manage-logins.md)來進行資料存取，請確保主要和次要資料庫一律具有相同的使用者認證，以便在複製完成時，您可以使用相同的認證立即存取它。 如果您使用 [Azure Active Directory](../active-directory/active-directory-whatis.md)，則可以完全不需管理副本中的認證。 不過，當您將資料庫複製到新的伺服器時，以登入為基礎的存取可能無法運作，因為登入不存在於新的伺服器上。 請參閱[如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md)，以了解如何在將資料庫複製到不同的邏輯伺服器時管理登入。 

若要複製 SQL Database，您需要下列項目：

* Azure 訂閱。 如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]  ，然後再回來完成這篇文章。
* 要複製的 SQL Database。 如果您沒有 SQL Database，請遵循此文章中的步驟來建立： [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

## <a name="next-steps"></a>後續步驟
* 若要使用 Azure 入口網站來複製資料庫，請參閱 [使用 Azure 入口網站複製 Azure SQL Database](sql-database-copy-portal.md) 。
* 若要使用 PowerShell 複製資料庫，請參閱 [使用 PowerShell 複製 Azure SQL Database](sql-database-copy-powershell.md) 。
* 若要使用 Transact-SQL 複製資料庫，請參閱 [使用 T-SQL 複製 Azure SQL Database](sql-database-copy-transact-sql.md) 。
* 請參閱 [如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md) ，以了解如何在將資料庫複製到不同的邏輯伺服器時管理使用者與登入。

## <a name="additional-resources"></a>其他資源
* [管理登入](sql-database-manage-logins.md)
* [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)
* [將資料庫匯出至 BACPAC](sql-database-export.md)
* [商務持續性概觀](sql-database-business-continuity.md)
* [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Feb17_HO2-->


