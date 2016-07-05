<properties 
   pageTitle="SQL Database 使用者錯誤復原 | Microsoft Azure" 
   description="了解如何使用 Azure SQL Database 的還原時間點 (PITR) 功能，從使用者錯誤、意外的資料損毀或已刪除的資料庫復原。" 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# 從使用者錯誤復原 Azure SQL Database

Azure SQL Database 提供兩個核心功能，從使用者錯誤或非預期的資料修改復原。

- [還原時間點](sql-database-point-in-time-restore.md) 
- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)

Azure SQL Database 一律會還原至新的資料庫。所有「基本」、「標準」和「高階」資料庫都提供這些還原功能。

##還原時間點

發生使用者錯誤或非預期的資料修改時，還原時間點可用來將資料庫還原至資料庫保留期限內的任何時間點。

「基本」資料庫有 7 天的保留期，「標準」資料庫有 14 天的「保留期」，「高階」資料庫則有 35 天的保留期。若要深入了解資料庫備份保留，請參閱[自動備份](sql-database-automated-backups.md)。

若要執行還原時間點，請參閱：

- [使用 Azure 入口網站的還原時間點](sql-database-point-in-time-restore-portal.md)
- [使用 PowerShell 的還原時間點](sql-database-point-in-time-restore-powershell.md)
- [使用 REST API 的還原時間點 (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 


## 還原已刪除的資料庫

假如資料庫已遭刪除，Azure SQL Database 可讓您將已刪除的資料庫還原至刪除的時間點。Azure SQL Database 會將已刪除的資料庫備份，儲存一段資料庫保留期間的時間。

已刪除資料庫的保留期間取決於資料庫所在的服務層，或是資料庫存在的天數，兩者中較少的天數。若要深入了解資料庫保留，請參閱[自動備份](sql-database-automated-backups.md)。

還原已刪除的資料庫：

- [使用 Azure 入口網站還原已刪除的資料庫](sql-database-restore-deleted-database-portal.md)
- [使用 PowerShell 還原已刪除的資料庫](sql-database-restore-deleted-database-powershell.md)
- [使用 REST API 還原已刪除的資料庫 (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## 後續步驟

- 如需針對災害復原使用和設定作用中異地複寫的相關資訊，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)
- 如需針對災害復原使用異地還原的相關資訊，請參閱[異地還原](sql-database-geo-restore.md)

## 其他資源

- [SQL Database 商務持續性和災害復原](sql-database-business-continuity.md)
- [還原時間點](sql-database-point-in-time-restore.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [異地複寫的安全性設定](sql-database-geo-replication-security-config.md)
- [SQL Database BCDR 常見問題集](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->