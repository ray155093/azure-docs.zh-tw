<properties
   pageTitle="雲端商務持續性 - 還原已刪除的資料庫- SQL Database | Microsoft Azure"
   description="了解還原時間點，其可讓您復原 Azure SQL Database 到先前的時間 (最多 35 天)。"
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# 概觀：還原已刪除的 Azure SQL Database

> [AZURE.SELECTOR]
- [商務持續性概觀](sql-database-business-continuity.md)
- [還原時間點](sql-database-point-in-time-restore.md)
- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)
- [異地還原](sql-database-geo-restore.md)
- [主動式異地複寫](sql-database-geo-replication-overview.md)
- [商務持續性案例](sql-database-business-continuity-scenarios.md)


您可以針對您的[服務層](sql-database-service-tiers.md)，在 [SQL Database 自動備份](sql-database-automated-backups.md)的保留期間，還原已刪除的資料庫。您可以使用 [Azure 入口網站](sql-database-restore-deleted-database-portal.md)、[PowerShell](sql-database-restore-deleted-database-powershell.md) 或 [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx)。

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## 還原已刪除的資料庫

您可以使用相同或不同的資料庫名稱，將已刪除的資料庫還原至包含原始資料庫的邏輯伺服器。對於已刪除的資料庫，還原點會固定為資料庫的刪除時間。

> [AZURE.IMPORTANT] 如果您刪除 Azure SQL Database 伺服器執行個體，其所有資料庫也會一併刪除且無法加以復原。

## 還原時間

還原資料庫所花費的時間取決於許多因素，包括資料庫大小、交易記錄數目、選取的時間點，以及需要重新執行來重新建構所選時間點狀態的活動數目。針對非常大型及/或作用中的資料庫，還原可能需要數小時。還原資料庫一律會在相同伺服器建立新的資料庫做為原始資料庫，因此還原的資料庫必須指定新名稱。大多數的資料庫還原會在 12 小時內完成。

## 摘要

自動備份可保護您的資料庫免於發生資料意外刪除。此零成本免管理解決方案可供所有 SQL 資料庫使用。

## 後續步驟

- 如需如何使用 Azure 入口網站還原已刪除資料庫的詳細步驟，請參閱[使用 Azure 入口網站還原已刪除的資料庫](sql-database-restore-deleted-database-portal.md)。
- 如需如何使用 PowerShell 還原已刪除資料庫的詳細步驟，請參閱[使用 PowerShell 還原已刪除的資料庫](sql-database-restore-deleted-database-powershell.md)。
- 如需如何還原已刪除資料庫的相關資訊，請參閱[使用 REST API 還原刪除的資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)。
- 如需 Azure SQL Database 自動備份的詳細資訊，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。

## 其他資源

- [還原時間點](sql-database-point-in-time-restore.md)
- [商務持續性概觀](sql-database-business-continuity.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->