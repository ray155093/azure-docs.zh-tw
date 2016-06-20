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

## 後續步驟

[探索 Azure SQL Database 教學課程](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0608_2016-->