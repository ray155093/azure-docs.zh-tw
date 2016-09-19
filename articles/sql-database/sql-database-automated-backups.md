<properties
   pageTitle="雲端商務持續性 - 內建備份 - SQL Database | Microsoft Azure"
   description="了解可讓您將 Azure SQL Database 回復到先前時間點，或將資料庫複製到地理區域中新資料庫 (最多 35 天) 的 SQL Database 內建備份。"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# SQL Database 自動備份

Azure SQL Database 服務會利用自動備份保護所有資料庫，此備份會保留 7 天 (適用於基本資料庫)、35 天 (適用於標準資料庫) 和 35 天 (適用於高階資料庫。如需每個服務層可用功能的詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。

系統會自動採用資料庫備份，不需要與任何選用與額外的費用。這些自動備份和還原時間點提供零成本與免管理的方式，可避免資料庫遭到任何原因的意外損毀或刪除。您可以使用這些自動備份來執行時間點還原，以及在意外資料損毀或刪除後，還原已刪除的資料庫。

## 自動備份成本

Microsoft Azure SQL Database 提供的備份儲存體可高達 200% 的最大可佈建資料庫儲存體，且不須支付額外費用。例如，如果您擁有可佈建 DB 大小為 250GB 的標準 DB 執行個體，您將免費獲得 500GB 的備份儲存體。若您的資料庫超過所提供的備份儲存體，您可以連絡 Azure 支援部門，選擇縮短保留期限，或是以標準讀取存取地理備援儲存體 (RA-GRS) 費率，另購額外的備份儲存體。

## 自動備份詳細資料

所有基本、標準和高階資料庫都受到自動備份的保護。完整備份每週進行一次，差異資料庫備份每小時進行一次、而交易記錄備份每隔 5 分鐘進行一次。建立資料庫之後，會立即排程第一次完整備份。這通常會在 30 分鐘內完成，但可能花更長的時間。如果資料庫已經很大，例如，如果它建立為資料庫複製的結果或從大型資料庫還原，則第一次完整備份可能需要更長的時間才能完成。第一次完整備份之後，會自動排程進一步的備份，並在背景中以無訊息方式管理。完整和差異備份的確切時間由系統決定，以平衡整體負載。

## 異地備援

備份檔案會儲存在異地備援儲存體帳戶，並具備讀取存取權 (RA-GRS)，以確保災害復原用途的可用性。這可確保備份的檔案複寫到[配對的資料中心](../best-practices-availability-paired-regions.md)。下圖顯示每週與每日備份的異地複寫，這些備份會儲存在異地備援儲存體帳戶，並具備讀取存取權 (RA-GRS)，以確保災害復原用途的可用性。

![異地還原](./media/sql-database-geo-restore/geo-restore-1.png)

## 使用自動備份

您可以在[保留期限](sql-database-service-tiers.md)內[從自動備份還原資料庫](sql-database-recovery-using-backups.md)至：

- 同一部邏輯伺服器上的新資料庫，復原到保留期限內指定的時間點。
- 同一部邏輯伺服器上的資料庫，復原到已刪除資料庫的刪除時間。
- 任何區域中任意邏輯伺服器上的新資料庫，復原到異地複寫 Blob 儲存體 (RA-GRS) 中最新的每日備份。

您也可以使用 [SQL Database 自動備份](sql-database-automated-backups.md)，在任何區域中任意邏輯伺服器上建立[資料庫複本](sql-database-copy.md)，其交易會與目前的 SQL Database 一致。您可以使用資料庫複本並[匯出到 BACPAC](sql-database-export.md)，以針對超過您保存期限的長期儲存體封存資料庫中交易一致的複本，或者將資料庫複本傳送到 SQL Server 的內部部署或 Azure VM 執行個體。

## 當我依服務層降級/升級時，我的還原點保留期限會有什麼改變？

降級為較低的效能層之後，還原點的保留期限會立即縮短為目前資料庫效能層的保留期限。如果升級服務層，只有在升級資料庫之後，保留期限才會開始延長。例如，如果資料庫降級為「基本」，保留期限會立即從 35 天變更為 7 天，而且將無法再使用 35 天以前的所有還原點。之後，如果重新升級為「標準」或「進階」，保留期限一開始會是 7 天，再慢慢延長為 35 天。

## 已卸除資料庫的保留期限有多久？ 
保留期限取決於資料庫所在的服務層，或是資料庫存在的天數，取兩者中較少的天數。

> [AZURE.IMPORTANT] 如果您刪除 Azure SQL Database 伺服器執行個體，其所有資料庫也會一併刪除且無法加以復原。目前不支援還原已刪除的伺服器。

## 後續步驟

- 若要了解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
- 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-geo-replication-overview.md)
- 若要了解如何使用自動備份進行封存，請參閱[資料庫複製](sql-database-copy.md)
- 如需商務持續性概觀，請參閱[商務持續性概觀](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0907_2016-->