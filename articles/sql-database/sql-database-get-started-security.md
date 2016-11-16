---
title: "SQL Database 教學課程︰開始使用安全性"
description: "了解如何建立使用者帳戶來存取和管理資料庫。"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3bd8ac5466ae67df0a1865509c8fb897d1374ec


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>SQL Database 教學課程：建立 SQL Database 使用者帳戶來存取和管理資料庫
> [!div class="op_single_selector"]
> * [開始使用教學課程](sql-database-get-started-security.md)
> * [授與存取權](sql-database-manage-logins.md)
> 
> 

在本教學課程中，您會了解如何使用 SQL Server Management Studio (SSMS)：

* 使用伺服器層級主體登入來登入 SQL Database。
* 建立 SQL Database 使用者帳戶。
* 授與 SQL Database 使用者 [db_owner 權限](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0)。
* 以不是伺服器層級主體的使用者帳戶連接到 SQL Database。

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>後續步驟
您現在已完成本 SQL Database 教學課程，而且建立使用者帳戶並授予 dbo 權限，即可開始深入了解 [SQL Database 安全性](sql-database-manage-logins.md)。




<!--HONumber=Nov16_HO2-->


