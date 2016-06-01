<properties
   pageTitle="Azure SQL 資料倉儲中的資料庫還原 (概觀) | Microsoft Azure"
   description="復原 Azure SQL 資料倉儲中資料庫之資料庫還原選項的概觀。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Azure SQL 資料倉儲中的資料庫還原 (概觀)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-manage-database-restore.md)
- [入口網站](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

描述在 Azure SQL 資料倉儲中還原資料庫的選項。這包括還原即時資料庫、已刪除的資料庫及無法存取的資料庫。即時和已刪除的資料庫可以透過來自相同資料中心的快照進行還原。無法存取的資料庫可能是因為服務中斷而離線。在這種狀況下，將會從位於不同地理位置的資料中心進行還原。


## 復原案例

**從基礎結構失敗中復原：**此案例是指從基礎結構問題中復原，例如磁碟故障等。客戶想要有容錯和高可用性基礎結構來確保業務持續性。

**從使用者錯誤中復原：**此案例是指從意外或偶發的資料損毀或刪除中復原。如果使用者不小心或偶然修改或刪除資料，客戶會想要將資料庫還原到較早的時間點，以確保業務持續性。

**從災害中復原 (DR)：**此案例是指從重大災難中復原。在天然災害或區域性停電等干擾性事件造成資料庫變成無法使用的情況下，客戶會想要復原不同區域的資料庫，讓業務得以持續運作。

## 快照原則

[AZURE.INCLUDE [SQL 資料倉儲備份保留原則](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## 資料庫還原功能

讓我們看看 SQL 資料倉儲如何增強資料庫的可靠性，並於上述案例中支援復原和持續運作。


### 資料備援

SQL 資料倉儲會將所有資料儲存在異地備援 Azure 儲存體 (RA-GRS) 中。異地備援儲存體會將資料複寫到與主要區域距離數百英哩的次要區域。在主要和次要區域中，您的資料都會複寫三次，且跨越不同的容錯網域和升級網域。即使是全面的區域性停電或災害，造成其中一個區域無法使用，這仍可確保資料的持久性。若要深入了解讀取存取異地備援儲存體，請參閱 [Azure 儲存體備援選項][]。

### 資料庫還原

資料庫還原的設計是為了將您的資料庫還原到較早的時間點。Azure SQL 資料倉儲服務至少每 8 小時會以自動儲存體快照集來保護所有資料庫，並保留 7 天，為您提供一組獨立的還原點。這些快照會儲存在 RA-GRS Azure 儲存體中，因此會預設為異地備援。自動快照與還原功能提供免管理的方式，以避免資料庫遭到意外損毀或刪除。若要深入了解資料庫還原，請參閱[資料庫還原工作][]。

### 異地還原

異地還原的設計是為了在資料庫因為干擾性事件而變成無法使用時復原資料庫。因為備份是異地備援，即使因為停電而無法存取資料庫，也能用來復原資料庫。您可以在任何 Azure 區域中的任何伺服器上建立還原的資料庫。除了從中斷復原之外，異地還原還可以用於其他案例，例如，將資料庫移轉或複製到不同的伺服器或區域。

**何時起始復原** 復原作業需要在復原時變更 SQL 連接字串，並且可能會導致永久的資料遺失。因此，只有在中斷情況可能持續超過應用程式的 RTO 時，才應該執行這項作業。利用下列資料點判斷是否需要復原︰

- 資料庫的永久性連線失敗。
- 您的 Azure 入口網站顯示有關區域中影響廣泛之事件的警示。


## 後續步驟
如需其他重要管理工作的詳細資訊，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[Azure 儲存體備援選項]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[管理概觀]: sql-data-warehouse-overview-management.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->