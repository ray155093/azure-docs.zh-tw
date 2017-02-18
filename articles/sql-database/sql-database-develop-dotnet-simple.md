---
title: "使用 .NET (C#) 連接到 SQL Database | Microsoft Docs"
description: "使用這個快速入門中的範例程式碼建置現代應用程式，這個應用程式使用 C#，並受到具有 Azure SQL Database 的雲端中之強大關聯式資料庫的支援。"
services: sql-database
documentationcenter: 
author: tobbox
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/03/2017
ms.author: tobiast
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: cc756c662b97c64ab30a7d5bf2cc325f9e74d905


---
# <a name="connect-to-sql-database-by-using-net-c"></a>使用 .NET (C#) 連接到 SQL Database
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## <a name="step-1--configure-development-environment"></a>步驟 1︰設定開發環境
[設定 ADO.NET 開發的開發環境](https://docs.microsoft.com/sql/connect/ado-net/step-1-configure-development-environment-for-ado-net-development/)

## <a name="step-2-create-a-sql-database"></a>步驟 2：建立 SQL Database
請參閱 [快速入門頁面](sql-database-get-started.md) ，以了解如何建立範例資料庫。  請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 以下所示的範例僅適用於 **AdventureWorks 結構描述**。  

## <a name="step-3--get-connection-string"></a>步驟 3：取得連接字串
[!INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>步驟 4︰執行範例程式碼
* [使用 ADO.NET 連接到 SQL 的概念證明](https://docs.microsoft.com/sql/connect/ado-net/step-3-proof-of-concept-connecting-to-sql-using-ado-net/)
* [使用 ADO.NET 復原連接 SQL](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net/)

## <a name="next-steps"></a>後續步驟
* [使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* 檢閱 [SQL Database 開發概觀](sql-database-develop-overview.md)
* 更多有關 [Microsoft ADO.Net Driver for SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server/)

## <a name="additional-resources"></a>其他資源
* [多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


