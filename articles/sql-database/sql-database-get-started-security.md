---
title: SQL Database 教學課程︰開始使用安全性
description: 了解如何建立使用者帳戶來存取和管理資料庫。
keywords: ''
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab

---
# SQL Database 教學課程：建立 SQL Database 使用者帳戶來存取和管理資料庫
> [!div class="op_single_selector"]
> * [開始使用教學課程](sql-database-get-started-security.md)
> * [授與存取權](sql-database-manage-logins.md)
> 
> 

在本教學課程中，您會了解如何使用 SQL Server Management Studio (SSMS)：

* 使用伺服器層級主體登入來登入 SQL Database。
* 建立 SQL Database 使用者帳戶。
* 授與 SQL Database 使用者 [db\_owner 權限](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0)。
* 以不是伺服器層級主體的使用者帳戶連接到 SQL Database。

[!INCLUDE [登入](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [建立 SQL Database 邏輯伺服器](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [建立 SQL Database 資料庫](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [建立 SQL Database 資料庫](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [建立 SQL Database 資料庫](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## 後續步驟
您現在已完成本 SQL Database 教學課程，而且建立使用者帳戶並授予 dbo 權限，即可開始深入了解 [SQL Database 安全性](sql-database-manage-logins.md)。

<!---HONumber=AcomDC_0824_2016-->