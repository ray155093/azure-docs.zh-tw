---
title: "在 Windows 上使用 PHP 連接到 SQL Database | Microsoft Docs"
description: "提供可從 Windows 用戶端連接到 Azure SQL Database 的範例 PHP 程式，並提供用戶端所需之必要軟體元件的連結。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 10/03/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 162c89b07a0bc690600f0cc59e3f4c70e02eac91


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>在 Windows 上使用 PHP 連接到 SQL Database
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題說明如何從以 PHP 撰寫並在 Windows 上執行的用戶端應用程式，連接到 Azure SQL Database。

## <a name="step-1--configure-development-environment"></a>步驟 1︰設定開發環境
[Step 1: Configure development environment for PHP development (步驟 1：設定 PHP 開發的開發環境)](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>步驟 2：建立 SQL Database
請參閱 [快速入門頁面](sql-database-get-started.md) ，以了解如何建立範例資料庫。  請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 以下所示的範例僅適用於 **AdventureWorks 結構描述**。

## <a name="step-3-get-connection-details"></a>步驟 3：取得連接詳細資料
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>步驟 4︰執行範例程式碼
* [Step 3: Proof of concept connecting to SQL using PHP (步驟 3：使用 PHP 連接到 SQL 的概念證明)](https://msdn.microsoft.com/library/mt720665.aspx)
* [Step 4: Connect resiliently to SQL with PHP (步驟 4：使用 PHP 復原連接 SQL)](https://msdn.microsoft.com/library/mt720667.aspx)

## <a name="next-steps"></a>後續步驟
* 檢閱 [SQL Database 開發概觀](sql-database-develop-overview.md)
* 更多有關 [Microsoft PHP Driver for SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* 如需 PHP 安裝和使用方式的詳細資訊，請參閱 [使用 PHP 存取 SQL Server Database](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx)。

## <a name="additional-resources"></a>其他資源
* [多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


