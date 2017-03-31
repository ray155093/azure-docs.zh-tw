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
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e64abfd5581c02b609707f7fa712962c024b293b
ms.lasthandoff: 03/23/2017


---
# <a name="copy-an-azure-sql-database"></a>複製 Azure SQL Database

您可以在相同伺服器或不同伺服器上建立資料庫副本。 資料庫複本是發生複製要求時的來源資料庫快照集。 資料庫副本與來源資料庫的服務層和效能等級 (定價層) 預設會相同。 使用 API 時，您可以選取相同服務層 (版本) 內的不同效能等級。 複製完成之後，副本會變成功能完整的獨立資料庫。 此時，您可以將它升級或降級成任何版本。 可以個別管理登入、使用者和權限。  

當您將資料庫複製到相同的邏輯伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者 (DBO)。 所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。  

當您將資料庫複製到不同的邏輯伺服器時，新伺服器上的安全性主體就會變成新資料庫上的資料庫擁有者。 如果您使用[自主資料庫使用者](sql-database-manage-logins.md)來進行資料存取，請確保主要和次要資料庫一律具有相同的使用者認證，以便在複製完成時，您可以使用相同的認證立即存取它。 如果您使用 [Azure Active Directory](../active-directory/active-directory-whatis.md)，則可以完全不需管理副本中的認證。 不過，當您將資料庫複製到新的伺服器時，以登入為基礎的存取可能無法運作，因為登入不存在於新的伺服器上。 請參閱[如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md)，以了解如何在將資料庫複製到不同的邏輯伺服器時管理登入。 

若要建立 SQL 資料庫，您可以使用 [Azure 入口網站](sql-database-copy-portal.md)、[PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md)  或 [T-SQL](sql-database-copy-transact-sql.md)。 

## <a name="next-steps"></a>後續步驟

* 請參閱 [如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md) ，以了解如何在將資料庫複製到不同的邏輯伺服器時管理使用者與登入。
* 如需登入的相關資訊，請參閱[管理登入](sql-database-manage-logins.md)。
* 若要匯出資料庫，請參閱[將資料庫匯出至 BACPAC](sql-database-export.md)

