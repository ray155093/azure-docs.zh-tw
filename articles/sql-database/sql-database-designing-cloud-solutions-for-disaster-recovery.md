---
title: "災害復原應用程式設計 - Azure SQL Database | Microsoft Docs"
description: "了解使用異地複寫針對 Azure SQL Database 災害復原的應用程式設計。"
keywords: "cloud disaster recovery,disaster recovery solutions,app data backup,geo-replication,business continuity planning,雲端災害復原,災害復原方案,應用程式資料備份,異地複寫,商務持續性計劃"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: e8a346ac-dd08-41e7-9685-46cebca04582
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 07/20/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 7e607debe47efb6a22ca6fa47a40554d13d29359
ms.openlocfilehash: dd56a8d1ee428b1845ed80f0b899cc73c2c4b7f6


---
# <a name="application-design-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>使用 SQL Database 的主動式異地複寫針對雲端災害復原的應用程式設計
> [!NOTE]
> [作用中異地複寫](sql-database-geo-replication-overview.md)現在可供所有層中的所有資料庫使用。
>
>

了解如何使用 SQL Database 中的 [主動式異地複寫](sql-database-geo-replication-overview.md) ，來設計能夠從區域失敗和嚴重中斷復原的資料庫應用程式。 對於商務持續性規劃，請考量應用程式部署拓撲、您要達到的服務等級協定、流量延遲及成本。 在本文中，我們將探討常見的應用程式模式，並討論每個選項的優缺點。 如需主動式異地複寫與彈性集區的相關資訊，請參閱 [彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>設計模式 1：雲端災害復原的主動-被動部署，使用共置資料庫
這個選項最適合具有下列特性的應用程式：

* 單一 Azure 區域中的作用中執行個體
* 強烈依賴資料的讀寫 (RW) 存取
* 考量到延遲和流量成本，無法接受應用程式邏輯和資料庫之間的跨區域連接    

在此情況下，當所有應用程式元件都受影響，而需要當成一個單位進行容錯移轉時，應用程式部署拓撲最適合用來處理區域性災害。 在地理備援方面，會將應用程式邏輯和資料庫複寫到另一個區域，但在正常情況下不會使用它們來處理應用程式工作負載。 次要地區中的應用程式應該設定為使用次要資料庫的 SQL 連接字串。 流量管理員設為使用 [容錯移轉路由方法](../traffic-manager/traffic-manager-configure-failover-routing-method.md)。  

> [!NOTE]
> 這整篇文章使用 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)僅供說明用途。 您可以使用任何支援容錯移轉路由方法的負載平衡方案。    
>
>

除了主要應用程式執行個體，您還應該考慮部署小型[背景工作角色應用程式](../cloud-services/cloud-services-choose-me.md#tellmecs)，以定期發出 T-SQL 唯讀 (RO) 命令來監視主要資料庫。 您可以利用它來自動觸發容錯移轉、在應用程式的系統管理員主控台產生警示，或兩種功能都執行。 為了確保區域性運作中斷不會影響監視，您應該將監視應用程式執行個體部署至每個區域，並將每個執行個體連線到主要區域中的主要資料庫以及當地區域中的次要資料庫。

> [!NOTE]
> 這兩個監視應用程式都應該為作用中，且探查主要和次要資料庫。 後者可用來偵測次要地區中的失敗，並於應用程式未受保護時發出警示。     
>
>

下圖顯示此組態在運作中斷之前的情形。

![SQL Database「異地複寫」組態。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

當主要區域發生運作中斷之後，監視應用程式會偵測到主要資料庫無法存取，並發出警示。 根據您的應用程式 SLA，您可以決定應該要在連續發生多少個監視探查失敗後，才宣告資料庫運作中斷。 為了達成應用程式端點和資料庫的協調式容錯移轉，您應該讓監視應用程式執行下列步驟：

1. [更新主要端點的狀態](https://msdn.microsoft.com/library/hh758250.aspx) 以觸發端點容錯移轉。
2. 呼叫次要資料庫以 [起始資料庫容錯移轉](sql-database-geo-replication-portal.md)。

容錯移轉後，應用程式會處理次要地區中的使用者要求，但仍與資料庫共置，因為主要資料庫現在位於次要地區。 下圖說明此案例。 在所有圖表中，實線表示作用中連線、虛線表示已暫停的連線，停止標誌表示動作觸發。

![異地複寫：容錯移轉至次要資料庫。 應用程式資料備份。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

如果次要地區發生運作中斷，主要與次要資料庫之間的複寫連結會暫停，而監視應用程式會發出警示，指出主要資料庫已曝露。 應用程式的效能不會受到影響，但應用程式會在曝露的情況下運作，因此，萬一這兩個區域連續失敗，將帶來較高的風險。

> [!NOTE]
> 我們只建議使用具有單一 DR 區域的部署組態。 這是因為大部分 Azure 地理位置有兩個區域。 這些組態無法保護應用程式免於遭受兩個區域同時發生的重大失敗。 在這種罕見的失敗情況下，您可以使用[異地復原作業](sql-database-disaster-recovery.md#recover-using-geo-restore)，在第三個區域復原資料庫。
>
>

一旦運作中斷情況趨緩，次要資料庫會自動與主要資料庫同步處理。 在同步處理期間，視需要同步處理的資料量而定，主要資料庫的效能可能會稍微受到影響。 下圖說明次要地區發生運作中斷的情形。

![次要資料庫會與主要資料庫進行同步處理。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

此設計模式的主要 **優點** 包括：

* SQL 連接字串是在每個區域中部署應用程式期間設定，而且在容錯移轉之後不會變更。
* 應用程式的效能不會受到容錯移轉所影響，因為應用程式和資料庫永遠共置。

主要 **缺點** 是次要地區中的備援應用程式執行個體只用於災害復原。

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>設計模式 2：應用程式負載平衡的主動-主動部署
此雲端災害復原選項最適合具有下列特性的應用程式：

* 資料庫的讀取與寫入比率很高
* 資料庫寫入延遲不會影響使用者體驗  
* 使用不同的連接字串可以隔開唯讀邏輯和讀寫邏輯
* 唯讀邏輯不依賴資料與最新更新完全同步  

如果您的應用程式具有這些特性，只要將使用者連線的負載分散於不同區域中的多個應用程式執行個體，即可改善效能和使用者體驗。 為了實作負載平衡，每個區域都應該有應用程式的作用中執行個體，而且讀寫 (RW) 邏輯要連接到主要區域中的主要資料庫。 唯讀 (RO) 邏輯應該連接到應用程式執行個體所在的相同區域中的次要資料庫。 流量管理員應該設定為使用[循環配置資源路由](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md)或[效能路由](../traffic-manager/traffic-manager-configure-performance-routing-method.md)，而且每個應用程式執行個體都啟用[端點監視](../traffic-manager/traffic-manager-monitoring.md)。

如同模式 #1，您應該考慮部署類似的監視應用程式。 但有別於模式 #1，監視應用程式將不負責觸發端點容錯移轉。

> [!NOTE]
> 雖然此模式使用一個以上的次要資料庫，但其中只會有一個次要資料庫用於容錯移轉，原因如稍早所述。 由於這個模式需要次要資料庫的唯讀存取權，因此需要主動式異地複寫。
>
>

流量管理員應該設定為效能路由，將使用者連接導向最靠近使用者地理位置的應用程式執行個體。 下圖說明此組態在運作中斷之前的情形。
![無中斷：將效能轉送至最接近的應用程式。 異地複寫。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

如果主要區域中偵測到資料庫運作中斷，您應該開始將主要資料庫容錯移轉到其中一個次要地區，以變更主要資料庫的位置。 流量管理員會自動從路由表中排除離線端點，但會繼續將使用者流量路由傳送至剩餘的線上執行個體。 因為主要資料庫現在位於不同的區域，所有線上執行個體都必須變更其讀寫 SQL 連接字串，以連接到新的主要資料庫。 您必須在起始資料庫容錯移轉之前進行這項變更。 唯讀 SQL 連接字串應該保持不變，因為它們永遠指向相同區域中的資料庫。 容錯移轉步驟如下：  

1. 變更讀寫 SQL 連接字串來指向新的主要資料庫。
2. 呼叫指定的次要資料庫以 [起始資料庫容錯移轉](sql-database-geo-replication-portal.md)。

下圖說明容錯移轉之後的新組態。
![容錯移轉之後的組態。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

如果其中一個次要地區發生運作中斷，流量管理員就會自動從路由表中移除該區域中的離線端點。 對於該區域中次要資料庫的複寫通道即會暫停。 因為在此案例中，剩餘的區域會收到更多使用者流量，所以中斷期間可能會影響應用程式的效能。 一旦運作中斷情況趨緩，受影響區域中的次要資料庫就會立即與主要資料庫進行同步處理。 在同步處理期間，主要資料庫的效能可能稍微受到影響，視需要同步處理的資料量而定。 下圖說明其中一個次要地區發生運作中斷的情形。

![次要地區中斷。 雲端災害復原 - 異地複寫。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

這個設計模式的主要 **優點** 是您可以跨多個次要地區調整應用程式工作負載，以達到最佳的使用者效能。 此選項的 **缺點** 包括：

* 應用程式執行個體和資料庫之間的讀寫連接有不同的延遲和成本
* 運作中斷期間會影響應用程式效能
* 在資料庫容錯移轉之後，應用程式執行個體需要動態變更 SQL 連接字串。  

> [!NOTE]
> 有一個類似的方法可用來卸載特殊工作負載，例如報告工作、商業智慧工具或備份。 這些工作負載通常會耗用大量的資料庫資源，因此建議您為它們指定其中一個次要資料庫，而且效能層級要符合預期的工作負載。
>
>

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>設計模式 3：資料保留的主動-被動部署
這個選項最適合具有下列特性的應用程式：

* 任何資料遺失都會帶來極高的商業風險。 資料庫容錯移轉只能當作永久中斷運作時的最後手段。
* 應用程式可以在「唯讀模式」下運作一段時間。

在此模式下，應用程式連接到次要資料庫時會切換到唯讀模式。 主要區域中的應用程式邏輯會與主要資料庫共置且以讀寫模式 (RW) 運作。 次要地區中的應用程式邏輯會與次要資料庫共置且已準備好以唯讀模式 (RO) 運作。  流量管理員應該設定為使用[容錯移轉路由](../traffic-manager/traffic-manager-configure-failover-routing-method.md)，而且兩個應用程式執行個體都啟用[端點監視](../traffic-manager/traffic-manager-monitoring.md)。

下圖說明此組態在運作中斷之前的情形。
![容錯移轉之前的主動/被動部署。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

當流量管理員偵測到主要區域的連線失敗時，就會自動將使用者流量切換到次要地區中的應用程式執行個體。 使用此模式時，您 **不可以** 在偵測到運作中斷之後起始資料庫容錯移轉。 次要地區中的應用程式會啟動，並使用次要資料庫在唯讀模式下運作。 下圖說明這種情形。

![中斷：處於唯讀模式的應用程式。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

一旦主要區域的運作中斷情況趨緩，流量管理員就會偵測主要區域中的連線恢復，然後將使用者流量切換回到主要區域中的應用程式執行個體。 該應用程式執行個體會恢復執行，並使用主要資料庫在讀寫模式下運作。

> [!NOTE]
> 由於這個模式需要次要資料庫的唯讀存取權，因此會需要「主動式異地複寫」。
>
>

如果次要地區發生運作中斷，流量管理員會將主要區域中的應用程式端點標示為降級，而且複寫通道會暫停。 不過，在運作中斷期間，這個運作中斷不會影響應用程式的效能。 一旦運作中斷情況趨緩，次要資料庫會立即與主要資料庫進行同步處理。 在同步處理期間，主要資料庫的效能可能稍微受到影響，視需要同步處理的資料量而定。

![中斷：次要資料庫。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

此設計模式有幾個 **優點**：

* 在暫時性運作中斷期間避免資料遺失。
* 您不需要部署監視應用程式，因為流量管理員會觸發復原。
* 停機時間僅取決於流量管理員多快偵測到連線失敗，而這是可設定的。

**缺點** 包括：

* 應用程式必須能夠在唯讀模式下運作。
* 當應用程式連接到唯讀資料庫時，必須動態切換到應用程式。
* 主要區域必須復原，讀寫作業才能繼續，這表示可能有數小時甚至幾天無法完整存取資料。

> [!NOTE]
> 如果區域中發生永久性服務中斷，您就必須手動啟動資料庫容錯移轉並承受資料遺失。 應用程式將在次要地區中運作，而且具有資料庫的讀寫存取。
>
>

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>商務持續性計劃：針對雲端災害復原選擇應用程式設計
特定的雲端災害復原策略可以合併或擴充這些設計模式，以完全滿足應用程式的需求。  如先前所述，您選擇的策略取決於您想要提供給客戶的 SLA 和應用程式部署拓樸。 為了協助指導您做決定，下表根據預估資料遺失或復原點目標 (RPO) 和預估復原時間 (ERT) 來比較各種選擇。

| 模式 | RPO | ERT |
|:--- |:--- |:--- |
| 災害復原的主動-被動部署，使用共置資料庫存取 |讀寫存取 < 5 秒 |失敗偵測時間 + 容錯移轉 API 呼叫 + 應用程式驗證測試 |
| 應用程式負載平衡的主動-主動部署 |讀寫存取 < 5 秒 |失敗偵測時間 + 容錯移轉 API 呼叫 + SQL 連接字串變更 + 應用程式驗證測試 |
| 資料保留的主動-被動部署 |唯讀存取 < 5 秒，讀寫存取 = 0 |唯讀存取 = 連線失敗偵測時間 + 應用程式驗證測試  <br>讀寫存取 = 運作中斷趨緩的時間 |

## <a name="next-steps"></a>後續步驟
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)
* 如需主動式異地複寫與彈性集區的相關資訊，請參閱 [彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。



<!--HONumber=Jan17_HO4-->


