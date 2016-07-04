<properties
   pageTitle="雲端商務持續性 - 資料庫復原 - SQL Database | Microsoft Azure"
   description="了解 Azure SQL Database 如何支援雲端商務持續性和資料庫復原，以及如何協助維持任務關鍵性雲端應用程式持續執行。"
   keywords="business continuity,cloud business continuity,database disaster recovery,database recovery,商務持續性,雲端商務持續性,資料庫災害復原,資料庫復原"
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
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="carlrab"/>

# 概觀：Azure SQL Database 的雲端商務持續性和資料庫災害復原

> [AZURE.SELECTOR]
- [還原時間點](sql-database-point-in-time-restore.md)
- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)
- [異地還原](sql-database-geo-restore.md)
- [主動式異地複寫](sql-database-geo-replication-overview.md)
- [商務持續性案例](sql-database-business-continuity-scenarios.md)

Azure SQL Database 提供數個商務持續性解決方案。商務持續性是指設計、部署及執行應用程式的方式，必須能夠彈性地回應規劃或未規劃的中斷事件，這些中斷事件會導致應用程式永久或暫時無法執行其商務功能。未規劃的事件範圍從人為疏失，到為了回應區域性嚴重損壞情況的永久或暫時中斷，這些區域性嚴重損壞情況可能會在特定 Azure 區域造成大規模功能遺失。規劃的事件包括應用程式重新部署至不同的區域和應用程式升級。商務持續性的目標是要讓您的應用程式在這些事件期間繼續運作，並將對商務功能的影響降到最低。

您必須先熟悉幾個概念，再開始討論 SQL Database 雲端商務持續性解決方案。它們是：

* **災害復原 (DR)**：還原應用程式之一般商務功能的程序

* **預估復原時間 (ERT)**：資料庫在收到還原或容錯移轉要求之後到完全可用的預估持續時間。

* **復原時間目標 (RTO)**：應用程式在發生中斷事件之後到完全復原之前可接受的時間上限。RTO 測量失敗期間無法使用的時間上限。

* **復原點目標 (RPO)**：應用程式在發生中斷事件之後到完全復原時可遺失的上次更新 (時間間隔) 的最大數量。RPO 測量失敗期間可遺失的資料量上限。


## SQL Database 雲端商務持續性案例

以下為規劃商務持續性和資料庫復原時需考量的重要案例：

### 針對商務持續性設計應用程式

我想要建置的應用程式對業務很重要。我想要將應用程式設計並設定成可安然度過服務因災難性失敗所致的區域性嚴重損壞情況。我知道應用程式的 RPO 和 RTO 需求，並將選擇符合這些需求的組態。

### 從人為疏失復原

我具有存取應用程式之生產環境版本的管理權限。我在定期維護過程中錯誤刪除了生產環境中的一些重要資料。我想要快速還原資料，以減輕這個錯誤的影響。

### 從中斷復原

我在生產環境中執行應用程式，並收到警示，指出在部署應用程式的區域發生重大中斷情況。我想要起始復原程序，將應用程式復原至其他區域，以減輕對業務的影響。

### 執行災害復原演練

由於從中斷復原會將應用程式的資料層重新配置到不同的區域，因此我想要定期測試復原程序，並評估該程序對應用程式的影響，以做足準備。

### 應用程式升級而不需要停機

我想要發行應用程式的重大升級。其中包含資料庫結構描述變更、部署其他預存程序等。這個程序需要防止使用者存取資料庫。同時，我想要確保該升級不會對營運造成明顯的中斷。

## SQL Database 商務持續性功能

下表列出 SQL Database 商務持續性功能，並顯示不同[服務層](sql-database-service-tiers.md)的差異：

| 功能 | 基本層 | 標準層 |高階層
| --- |--- | --- | ---
| 還原時間點 | 7 天內的任何還原點 | 14 天內的任何還原點 | 35 天內的任何還原點
| 異地還原 | ERT < 12 小時，RPO < 1 小時 | ERT < 12 小時，RPO < 1 小時 | ERT < 12 小時，RPO < 1 小時
| 主動式異地複寫 | ERT < 30 秒，RPO < 5 秒 | ERT < 30 秒，RPO < 5 秒 | ERT < 30 秒，RPO < 5 秒

這些功能是為了解決稍早所列案例而提供。如需如何選取特定功能的指引，請參閱[商務持續性的設計](sql-database-business-continuity-design.md)一節。

> [AZURE.NOTE] ERT 和 RPO 的值是設計出來的目標，僅供指引之用。它們並非 [SQL Database SLA](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/) 的一部分。


###還原時間點

[還原時間點](sql-database-point-in-time-restore.md)的設計是為了將您的資料庫還原到較早的時間點。它使用服務為每個使用者資料庫自動維護的資料庫備份、增量備份和交易記錄備份。這項功能適用於所有服務層。基本層可還原 7 天，標準層可還原 14 天，而高階層可還原 35 天。

### 異地還原

[異地還原](sql-database-geo-restore.md)也適用於基本、標準和進階資料庫。它會在資料庫因裝載區域中的事件而無法使用時，提供預設復原選項。異地還原與時間點還原類似，需要使用 Azure 異地備援儲存體中的資料庫備份。它會從異地複寫的備份複本還原，因此可彈性地回應主要區域的儲存體中斷情況。

### 主動式異地複寫

[作用中異地複寫](sql-database-geo-replication-overview.md)適用於所有資料庫層。其設計是針對比異地還原需要更主動復原的應用程式。透過主動式異地複寫，您可以在不同區域的伺服器上最多建立四個可讀取的次要資料庫。您可以起始容錯移轉至任何次要資料庫。此外，作用中異地複寫可用來支援[應用程式升級或重新配置案例](sql-database-manage-application-rolling-upgrade.md)，以及對唯讀工作負載進行負載平衡。

## 在商務持續性功能之間進行選擇

設計應用程式的商務持續性之前，您必須先回答下列問題：

1. 哪項商務持續性功能適合防止我的應用程式中斷？
2. 我使用哪個層級的備援和複寫拓樸？

如需使用彈性集區時的詳細復原策略，請參閱[使用 SQL Database 彈性集區之應用程式的災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

### 何時使用異地還原

[異地還原](sql-database-geo-restore.md)會在資料庫因裝載區域中發生事件而無法使用時，提供預設的復原選項。SQL Database 預設會為每個資料庫提供內建的基本保護功能。其做法是執行[資料庫備份](sql-database-automated-backups.md)並將備份儲存在異地備援 Azure 儲存體 (GRS) 中。如果您選擇這個方法，則不需要進行任何特殊組態或其他資源配置。您可以從這些自動化的異地備援備份還原到新的資料庫，藉以將您的資料庫還原到任何區域。

如果您的應用程式符合下列準則，則應該使用內建保護功能：

1. 非關鍵性應用程式。沒有繫結 SLA，因此停機 24 小時或更長的時間不會衍生財務責任。
2. 資料變更率很低 (例如每小時才有幾筆交易)。RPO 為 1 小時，因此不會導致大量資料遺失。
3. 應用程式對成本很敏感，因此無法採用需要額外成本的異地複寫 

> [AZURE.NOTE] 異地還原不會在任何特定區域預先配置計算容量，以在中斷期間從備份還原作用中的資料庫。這項服務管理與異地還原要求相關之工作負載的方式，是將對該區域中現有資料庫的影響降到最低，並會優先考慮資料庫的容量需求。因此，資料庫的復原時間取決於當時相同區域中要復原的其他資料庫數目，以及 DB的大小、交易記錄的數目、網路頻寬等。

### 何時使用作用中異地複寫

[作用中異地複寫](sql-database-geo-replication-overview.md)能夠在不同區域中，從您的主要資料庫建立和維護可讀取的 (次要) 資料庫，使用非同步複寫機制讓它們保持為目前使用狀態。它會確保資料庫具有可在復原後支援應用程式工作負載的必要資料和計算資源。

如果您的應用程式符合下列準則，則應使用作用中異地複寫：

1. 是關鍵性應用程式。具有主動式 RPO 和 RTO 的繫結 SLA。遺失資料和可用性會衍生財務責任。 
2. 資料變更率很高 (例如每分鐘或每秒便有幾筆交易)。與預設保護相關的 RPO 為 1 小時，因此可能會導致無法接受的資料遺失。
3. 與潛在的財務責任和相關企業損失相較下，使用異地複寫的相關成本明顯較低。


## 後續步驟

- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要了解如何使用 Azure SQL Database 自動備份以執行資料庫的時間點還原，請參閱[還原時間點](sql-database-point-in-time-restore.md)
- 若要了解如何使用 Azure SQL Database 自動備份以還原已刪除的資料庫，請參閱[還原已刪除的資料庫](sql-database-restore-deleted-database.md)
- 若要了解如何使用 Azure SQL Database 自動備份以執行資料庫的異地還原，請參閱[異地還原](sql-database-geo-restore.md)
- 若要了解如何針對商務持續性設定和使用作用中異地複寫，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)

## 其他資源

- [從中斷復原](sql-database-disaster-recovery.md)
- [從使用者錯誤復原](sql-database-user-error-recovery.md)
- [執行災害復原演練](sql-database-disaster-recovery-drills.md)
- [管理災害復原後的安全性](sql-database-geo-replication-security-config.md)
- [使用 SQL Database 作用中異地複寫管理雲端應用程式的輪流升級](sql-database-manage-application-rolling-upgrade.md)
- [設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [使用 SQL Database 彈性集區之應用程式的災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [使用異地複寫來設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->