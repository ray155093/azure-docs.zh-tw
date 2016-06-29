<properties
   pageTitle="雲端商務持續性 - 內建備份 - SQL Database | Microsoft Azure"
   description="深入了解可讓您將 Azure SQL Database 回復到先前時間點，或將資料庫複製到地理區域中新資料庫 (最多 35 天) 的 SQL Database 內建備份。"
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
   ms.workload="sqldb-features"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# 概觀：SQL Database 自動備份

Azure SQL Database 服務會利用自動備份保護所有資料庫，此備份會保留 7 天 (適用於 Basic)、14 天 (適用於標準資料庫) 和 35 天 (適用於高階資料庫。您可以使用這些自動備份來執行時間點還原，以及在意外資料損毀或刪除後，還原已刪除的資料庫。

系統會自動採用資料庫備份，不需要與任何選用與額外的費用。這些自動備份和還原時間點提供零成本與免管理的方式，可避免資料庫遭到任何原因的意外損毀或刪除。

## 自動備份成本

Microsoft Azure SQL Database 提供的備份儲存體可高達 200% 的最大可佈建資料庫儲存體，且不須支付額外費用。例如，如果您擁有可佈建 DB 大小為 250GB 的標準 DB 執行個體，您將免費獲得 500GB 的備份儲存體。若您的資料庫超過所提供的備份儲存體，您可以連絡 Azure 支援部門，選擇縮短保留期限，或是以標準讀取存取地理備援儲存體 (RA-GRS) 費率，另購額外的備份儲存體。

## 自動備份詳細資料

所有基本、標準和高階資料庫都受到自動備份的保護。完整備份每週進行一次，差異備份每天進行一次、而記錄備份每隔 5 分鐘進行一次。建立資料庫之後，會立即排程第一次完整備份。這通常會在 30 分鐘內完成，但可能花更長的時間。如果資料庫已經很大，例如，如果它建立為資料庫複製的結果或從大型資料庫還原，則第一次完整備份可能需要更長的時間才能完成。第一次完整備份之後，會自動排程進一步的備份，並在背景中以無訊息方式管理。完整和差異備份的確切時間由系統決定，以平衡整體負載。備份檔案會儲存在異地備援儲存體帳戶，並具備讀取存取權 (RA-GRS)，以確保災害復原用途的可用性。

## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->