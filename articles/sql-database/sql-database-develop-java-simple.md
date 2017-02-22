---
title: "使用 Java 以 Windows 的 JDBC 連接到 SQL Database | Microsoft Docs"
description: "提供可用來連接到 Azure SQL Database 的 Java 程式碼範例。 這個範例使用在 Windows 用戶端電腦上執行的 JDBC。"
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: genemi
ms.assetid: 08fc49b1-cd48-4dcc-a293-ff22a4d2d62c
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: aafc89ca958e4bdc2ba52bf1a7d379e3a4cddf1c


---
# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>在 Windows 上搭配 JDBC 使用 Java 連接到 SQL Database
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題提供可用來連接到 Azure SQL Database 的 Java 程式碼範例。 Java 範例需要 Java Development Kit (JDK) 1.8 版。 這個範例使用 JDBC 驅動程式連接到 Azure SQL Database。

## <a name="step-1--configure-development-environment"></a>步驟 1︰設定開發環境
[Step&1;: Configure development environment for Java development (步驟&1;：設定 Java 開發的開發環境)](https://docs.microsoft.com/sql/connect/jdbc/step-1-configure-development-environment-for-java-development/)

## <a name="step-2-create-a-sql-database"></a>步驟 2：建立 SQL Database
請參閱 [快速入門頁面](sql-database-get-started.md) ，以了解如何建立資料庫。  

## <a name="step-3-get-connection-string"></a>步驟 3：取得連接字串
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> 如果您使用 JTDS JDBC 驅動程式，則您必須在連接字串的 URL 加入 "ssl=require"，然後您需要設定 JVM 的下列選項 "-Djsse.enableCBCProtection=false"。 此 JVM 選項會停用安全性漏洞修正程式，因此請確定您了解會涉及到哪些風險，才能設定此選項。
> 
> 

## <a name="step-4-run-sample-code"></a>步驟 4︰執行範例程式碼
* [Step&3;: Proof of concept connecting to SQL using Java (步驟&3;：使用 Java 連接到 SQL 的概念證明)](https://docs.microsoft.com/sql/connect/jdbc/step-3-proof-of-concept-connecting-to-sql-using-java/)

## <a name="next-steps"></a>後續步驟
* 瀏覽 [Java 開發人員中心](/develop/java/)。
* 檢閱 [SQL Database 開發概觀](sql-database-develop-overview.md)
* 更多有關 [用於 SQL Server 的 Microsoft JDBC 驅動程式](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/)

## <a name="additional-resources"></a>其他資源
* [多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


