<properties
   pageTitle="Azure SQL Database 解決方案快速入門 | Microsoft Azure"
   description="了解 Azure SQL Database 解決方案"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="06/01/2016"
   ms.author="carlrab"/>

# 探索 Azure SQL Database 解決方案快速入門

本文包含「Azure SQL Database 解決方案快速入門」的概觀。這些「快速入門」是根據真實世界案例，示範在完整解決方案中如何使用 SQL Database。如需示範如何使用特定 Azure SQL Database 功能的簡單逐步教學課程，請參閱[探索 Azure SQL Database 教學課程](sql-database-explore-tutorials.md)。

## 收集和監視多個集區的資源使用狀況資料

這個「解決方案快速入門」提供一個解決方案來收集和監視訂用帳戶中多個集區的 Azure SQL Database 資源使用狀況。當訂用帳戶中有大量資料庫時，難以分開監視每個彈性集區。若要解決這個難題，您可以結合 SQL Database PowerShell Cmdlet 和 T-SQL 查詢，從多個集區及其資料庫收集資源使用狀況資料，以便監視和分析資源使用狀況。

GitHub SQL Server 範例儲存機制中的[使用 PowerShell 和 Power BI 來管理 SQL Database 中的多個彈性集區](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)提供一組 PowerShell 指令碼和 T-SQL 查詢，以及其用途和使用方式的相關文件。

## 在 SaaS 案例中開始使用彈性集區

這個「解決方案快速入門」為「軟體即解決方案」(SaaS) 案例提供一個解決方案，它利用「彈性集區」來提供符合成本效益、可調整的 SaaS 應用程式資料庫後端。在此解決方案中，您將逐步完成一個 Web 應用程式實作，此應用程式可讓您使用補充「Azure 入口網站」的自訂儀表板，以視覺化方式呈現負載產生器在「彈性集區」上建立的負載，。

GitHub SQL Server 範例儲存機制中的 [Saas-scenario-with-elastic-pools](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) 提供一個負載產生器和監視 Web 應用程式，以及其用途和使用方式的相關文件。

## 使用 Entity Framework 和 Code First 開發建立 Azure SQL Database

此視訊和範例提供以新資料庫為目標的 Code First 開發介紹。此案例包含以不存在而 Code First 將建立的資料庫為目標，或以 Code First 將加入新資料表的空白資料庫為目標。Code First 可讓您使用 C# 或 VB.Net 類別定義您的模型。使用您的類別或內容 (Property) 的屬性 (Attribute) 可選擇性地執行其他設定，或可使用 Fluent API 來執行。請參閱[新資料庫的 Code First](https://msdn.microsoft.com/data/jj193542.aspx)。

## 將彈性資料庫工具整合到 Entity Framework 應用程式中

此範例說明 Entity Framework 應用程式為了要與[彈性資料庫工具](sql-database-elastic-scale-get-started.md)整合所需做的變更。重點在於使用 Entity Framework Code First 方法來編寫[分區對應管理](sql-database-elastic-scale-shard-map-management.md)和[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)。這整個範例中以 EF 的 [Code First – EF 的新資料庫範例](http://msdn.microsoft.com/data/jj193542.aspx)作為執行範例。本文所附的範例程式碼取自於 Visual Studio 程式碼範例中的彈性資料庫工具範例集。請參閱[採用 Entity Framework 的彈性資料庫用戶端程式庫](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)。

## 使用彈性資料庫工具和資料列層級安全性的多租用戶應用程式

此範例說明 Entity Framework 應用程式為了整合[彈性資料庫工具](sql-database-elastic-scale-get-started.md)與[資料列層級安全性](https://msdn.microsoft.com/library/dn765131)所需做的變更。此範例說明如何運用這些技術，透過 ADO.NET SqlClient 和/或 Entity Framework 建置應用程式，且讓此應用程式具有可支援多租用戶分區的高度可擴充資料層。此範例會新增對多租用戶分區資料庫的支援，藉此擴充[採用 Entity Framework 的彈性資料庫用戶端程式庫](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)。這麼做可以建立具有四個租用戶與兩個多租用戶分區資料庫的簡單主控台應用程式，以用於建立部落格和文章。請參閱[使用彈性資料庫工具和資料列層級安全性的多租用戶應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md)。

## Tailspin Surveys 應用程式

此範例是可讓使用者建立線上問卷的多租用戶 Web 應用程式 (稱為 Surveys)。此範例示範管理多租用戶應用程式中的使用者識別時的一些重要考量，包括註冊、驗證、授權和應用程式角色。若要執行這個範例，請參閱[如何執行 Tailspin Surveys 範例應用程式](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)。

## 後續步驟

[探索 Azure SQL Database 教學課程](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0615_2016-->