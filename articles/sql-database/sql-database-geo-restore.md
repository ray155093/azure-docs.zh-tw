<properties
   pageTitle="雲端業務持續性 - 異地還原 | Microsoft Azure"
   description="了解 Azure SQL Database 如何支援雲端商務持續性和資料庫復原，以及如何協助維持任務關鍵性雲端應用程式持續執行。"
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
   ms.date="05/10/2016"
   ms.author="sstein"/>

# 概觀：SQL Database 異地還原

> [AZURE.SELECTOR]
- [概觀](sql-database-geo-restore.md)
- [Azure 入口網站](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

異地還原可讓您從最近的每日備份還原 SQL 資料庫，並且會對所有的服務層自動啟用此功能，不需額外成本。異地還原使用異地備援備份做為其來源，即使因為中斷而無法存取資料庫或資料中心，也能用來復原資料庫。

起始異地還原會建立新的 SQL 資料庫，該資料庫可以建立在任何 Azure 區域中的任何伺服器上。

> [AZURE.NOTE] 您也可以使用 [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


異地還原是當您的資料庫因裝載區域中的事件而無法使用時的預設復原選項。您可以在任何 Azure 區域中的任何伺服器上建立資料庫。異地還原會依賴 Azure 異地備援儲存體中的[自動資料庫備份](sql-database-automated-backups.md)，並從異地複寫的備份複本還原，因此可彈性地回應主要區域的儲存體中斷情況。



## 異地還原詳細資料

異地還原和還原時間點使用相同的技術，但有一個重要的差異。它會從異地複寫 blob 儲存體 (RA-GRS) 中最新的每日備份複本還原資料庫。對於每個作用中的資料庫，此服務會維護備份鏈結，其包含每週完整備份、多個每日差異備份以及每隔 5 分鐘儲存一次的交易記錄。這些 blob 都會進行異地複寫，這樣可保證即使在主要區域中發生大規模失敗之後，仍可使用每日備份。以下顯示複製到儲存體容器的每週和每日備份的「異地複寫」。

![異地還原](./media/sql-database-geo-restore/geo-restore-1.png)


如果區域中的大規模意外導致您無法使用資料庫應用程式，可以使用異地還原，將資料庫從最近的備份還原到任何其他區域中的伺服器。所有備份都會進行異地複寫，而且可以在採用備份與將其異地複寫至不同區域中的 Azure blob 之間具有延遲。此延遲可能長達一小時，因此當災害事件發生時，最長可能有 1 小時的資料遺失，亦即，最多 1 小時的 RPO。以下顯示從上次每日備份起的資料庫還原。


![異地還原](./media/sql-database-geo-restore/geo-restore-2.png)



## 異地還原的復原時間

許多因素都會影響復原時間，包括資料庫大小和資料庫的效能層級，以及目標區域中正在處理的並行還原要求數目。如果某區域中的中斷延長，其他區域可能會處理大量的異地還原要求。如果有大量的要求，可能會增加該區域中的資料庫復原時間。


## 摘要

異地還原可供所有服務層使用，而且它是 SQL Database 中最基本的災害復原解決方案，具備最長的 RPO 和預估復原時間 (ERT)。對於大小上限為 2 GB 的 Basic 資料庫，異地還原提供 ERT 為 12 小時的合理 DR 解決方案。對於較大的「標準」或「高階」資料庫，如果需要大幅縮短復原時間，或為了降低資料遺失的可能性，您應該考慮使用「作用中異地複寫」。「作用中異地複寫」提供明顯較低的 RPO 和 ERT，因為它只需要您起始對連續複寫次要資料庫的容錯移轉。如需詳細資料，請參閱[作用中的異地複寫](sql-database-geo-replication-overview.md)。

## 後續步驟

- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [使用 Azure 入口網站進行異地還原](sql-database-geo-restore-portal.md)
- [使用 PowerShell 進行異地還原](sql-database-geo-restore-powershell.md)

## 其他資源

- [SQL Database BCDR 常見問題集](sql-database-bcdr-faq.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [時間點還原](sql-database-point-in-time-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->