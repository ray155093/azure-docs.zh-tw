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
   ms.date="06/02/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Azure SQL 資料倉儲中的資料庫還原 (概觀)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-manage-database-restore.md)
- [入口網站](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

本文說明可用來還原「Azure SQL 資料倉儲」中資料庫的選項。這些包括還原即時資料倉儲和已刪除的資料倉儲。即時和已刪除的資料倉儲會從自所有資料倉儲建立的自動快照還原。

## 復原案例

**從基礎結構失敗中復原：**此案例是指從基礎結構問題中復原，例如磁碟故障等。客戶想要有容錯和高可用性基礎結構來確保業務持續性。

**從使用者錯誤中復原：**此案例是指從意外或偶發的資料損毀或刪除中復原。如果使用者不小心或偶然修改或刪除資料，客戶會想要將資料庫還原到較早的時間點，以確保業務持續性。

## 快照原則

[AZURE.INCLUDE [SQL 資料倉儲備份保留原則](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## 資料庫還原功能

讓我們看看 SQL 資料倉儲如何增強資料庫的可靠性，並於上述案例中支援復原和持續運作。


### 資料備援

「SQL 資料倉儲」會將所有資料儲存在[本機備援 (LRS)](../storage/storage-redundancy.md)「Azure 進階儲存體」上，其中會保存 3 份資料複本。

### 資料庫還原

資料庫還原的設計是為了將您的資料庫還原到較早的時間點。Azure SQL 資料倉儲服務至少每 8 小時會以自動儲存體快照集來保護所有資料庫，並保留 7 天，為您提供一組獨立的還原點。自動快照與還原功能提供免管理的方式，以避免資料庫遭到意外損毀或刪除。若要深入了解資料庫還原，請參閱[資料庫還原工作][]。

## 後續步驟
如需其他重要管理工作的詳細資訊，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[Azure storage redundancy options]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[管理概觀]: sql-data-warehouse-overview-management.md
[資料庫還原工作]: sql-data-warehouse-manage-database-restore-portal.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->