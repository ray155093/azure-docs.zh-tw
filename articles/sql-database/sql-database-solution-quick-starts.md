<properties
   pageTitle="Azure SQL Database 解決方案快速入門 | Microsoft Azure"
   description="了解 Azure SQL Database 解決方案"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# 探索 Azure SQL Database 解決方案快速入門

本文包含「Azure SQL Database 解決方案快速入門」的概觀。這些快速入門位於 GitHub SQL Server 範例儲存機制中，並能以實際案例為基礎的完整解決方案示範 SQL Database 的使用方式。如需示範如何使用特定 SQL Database 功能的簡單逐步教學課程，請參閱[探索 Azure SQL Database 教學課程](sql-database-explore-tutorials.md)。

## 請嘗試 WingTipTickets 示範和實際操作實驗室

[Azure SQL Database WingTipTickets](https://github.com/microsoft/wingtiptickets) 示範和線上實習實驗室能示範以 Azure SQL Database 和 Azure 搜尋服務為基礎，且用於銷售音樂會門票的範例應用程式。


## 收集和監視多個集區的資源使用狀況資料

[解決方案快速入門：使用 PowerShell 進行彈性集區遙測](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)提供收集和監視訂用帳戶中多個集區的 SQL Database 資源使用狀況的解決方案。當訂用帳戶中有大量資料庫時，將會難以分別監視每個彈性集區。

若要解決這個難題，您可以結合 SQL Database PowerShell Cmdlet 和 T-SQL 查詢，從多個集區及其資料庫收集資源使用狀況資料。這可以協助您更有效地監視並分析資源使用狀況。

本快速入門提供一組 PowerShell 指令碼和 T-SQL 查詢，以及描述解決方案用途和實作方式的文件。

## 在 SaaS 案例中開始使用彈性資料庫

 [Solution Quick Start: Elastic Pool custom dashboard for SaaS (解決方案快速入門：適用於 SaaS 的彈性集區自訂儀表板)](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) 可為「軟體即解決方案 (SaaS)」案例提供能利用 SQL Database 的彈性資料庫功能來提供符合成本效益且可調整的 SaaS 應用程式資料庫後端的解決方案。

在這個解決方案中，您將會逐步完成 Web 應用程式的實作。此 Web 應用程式能讓您視覺化由使用可補充 Azure 入口網站之自訂儀表板的負載產生器在彈性資料庫上建立的負載。

本快速入門提供負載產生器和監視 Web 應用程式，以及描述應用程式用途和使用方式的文件。

## 使用 Code First 開發和 Entity Framework 建立 Azure SQL Database

[Code First 至新的資料庫](https://msdn.microsoft.com/data/jj193542.aspx)中的視訊和範例提供以新資料庫為目標的 Code First 開發介紹。此案例會以尚不存在，但會由 Code First 建立的資料庫為目標。或者，案例會建立空的資料庫，讓 Code First 新增新的資料表。

Code First 能讓您使用 C# 或 Visual Basic .NET 類別定義您的模型。您可以使用您類別或內容上的屬性，或是使用 Fluent API，來執行選擇性的其他設定。

## 將彈性資料庫工具整合到 Entity Framework 應用程式中

[搭配使用彈性資料庫用戶端程式庫與 Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) 範例顯示您需要對 Entity Framework 應用程式做出的變更，以將它與[彈性資料庫工具](sql-database-elastic-scale-get-started.md)整合。重點將著重於使用 Entity Framework Code First 方法來編寫[分區對應管理](sql-database-elastic-scale-shard-map-management.md)和[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)。

這整個範例中將會以 [EF 的 Code First 至新的資料庫範例](http://msdn.microsoft.com/data/jj193542.aspx)做為執行範例。本文所附的範例程式碼取自於 Visual Studio 程式碼範例中的彈性資料庫工具範例集。

## 整合彈性資料庫工具和資料列層級安全性

[使用彈性資料庫工具和資料列層級安全性的多租用戶應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md)顯示您需要對 Entity Framework 應用程式做出的變更，以整合[彈性資料庫工具](sql-database-elastic-scale-get-started.md)和[資料列層級安全性](https://msdn.microsoft.com/library/dn765131)。此範例說明如何運用這些技術建置應用程式，且讓此應用程式具有可支援多租用戶分區的高度可調整資料層。

您可使用 ADO.NET SqlClient 或 Entity Framework 來達成此目的。此範例會新增對多租用戶分區資料庫的支援，藉此擴充[採用 Entity Framework 的彈性資料庫用戶端程式庫](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)。這麼做可以建立具有四個租用戶與兩個多租用戶分區資料庫的簡單主控台應用程式，以用於建立部落格和文章。

## 透過 Tailspin Surveys 應用程式建立線上問卷

此 [Tailspin Surveys 範例應用程式](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)是可讓使用者建立線上問卷的多租用戶 Web 應用程式 (稱為 Surveys)。此範例可解答有關在多租用戶應用程式中管理使用者識別的一些重要疑慮，包括註冊、驗證、授權和應用程式角色。

## 透過 Contoso 診所示範應用程式了解 SQL Database 的最新安全性功能

此 [Contoso 診所示範應用程式](https://github.com/Microsoft/azure-sql-security-sample)能展示 SQL Database 最新的安全性功能。

## 後續步驟

[探索 Azure SQL Database 教學課程](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0907_2016-->