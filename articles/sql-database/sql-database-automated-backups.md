<properties
   pageTitle="SQL Database 備份 | Microsoft Azure"
   description="深入了解可讓您將 Azure SQL Database 回復到先前時間點，或將資料庫複製到地理區域中新資料庫 (最多 35 天) 的 SQL Database 內建資料庫備份。"
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
   ms.date="09/26/2016"
   ms.author="carlrab"/>

# SQL Database 備份

SQL Database 會在服務供應過程中自動建立資料庫備份，不需您介入處理，也沒有額外成本。使用資料庫備份將資料庫還原至先前的時間點。本文說明 SQL Database 中的資料庫備份功能的詳細資訊。

## 什麼是資料庫備份？  

資料庫備份是一個檔案，其中儲存在特定時間點之下資料庫狀態的相關資訊。SQL Database 會建立[完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差異](https://msdn.microsoft.com/library/ms175526.aspx)和[交易記錄檔](https://msdn.microsoft.com/library/ms191429.aspx)備份。當您將資料庫還原到某個時間點時，此服務會判斷需要還原的備份。

資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

## 異地備援儲存體

此 SQL Database 服務會將資料庫備份檔案儲存在具有讀取權限的異地備援儲存體帳戶中 (RA-GRS)。Azure 儲存體 RA-GRS 功能會將備份檔案複寫到[配對的資料中心](../best-practices-availability-paired-regions.md)。萬一您無法從主要資料庫區域存取資料庫備份，此異地複寫可確保您能夠還原資料庫。在下列範例中，SQL Database 會在美國東部地區建立資料庫備份，並將它們儲存在 RA-GRS 帳戶中。然後，Azure 儲存體異地複寫會將備份複寫到美國西部區域中配對的資料中心。

![異地還原](./media/sql-database-geo-restore/geo-restore-1.png)

>[AZURE.NOTE] 在 Azure 儲存體中，「複寫」指的是將檔案從一個位置複製到另一個位置。SQL 的「資料庫複寫」指的是保持多個次要資料庫與主要資料庫同步。

若要深入了解：
- 異地備援儲存體，請參閱 [Azure 儲存體複寫](../storage/storage-redundancy.md)。
- RA-GRS 儲存體：請參閱[讀取權限異地備援儲存體](../storage/storage-redundancy.md#read-access-geo-redundant-storage)。

## 資料庫備份的成本

Microsoft Azure SQL Database 提供高達 200% 的最大可佈建資料庫儲存體作為備份儲存體，且不須支付額外費用。例如，如果您擁有可佈建 DB 大小為 250 GB 的標準 DB 執行個體，您就能免費獲得 500 GB 的備份儲存體。如果您的資料庫超過所提供的備份儲存體，您可以連絡 Azure 支援人員來選擇縮短保留期限。另一個選項是付費購買額外的備份儲存體，按讀取權限異地備援儲存體 (RA-GRS) 標準費率計費。

## 資料庫備份排程

所有基本、標準和高階資料庫都受到自動備份的保護。完整備份每週進行一次，差異資料庫備份每小時進行一次、而交易記錄備份每隔五分鐘進行一次。建立資料庫之後，會立即排程第一次完整備份。通常會在 30 分鐘內完成，但如果資料庫很大，則時間可能更久。比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。完整和[差異](https://msdn.microsoft.com/library/ms175526.aspx)資料庫備份的確切時間，依整體系統工作負載維持平衡而決定。

## 資料庫備份的保留期限

每個 SQL Database 備份的保留天數：「基本」7 天、「標準」35 天、「進階」35 天。如需每個服務層可用功能的詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。

### 當我依服務層降級/升級時，我的還原點保留期限會有什麼改變？

降級為較低的效能層之後，還原點的保留期限會立即縮短為目前資料庫效能層的保留期限。如果升級服務層，只有在升級資料庫之後，保留期限才會開始延長。例如，如果資料庫降級為「基本」，保留期限會從 35 天變成 7 天。超過七天所有還原點將立即無法使用。當您將資料庫升級到「標準」或「進階」時，保留期限會從 7 天開始延長，最多到 35 天。

### 已卸除資料庫的保留期限有多久？ 

保留期限取決於資料庫所在的服務層，或是資料庫存在的天數，取兩者中較少的天數。

> [AZURE.IMPORTANT] 如果您刪除 Azure SQL Database 伺服器執行個體，所有屬於該執行個體的資料庫也會一併刪除，且無法復原。您無法還原已刪除的伺服器。


## 資料庫備份的常見用途

資料庫備份的主要用途是能夠將資料庫還原到保留期限內的某個時間點。資料庫備份可讓您將資料庫還原到某個時間點、將已刪除的資料庫還原到它被刪除時的狀態，或將資料庫還原至另一個地理區域。

- 若要深入了解資料庫還原，請參閱[從自動備份還原資料庫](sql-database-recovery-using-backups.md)。

您可以使用資料庫備份將資料庫複製到任何地理區域中的邏輯 SQL 伺服器。複本與目前的 SQL 資料庫會維持交易一致性。

- 若要深入了解複製資料庫，請參閱[資料庫複製](sql-database-copy.md)。

您也可以建立資料庫複本，再[匯出至 BACPAC](sql-database-export.md) 檔案，就能將自動備份保存到超過保留期限。產生 BACPAC 檔案之後，您可以將它保存到長期儲存體，就能保存到超過保留期限。或者，使用 BACPAC 將資料庫的複本傳送到內部部署或 Azure 虛擬機器 (VM) 中的 SQL Server。

- 若要深入了解保存資料庫備份，請參閱[資料庫複製](sql-database-copy.md)。


## 相關主題

### 案例

- 如需商務持續性概觀，請參閱[商務持續性概觀](sql-database-business-continuity.md)

### 特性

若要深入了解︰

- 還原資料庫備份，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
- 保存資料庫備份，請參閱[資料庫複製](sql-database-copy.md)。
- 快速復原選項，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)。

<!-- ### Tasks -->

<!-- ### Tutorials -->

<!---HONumber=AcomDC_0928_2016-->