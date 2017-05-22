---
title: "使用 Azure SQL Database 設計高可用性服務 | Microsoft Docs"
description: "了解使用 Azure SQL Database 對高可用性服務的應用程式設計。"
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
ms.date: 04/21/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 364038c11f13bcb72b259618b1d7d433f48a33c1
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>使用 Azure SQL Database 設計高可用性服務

在 Azure SQL Database 上建置和部署高可用性服務時，必須使用[容錯移轉群組和主動式異地複寫](sql-database-geo-replication-overview.md)。 它可以從區域性故障和嚴重中斷恢復，並使用容錯移轉至次要資料庫來快速復原。 本文著重於常見的應用程式模式，以及根據應用程式部署需求、您的目標服務等級協定、流量延遲和成本探討每個選項的優缺點。 如需主動式異地複寫與彈性集區的相關資訊，請參閱 [彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>設計模式 1：雲端災害復原的主動-被動部署，使用共置資料庫
這個選項最適合具有下列特性的應用程式：

* 單一 Azure 區域中的作用中執行個體
* 強烈依賴資料的讀寫 (RW) 存取
* 考量到延遲和流量成本，無法接受 Web 應用程式和資料庫之間的跨區域連線能力    

在此情況下，當所有應用程式元件都受影響，而需要當成一個單位進行容錯移轉時，應用程式部署拓撲最適合用來處理區域性災害。 在地理備援方面，會將應用程式邏輯和資料庫複寫到另一個區域，但在正常情況下不會使用它們來處理應用程式工作負載。 次要地區中的應用程式應該設定為使用次要資料庫的 SQL 連接字串。 流量管理員設為使用 [容錯移轉路由方法](../traffic-manager/traffic-manager-configure-failover-routing-method.md)。  

> [!NOTE]
> 這整篇文章使用 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)僅供說明用途。 您可以使用任何支援容錯移轉路由方法的負載平衡方案。    
>

下圖顯示此組態在運作中斷之前的情形。

![SQL Database「異地複寫」組態。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

在主要區域中斷之後，SQL Database 服務會偵測到主要資料庫無法存取，並根據自動容錯移轉原則的參數觸發容錯移轉至次要資料庫。 根據您的應用程式 SLA，您可以決定在中斷偵測和容錯移轉本身之間設定寬限期。 設定寬限期可減少在重大中斷且區域可用性無法快速還原的情況下減少資料遺失的風險。 如果流量管理員在容錯移轉群組觸發資料庫的容錯移轉之前就啟動端點容錯移轉，Web 應用程式將無法重新連線到資料庫。 資料庫容錯移轉一旦完成，應用程式的重新連線嘗試就會自動成功。 

> [!NOTE]
> 若要完全協調應用程式和資料庫的容錯移轉，您應該設計您自己的監視方法，並以手動方式容錯移轉 Web 應用程式端點和資料庫。
>

應用程式端點和資料庫兩者的容錯移轉完成後，應用程式會重新開始處理區域 B 的使用者要求，然後會與資料庫維持在相同位置，因為主要資料庫目前位於區域 B。下圖說明這種情況。 在所有圖表中，實線表示作用中連線，虛線表示暫止的連線，停止標誌表示動作觸發。

![異地複寫：容錯移轉至次要資料庫。 應用程式資料備份。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

如果次要地區發生運作中斷，主要與次要資料庫之間的複寫連結會暫停，但是不會觸發容錯移轉，因為主要資料庫未受到影響。 在此案例中，應用程式的可用性不會改變，但應用程式會在曝露的情況下運作，因此，萬一這兩個區域連續失敗，將帶來較高的風險。

> [!NOTE]
>我們只建議使用具有單一 DR 區域的部署組態。 這是因為大部分 Azure 地理位置有兩個區域。 這些組態無法保護應用程式免於遭受兩個區域同時發生的重大失敗。 在這種罕見的失敗情況下，您可以使用[異地復原作業](sql-database-disaster-recovery.md#recover-using-geo-restore)，在第三個區域復原資料庫。
>

一旦運作中斷情況趨緩，次要資料庫將會自動與主要資料庫重新同步處理。 在同步處理期間，視需要同步處理的資料量而定，主要資料庫的效能可能會稍微受到影響。 下圖說明次要地區發生運作中斷的情形。

![次要資料庫會與主要資料庫進行同步處理。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

此設計模式的主要 **優點** 包括：

* 這兩個區域會部署相同的 Web 應用程式，不需要任何特定地區組態和任何其他邏輯來回應容錯移轉。 
* 應用程式的效能不會受到容錯移轉所影響，因為 Web 應用程式和資料庫永遠共置。

主要 **缺點** 是次要地區中的備援應用程式執行個體只用於災害復原。

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>設計模式 2：應用程式負載平衡的主動-主動部署
此雲端災害復原選項最適合具有下列特性的應用程式：

* 資料庫的讀取與寫入比率很高
* 使用者體驗到的資料庫讀取延遲比寫入延遲更為重要 
* 使用不同的連接字串可以隔開唯讀邏輯和讀寫邏輯
* 唯讀邏輯不依賴資料與最新更新完全同步  

如果您的應用程式具有這些特性，只要將使用者連線的負載分散於不同區域中的多個應用程式執行個體，即可大幅改善整體的使用者體驗。 其中兩個區域應該選取做為 DR 配對，容錯移轉群組則應該包含這些區域中的資料庫。 為了實作負載平衡，每個區域都應該有應用程式的作用中執行個體，而且讀寫 (RW) 邏輯要連接到容錯移轉群組的讀寫接聽程式端點。 如果主要資料庫因為運作中斷受到影響，這可以保證容錯移轉將會自動起始。 Web 應用程式中的唯讀邏輯 (RO) 應該直接連接到該區域中的資料庫。 流量管理員應該設定為使用[效能路由](../traffic-manager/traffic-manager-configure-performance-routing-method.md)，而且每個應用程式執行個體都啟用[端點監視](../traffic-manager/traffic-manager-monitoring.md)。

如同模式 #1，您應該考慮部署類似的監視應用程式。 但有別於模式 #1，監視應用程式將不負責觸發端點容錯移轉。

> [!NOTE]
> 雖然此模式使用一個以上的次要資料庫，但只有區域 B 的次要資料庫才會用於容錯移轉，也應該屬於容錯移轉群組。
>

流量管理員應該設定為效能路由，將使用者連接導向最靠近使用者地理位置的應用程式執行個體。 下圖說明此組態在運作中斷之前的情形。

![無中斷：將效能轉送至最接近的應用程式。 異地複寫。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

如果區域 A 偵測到資料庫中斷，容錯移轉群組會自動開始將區域 A 的主要資料庫容錯移轉到區域 B 的次要資料庫。它也會將讀寫接聽程式端點自動更新到區域 B，讓 Web 應用程式中的讀寫連接不會受到影響。 流量管理員會從路由表中排除離線端點，但會繼續將使用者流量路由傳送至剩餘的線上執行個體。 唯讀 SQL 連接字串不會受到影響，因為它們永遠指向相同區域中的資料庫。 

下圖說明容錯移轉之後的新組態。

![容錯移轉之後的組態。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

如果其中一個次要地區發生運作中斷，流量管理員將會自動從路由表中移除該區域中的離線端點。 對於該區域中次要資料庫的複寫通道將會暫止。 因為在此案例中，剩餘的區域會收到更多使用者流量，所以中斷期間將會影響應用程式的效能。 一旦運作中斷情況趨緩，受影響區域中的次要資料庫將會立即與主要資料庫進行同步處理。 在同步處理期間，主要資料庫的效能可能稍微受到影響，視需要同步處理的資料量而定。 下圖說明區域 B 發生運作中斷的情形。

![次要地區中斷。 雲端災害復原 - 異地複寫。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

這個設計模式的主要 **優點** 是您可以跨多個次要地區調整應用程式工作負載，以達到最佳的使用者效能。 此選項的 **缺點** 包括：

* 應用程式執行個體和資料庫之間的讀寫連接有不同的延遲和成本
* 運作中斷期間會影響應用程式效能

> [!NOTE]
> 有一個類似的方法可用來卸載特殊工作負載，例如報告工作、商業智慧工具或備份。 這些工作負載通常會耗用大量的資料庫資源，因此建議您為它們指定其中一個次要資料庫，而且效能層級要符合預期的工作負載。
>

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>設計模式 3：資料保留的主動-被動部署
這個選項最適合具有下列特性的應用程式：

* 任何資料遺失都會帶來極高的商業風險。 資料庫容錯移轉只能當作中斷情況嚴重時的最後手段。
* 應用程式支援唯讀和讀寫模式的作業，而且可以「唯讀模式」運作一段時間。

在此模式中，應用程式會在讀寫連接開始收到逾時錯誤時切換到唯讀模式。 Web 應用程式要部署到這兩個區域，並且包含讀寫接聽程式端點的連接，和唯讀接聽程式端點的不同連接。 流量管理員應該設定為使用[容錯移轉路由](../traffic-manager/traffic-manager-configure-failover-routing-method.md)，而且每個區域中的應用程式端點都啟用[端點監視](../traffic-manager/traffic-manager-monitoring.md)。

下圖說明此組態在運作中斷之前的情形。

![容錯移轉之前的主動/被動部署。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

當流量管理員偵測到區域 A 的連線失敗時，會自動將使用者流量切換到區域 B 中的應用程式執行個體。使用此模式時，請務必將資料遺失的寬限期設為夠高的值，例如 24 小時。 如果在該時間內中斷情況已趨緩，這可以確保避免遺失資料。 當區域 B 的 Web 應用程式啟動時，讀寫作業將會開始失敗。 此時應該會切換到唯讀模式。 在此模式中，要求將會自動路由到次要資料庫。 發生中斷情況不會在寬限期內趨緩的災難性失敗時，容錯移轉群組將會觸發容錯移轉。 之後就可以使用讀寫接聽程式，呼叫就不會再失敗。 下圖說明這種情形。

![中斷：處於唯讀模式的應用程式。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

如果主要區域的運作中斷情況在寬限期內趨緩，流量管理員就會偵測到主要區域中的連線恢復，然後將使用者流量切換回到區域 A 中的應用程式執行個體。該應用程式執行個體會繼續使用區域 A 的主要資料庫以讀寫模式運作。

如果是區域 B 發生中斷情況，流量管理員會偵測到區域 B 中的應用程式端點失敗，容錯移轉群組就會將唯讀接聽程式切換到區域 A。此中斷情況不會影響使用者體驗，但是主要資料庫會在中斷期間曝露出來。 下圖說明這種情形。

![中斷：次要資料庫。 雲端災害復原。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

一旦中斷情況趨緩，次要資料庫會立即與主要資料庫同步處理，唯讀接聽程式會切換回區域 B 中的次要資料庫。在同步處理期間，主要資料庫的效能可能會根據需要進行同步處理的資料量而稍微受到影響。

此設計模式有幾個 **優點**：

* 在暫時性運作中斷期間避免資料遺失。
* 停機時間僅取決於流量管理員多快偵測到連線失敗，而這是可設定的。

**取捨**是︰

* 應用程式必須能夠在唯讀模式下運作。

> [!NOTE]
> 如果區域中發生永久性服務中斷，您就必須手動啟動資料庫容錯移轉並承受資料遺失。 應用程式將在次要地區中運作，而且具有資料庫的讀寫存取。
>

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>商務持續性計劃：針對雲端災害復原選擇應用程式設計
特定的雲端災害復原策略可以合併或擴充這些設計模式，以完全滿足應用程式的需求。  如先前所述，您選擇的策略取決於您想要提供給客戶的 SLA 和應用程式部署拓樸。 為了協助指導您做決定，下表根據預估資料遺失或復原點目標 (RPO) 和預估復原時間 (ERT) 來比較各種選擇。

| 模式 | RPO | ERT |
|:--- |:--- |:--- |
| 災害復原的主動-被動部署，使用共置資料庫存取 |讀寫存取 < 5 秒 |失敗偵測時間 + DNS TTL |
| 應用程式負載平衡的主動-主動部署 |讀寫存取 < 5 秒 |失敗偵測時間 + DNS TTL |
| 資料保留的主動-被動部署 |唯讀存取 < 5 秒 | 唯讀存取 = 0 |
||讀寫存取 = 0 | 讀寫存取 = 失敗偵測時間 + 遺失資料的寬限期 |
|||

## <a name="next-steps"></a>後續步驟
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
* 若要了解更快速的復原選項，請參閱 [主動式異地複寫](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自動備份進行封存，請參閱 [資料庫複製](sql-database-copy.md)
* 如需主動式異地複寫與彈性集區的相關資訊，請參閱 [彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

