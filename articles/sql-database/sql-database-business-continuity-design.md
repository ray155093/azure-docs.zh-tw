<properties 
   pageTitle="SQL Database 的業務續航力設計" 
   description="選擇指引：本節提供如何選擇應使用之 BCDR 功能及何時使用的指引。其中包括使用 SQL DB 會自動取得之功能的說明。"
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
   ms.date="05/27/2016"
   ms.author="carlrab"/>

# 業務續航力的設計

設計應用程式的業務續航力之前，您必須先回答下列問題：

1. 哪項業務續航力功能適合防止我的應用程式中斷？
2. 我使用哪個層級的備援和複寫拓樸？

如需使用彈性集區時的詳細復原策略，請參閱[使用 SQL Database 彈性集區之應用程式的災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

## 何時使用異地還原

[異地還原](sql-database-geo-restore.md)會在資料庫因裝載區域中發生事件而無法使用時，提供預設的復原選項。SQL Database 預設會為每個資料庫提供內建的基本保護功能。其作法是執行[資料庫備份](sql-database-automated-backups.md)並將備份儲存在 Azure 異地備援儲存體 (GRS) 中。如果您選擇這個方法，則不需要進行任何特殊組態或其他資源配置。透過這些備份，您可以在任何區域使用異地還原命令來復原資料庫。如需如何使用異地還原來復原應用程式的詳細資訊，請參閱[從中斷復原](sql-database-disaster-recovery.md)一節。

如果您的應用程式符合下列準則，則應該使用內建保護功能：

1. 非關鍵性應用程式。沒有繫結 SLA，因此停機 24 小時或更長的時間不會衍生財務責任。
2. 資料變更率很低 (例如每小時才有幾筆交易)。RPO 為 1 小時，因此不會導致大量資料遺失。
3. 應用程式對成本很敏感，因此無法採用需要額外成本的異地複寫 

> [AZURE.NOTE] 異地還原不會在任何特定區域預先配置計算容量，以在中斷期間從備份還原作用中的資料庫。這項服務管理與異地還原要求相關之工作負載的方式，是將對該區域中現有資料庫的影響降到最低，並會優先考慮資料庫的容量需求。因此，資料庫的復原時間取決於當時相同區域中要復原的其他資料庫數目。

## 何時使用作用中異地複寫

[作用中異地複寫](sql-database-geo-replication-overview.md)可讓您在與主要資料庫不同的區域中建立可讀取的 (次要) 資料庫。它會確保資料庫具有可在復原後支援應用程式工作負載的必要資料和計算資源。請參閱[從中斷復原](sql-database-disaster-recovery.md)一節，以使用容錯移轉來復原您的應用程式。

如果您的應用程式符合下列準則，則應該使用「異地複寫」：

1. 是關鍵性應用程式。具有主動式 RPO 和 RTO 的繫結 SLA。遺失資料和可用性會衍生財務責任。 
2. 資料變更率很高 (例如每分鐘或每秒便有幾筆交易)。與預設保護相關的 RPO 為 1 小時，因此可能會導致無法接受的資料遺失。
3. 與潛在的財務責任和相關企業損失相較下，使用異地複寫的相關成本明顯較低。

若要啟用「作用中異地複寫」，請參閱[為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-portal.md)

> [AZURE.NOTE] 主動式異地複寫也支援對次要資料庫的唯讀存取，因此可提供唯讀工作負載額外的容量。

##如何選擇容錯移轉組態 

設計應用程式的業務續航力時，您應該考慮幾個組態選項。您的選擇會視應用程式部署拓撲，以及應用程式的哪些部分最容易受到中斷的影響而定。如需指引，請參閱[使用異地複寫設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## 後續步驟

- [使用異地複寫來設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

## 其他資源

- [使用 SQL Database 彈性集區之應用程式的災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) 

<!---HONumber=AcomDC_0615_2016-->