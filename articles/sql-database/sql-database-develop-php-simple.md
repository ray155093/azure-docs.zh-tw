<properties
	pageTitle="在 Windows 上使用 PHP 連接到 SQL Database | Microsoft Azure"
	description="提供可從 Windows 用戶端連接到 Azure SQL Database 的範例 PHP 程式，並提供用戶端所需之必要軟體元件的連結。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="meetb"/>


# 在 Windows 上使用 PHP 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題說明如何從以 PHP 撰寫並在 Windows 上執行的用戶端應用程式，連接到 Azure SQL Database。

## 步驟 1︰設定開發環境

[Step 1: Configure development environment for PHP development (步驟 1：設定 PHP 開發的開發環境)](https://msdn.microsoft.com/library/mt720663.aspx)

## 步驟 2：建立 SQL Database

請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立範例資料庫。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。


## 步驟 3：取得連接詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## 步驟 4︰執行範例程式碼

* [Step 3: Proof of concept connecting to SQL using PHP (步驟 3：使用 PHP 連接到 SQL 的概念證明)](https://msdn.microsoft.com/library/mt720665.aspx)
* [Step 4: Connect resiliently to SQL with PHP (步驟 4：使用 PHP 復原連接 SQL)](https://msdn.microsoft.com/library/mt720667.aspx)


## 後續步驟


如需 PHP 安裝和使用方式的詳細資訊，請參閱[使用 PHP 存取 SQL Server Database](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx)。

## 其他資源

如需多租用戶型軟體即服務 (SaaS) 資料庫應用程式的常見資料架構模式的資訊，請參閱[多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

<!---HONumber=AcomDC_0615_2016-->