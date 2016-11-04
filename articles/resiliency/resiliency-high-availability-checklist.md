---
title: 可用性檢查清單 | Microsoft Docs
description: 您可採取的設定和動作的快速檢查清單，確保您以 Azure 改善應用程式可用性。
services: ''
documentationcenter: na
author: adamglick
manager: saladki
editor: ''

ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick

---
# <a name="high-availability-checklist"></a>高可用性檢查清單
使用 Azure 的好處之一是能夠利用雲端提高應用程式的可用性 (和延展性)。 為了確定您可以充分利用這些選項，下列檢查清單可協助您了解一些重要的基礎結構基本概念，以確保應用程式的恢復功能。 

> [!NOTE]
> 以下大部分的建議可隨時在應用程式中實作，因此很適合用在「快速檢修」。 最佳的長期解決方案通常涉及為雲端而打造的應用程式設計。  如需這幾項的檢查清單 (更設計導向的領域)，請參閱 [可用性檢查清單](../best-practices-availability-checklist.md)。
> 
> 

### <a name="are-you-using-traffic-manager-in-front-of-your-resources?"></a>您在資源前方使用流量管理員嗎？
使用流量管理員可協助您跨 Azure 區域或在 Azure 和內部部署位置之間，路由傳送網際網路流量。 這樣做有幾項理由，包括延遲和可用性。 如需如何使用流量管理員來增加恢復功能並將流量分散到多個區域的詳細資訊，請參閱 [在 Azure 的多個資料中心內執行 VM 以獲得高可用性](../guidance/guidance-compute-multiple-datacenters.md)。

**如果您不使用流量管理員，會發生什麼事？**  如果您在應用程式前方未使用流量管理員，則您只能在單一區域中存取資源。 這會限制您的規模調整、遠離所選擇區域的使用者會感受更長的延遲，而在區域全面性服務中斷的情況下將無力保護。

### <a name="have-you-avoided-using-a-single-virtual-machine-for-any-role?"></a>您已避免使用單一虛擬機器作為任何角色嗎？
良好的設計會避免任何單一失敗點。 這在所有服務設計 (內部部署或雲端) 中都很重要，尤其在雲端中特別有用，因為您可以透過相應放大 (新增虛擬機器) 而非相應增加 (使用功能更強大的虛擬機器)，以提高延展性和恢復功能。 如需可調整的應用程式設計的詳細資訊，請參閱 [建置在 Microsoft Azure 上之應用程式的高可用性](resiliency-high-availability-azure-applications.md)。

**如果以單一虛擬機器作為角色，會發生什麼事？** 單一機器是單一失敗點，不適用於 [Azure 虛擬機器服務等級協定](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)。 在最佳的情況下，應用程式將會正常運作，但這不是具有恢復功能的設計、不在 Azure 虛擬機器 SLA 的涵蓋範圍內，而且如果發生狀況，任何單一失敗點都會增加停機的機會。

### <a name="are-you-using-a-load-balancer-in-front-of-your-application's-internet-facing-vms?"></a>您在應用程式的網際網路對向 VM 前方使用負載平衡器嗎？
負載平衡器可讓您將應用程式的連入流量分散到任意數目的機器。 您可以隨時從負載平衡器新增/移除電腦，這適用於虛擬機器 (以及搭配虛擬機器擴充集來自動調整規模)，讓您輕鬆處理流量增加或 VM 失敗的情況。 如果您想要深入了解負載平衡器，請參閱 [Azure Load Balancer 概觀](../load-balancer/load-balancer-overview.md)和[在 Azure 上執行多個 VM 以獲得延展性和可用性](../guidance/guidance-compute-multi-vm.md)。

**如果您在網際網路對向 VM 前方未使用負載平衡器，會發生什麼事？**  如果沒有負載平衡器，您將無法相應放大 (新增更多虛擬機器)，而只能選擇相應增加 (增加網路面向虛擬機器的大小)。 您也將面臨該虛擬機器的單一失敗點。 您也必須撰寫 DNS 程式碼，以注意是否遺失網際網路對向電腦，並將 DNS 項目重新對應至您開始接管的新電腦。

### <a name="are-you-using-availability-sets-for-your-stateless-application-and-web-servers?"></a>您的無狀態應用程式和 Web 伺服器使用可用性設定組嗎？
將電腦納入可用性設定組的相同應用程式層可讓 VM 符合 Azure VM SLA 的資格。 成為可用性設定組的一部分也確保電腦放在不同的更新網域 (也就是在不同時間修補的不同主機機器) 和容錯網域 (也就是共用通用電源和網路交換器的主機電腦)。 不在可用性設定組中的 VM 可能位於相同的主機電腦，因此，可能潛藏著看不見的單一失敗點。 如需使用可用性設定組提高 VM 可用性的詳細資訊，請參閱 [管理虛擬機器的可用性](../virtual-machines/virtual-machines-windows-manage-availability.md)。

**如果無狀態應用程式和 Web 伺服器不使用可用性設定組，會發生什麼事？**  不使用可用性設定組表示您無法利用 Azure VM SLA。 這也表示，如果主機電腦進行更新 (主控您所使用的 VM 的電腦) 或發生常見的硬體故障，該應用程式層中的電腦會全部離線。

### <a name="are-you-using-virtual-machine-scale-sets-(vmss)-for-your-stateless-application-or-web-servers?"></a>您的無狀態應用程式和 Web 伺服器使用虛擬機器擴展集 (VMSS) 嗎？
可調整又有恢復功能的良好設計會使用 VMSS，以確保您可以擴增/縮減應用程式層中的電腦數目 (例如 Web 層)。 VMSS 可讓您定義應用程式層如何調整規模 (根據您選擇的準則來新增或移除伺服器)。 如需如何使用 Azure 虛擬機器調整集來靈活因應流量尖峰的詳細資訊，請參閱 [虛擬機器調整集概觀](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

**如果無狀態應用程式或 Web 伺服器不使虛擬機器擴展集，會發生什麼事？**  如果沒有 VMSS，您會比較沒有能力無限制調整規模及最佳化資源用量。 缺少 VMSS 的設計有其調整上限，必須以額外程式碼來處理 (或手動)。 如此欠缺 VMSS 也表示應用程式無法輕易新增和移除電腦 (不論規模)，因而無法協助您處理大量的尖峰流量 (例如在促銷期間，或如果您的網站/應用程式/產品爆紅)。

### <a name="are-you-using-premium-storage-and-separate-storage-accounts-for-each-of-your-virtual-machines?"></a>您的每個虛擬機器使用進階儲存體和個別的儲存體帳戶嗎？
生產虛擬機器最好使用進階儲存體。 此外，您應該確保每個虛擬機器使用不同的儲存體帳戶 (在小型部署中確實如此。 針對大型部署，多個電腦可以重複使用儲存體帳戶，但需要維持平衡，以確保更新網域之間和應用程式層之間達到平衡)。 如需 Azure 儲存體效能和延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體效能與延展性檢查清單](../storage/storage-performance-checklist.md)。

**如果每個虛擬機器不使用個別的儲存體帳戶，會發生什麼事？**  就像許多其他資源一樣，儲存體帳戶也是單一失敗點。 雖然 Azure 儲存體有許多保護和恢復功能，但單一失敗點絕對不是良好的設計。 比方說，如果該帳戶的存取權限受損、達到儲存體限制，或達到 [IOPS 限制](../azure-subscription-service-limits.md#virtual-machine-disk-limits) ，則會影響使用該儲存體帳戶的所有虛擬機器。 此外，如果服務中斷影響到包含該特定儲存體帳戶的儲存體戳記，則會影響多個虛擬機器。

### <a name="are-you-using-a-load-balancer-or-a-queue-between-each-tier-of-your-application?"></a>您在應用程式的每一層之間使用負載平衡器或佇列嗎？
在應用程式的每一層之間使用負載平衡器或佇列，可讓您輕鬆又獨立地調整應用程式的每一層規模。 您應該根據延遲、複雜性和散發性 (也就是應用程式散發的範圍廣度) 需求來選擇這些技術。 一般而言，佇列的延遲和複雜性通常較高，但可讓您更具恢復功能，還可讓您將應用程式散發到更大的區域 (例如跨區域)。 如需如何使用內部負載平衡器或佇列的詳細資訊，請參閱[內部負載平衡器概觀](../load-balancer/load-balancer-internal-overview.md)和 [Azure 佇列和服務匯流排佇列 - 異同比較](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)。

**如果您在應用程式的每一層之間不使用負載平衡器或佇列，發生什麼事？**  如果應用程式的每一層之間沒有負載平衡器或佇列，則很難將應用程式相應增加或相應減少，並將其負載分散到多部電腦。 不這樣做可能會導致資源佈建過度或不足，如果流量出現未預期的變更，或發生系統失敗，還可能造成停機或使用者體驗不佳。

### <a name="are-your-sql-databases-using-active-geo-replication?"></a>您的 SQL Database 使用作用中異地複寫嗎？
作用中異地複寫可讓您在相同或不同區域中，設定最多 4 個可讀取的次要資料庫。 在服務中斷或無法連線至主要資料庫的情況下，便可使用次要資料庫。 如果您想要深入了解 SQL Database 作用中異地複寫，請參閱 [概觀︰SQL Database 作用中異地複寫](../sql-database/sql-database-geo-replication-overview.md)。

 **如果您的 SQL Database 不使用作用中異地複寫，會發生什麼事？** 在沒有作用中異地複寫的情況下，如果主要資料庫離線 (計劃性維護、服務中斷、硬體故障等)，應用程式資料庫會離線，直到主要資料庫恢復連線且狀態良好為止。 

### <a name="are-you-using-a-cache-(azure-redis-cache)-in-front-of-your-databases?"></a>您在資料庫前方使用快取 (Azure Redis Cache) 嗎？
如果應用程式的資料庫負載很高，其中大部分資料庫呼叫都是讀取，您可以在資料庫前方實作快取層來卸載這些讀取作業，以增加應用程式的速度並減少資料庫的負載。 您可以在資料庫前方放置快取層，以增加應用程式的速度並減少資料庫負載 (因而增加它可處理的規模)。 如果您想要深入了解 Azure Redis 快取，請參閱 [快取指引](../best-practices-caching.md)。

 **如果在資料庫前方不使用快取，會發生什麼事？**  如果資料庫電腦的功能強大，足以處理您加諸於它的流量負載，則應用程式會正常回應，但這也可能表示在負載較低時，資料庫電腦的成本相對地較不划算。 如果資料庫電腦的功能不足以處理負載，則使用者的應用程式體驗會開始惡化 (延遲、逾時，甚至是服務中斷)。

### <a name="have-you-contacted-microsoft-azure-support-if-you-are-expecting-a-high-scale-event?"></a>如果您預期有大規模的活動，您會連絡 Microsoft Azure 支援嗎？
Azure 支援可協助您提高服務限制以因應計劃性的高流量事件 (例如新產品上市或特殊節日)。 Azure 支援也能夠幫您連絡專家，以協助您與帳戶小組一起檢閱設計，並協助您找到可滿足大規模活動需求的最佳解決方案。 如需如何連絡 Azure 支援的詳細資訊，請參閱 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

**如果您未連絡 Azure 支援來協助處理大規模活動，會發生什麼事？** 面對高流量事件，如果您不溝通或規劃，您可能會達到特定的 [Azure 服務限制](../azure-subscription-service-limits.md) ，因而在事件期間造成使用者體驗不佳 (更糟的是停機)。 在衝擊湧現之前做好架構檢討和溝通，有助於減緩這些風險。

### <a name="are-you-using-a-content-delivery-network-(azure-cdn)-in-front-of-your-web-facing-storage-blobs-and-static-assets?"></a>您在 Web 面向的儲存體 blob 和靜態資產前方使用內容傳遞網路 (CDN) 嗎？
使用 CDN 可在世界各地的 CDN POP/邊緣位置快取內容，有助於減輕伺服器的負載。 這樣做可以縮短延遲、增加延展性、減少伺服器負載，並納入防範阻斷服務 (DOS) 攻擊的策略中。 如需如何使用 Azure CDN 增加恢復功能並縮短客戶延遲的詳細資訊，請參閱 [Azure 內容傳遞網路 (CDN) 概觀](../cdn/cdn-overview.md)。

**如果您不使用 CDN，會發生什麼事？**  如果不使用 CDN，則所有客戶流量會衝向您的資源。 這表示伺服器的負載會升高，導致其延展性降低。 此外，客戶可能會感覺延遲更長，因為 CDN 在世界各地的位置有可能更接近客戶。

## <a name="next-steps:"></a>後續步驟：
如果您想要詳細了解如何設計高可用性的應用程式，請參閱 [建置在 Microsoft Azure 上之應用程式的高可用性](resiliency-high-availability-azure-applications.md)。

<!--HONumber=Oct16_HO2-->


