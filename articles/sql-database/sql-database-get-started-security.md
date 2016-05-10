<properties
	pageTitle="SQL Database 教學課程︰開始使用安全性"
	description="了解如何建立使用者帳戶來存取和管理資料庫。"
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="carlrab"/>

# SQL Database 教學課程：使用 Azure 入口網站建立 SQL Database 使用者帳戶來存取和管理資料庫。

在本教學課程裡，您將學習如何使用 Azure 入口網站來：

- 使用伺服器層級主體登入來登入 SQL Database
- 建立 SQL Database 使用者帳戶
- 授與使用者資料庫內的 SQL Database 使用者帳戶 dbo 權限
- 以不是伺服器層級主體的使用者帳戶連接到 SQL Database 

[AZURE.INCLUDE [登入](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [建立 SQL Database 邏輯伺服器](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [建立 SQL Database 資料庫](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [建立 SQL Database 資料庫](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [建立 SQL Database 資料庫](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## 後續步驟
您現在已完成本 SQL Database 教學課程，而且建立使用者帳戶並授予 dbo 權限，即可開始深入了解 [SQL Database 安全性](sql-database-manage-logins.md)。

<!---HONumber=AcomDC_0504_2016-->