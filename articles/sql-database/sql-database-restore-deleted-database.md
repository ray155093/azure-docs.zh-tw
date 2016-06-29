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
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# 概觀：還原已刪除的 Azure SQL Database

> [AZURE.SELECTOR]
- [概觀](sql-database-restore-deleted-database.md)
- [Azure 入口網站](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

您可以在 [SQL Database 自動備份](sql-database-automated-backups.md)的保留期間，還原已刪除的資料庫。您可以使用 [Azure 入口網站](sql-database-restore-deleted-database-portal.md)、[PowerShell](sql-database-restore-deleted-database-powershell.md) 或 [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx)。

如果您刪除資料庫，最後一個備份會在一般的保留期內保留，讓您將資料庫還原到它被刪除時的時間點。

## 還原已刪除的資料庫

對於刪除的資料庫，還原點會固定為資料庫的刪除時間。還原已刪除的資料庫時，只能還原到包含原始資料庫的伺服器。刪除伺服器時請留意這一點，因為刪除伺服器之後，您就無法還原先前位於該伺服器上的資料庫。

> [AZURE.IMPORTANT] 如果您刪除 Azure SQL Database 伺服器執行個體，其所有資料庫也會一併刪除且無法加以復原。

## 摘要

自動備份可保護您的資料庫免於發生資料意外刪除。此零成本免管理解決方案可供所有 SQL 資料庫使用。

## 後續步驟

- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [使用 Azure 入口網站還原已刪除的資料庫](sql-database-restore-deleted-database-portal.md)
- [使用 PowerShell 還原已刪除的資料庫](sql-database-restore-deleted-database-powershell.md)
- [使用 REST API 還原已刪除的資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動備份](sql-database-automated-backups.md)

## 其他資源

- [時間點還原](sql-database-point-in-time-restore.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->