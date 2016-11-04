---
title: 複製 Azure SQL Database | Microsoft Docs
description: 建立 Azure SQL Database 的複本
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 06/16/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# <a name="copy-an-azure-sql-database"></a>複製 Azure SQL Database
> [!div class="op_single_selector"]
> * [概觀](sql-database-copy.md)
> * [Azure 入口網站](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

您可以使用 Azure [SQL Database 自動備份](sql-database-automated-backups.md) 來建立 SQL Database 的複本。 複製作業會複製交易記錄的結尾，然後使用完整、差異及交易記錄備份 (其為要建立複本的自動備份的一部分)，以便與截至最後一個交易記錄備份為止的來源資料庫維持交易一致性。 

您可以在相同伺服器或不同伺服器上建立資料庫副本。 資料庫副本與來源資料庫的服務層和效能層級 (定價層) 相同。 複製完成之後，副本會變成功能完整的獨立資料庫。 可以個別管理登入、使用者和權限。 

當您將資料庫複製到相同的邏輯伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者 (DBO)。 所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。 

當您將資料庫複製到不同的邏輯伺服器時，新伺服器上的安全性主體就會變成新資料庫上的資料庫擁有者。 內含使用者的資料庫使用者可用於複製的資料庫中。 不過，當您將資料庫複製到新的伺服器時，以登入為基礎的使用者通常將不會運作，因為登入不存在於新的伺服器上，而且如果它們存在，它們的 SID 將不會相符。 在新資料庫於目的地伺服器上線之後，使用 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 陳述式將使用者從新的資料庫重新對應至目的地伺服器上的登入。 若要解析被遺棄的使用者，請參閱 [被遺棄使用者疑難排解](https://msdn.microsoft.com/library/ms175475.aspx)。 

若要複製 SQL Database，您需要下列項目：

* Azure 訂用帳戶。 如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]  ，然後再回來完成這篇文章。
* 要複製的 SQL Database。 如果您沒有 SQL Database，請遵循此文章中的步驟來建立： [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

## <a name="next-steps"></a>後續步驟
* 若要使用 Azure 入口網站複製資料庫，請參閱 [使用 Azure 入口網站複製 Azure SQL Database](sql-database-copy-portal.md) 。
* 若要使用 PowerShell 複製資料庫，請參閱 [使用 PowerShell 複製 Azure SQL Database](sql-database-copy-powershell.md) 。
* 若要使用 Transact-SQL 複製資料庫，請參閱 [使用 T-SQL 複製 Azure SQL Database](sql-database-copy-transact-sql.md) 。
* 請參閱 [如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md) ，以了解如何在將資料庫複製到不同的邏輯伺服器時管理使用者與登入。

## <a name="additional-resources"></a>其他資源
* [管理登入](sql-database-manage-logins.md)
* [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)
* [將資料庫匯出至 BACPAC](sql-database-export.md)
* [商務持續性概觀](sql-database-business-continuity.md)
* [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!--HONumber=Oct16_HO2-->


