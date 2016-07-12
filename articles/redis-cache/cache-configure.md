<properties 
	pageTitle="如何設定 Azure Redis 快取 | Microsoft Azure"
	description="了解 Azure Redis 快取的預設 Redis 組態，以及了解如何設定您的 Azure Redis 快取執行個體"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="douge"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="06/29/2016"
	ms.author="sdanie" />

# 如何設定 Azure Redis 快取

本主題描述如何檢視並更新您的 Azure Redis 快取執行個體的組態，並涵蓋 Azure Redis 快取執行個體的預設 Redis 伺服器組態。

>[AZURE.NOTE] 如需設定及使用進階快取功能的詳細資訊，請參閱[如何設定進階 Azure Redis Cache 的持續性](cache-how-to-premium-persistence.md)、[如何設定進階 Azure Redis Cache 的叢集](cache-how-to-premium-clustering.md)，以及[如何設定進階 Azure Redis Cache 的虛擬網路支援](cache-how-to-premium-vnet.md)。

## 設定 Redis 快取設定

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Redis 快取會在 [設定] 刀鋒視窗上提供下列設定。

![Redis 快取設定](./media/cache-configure/redis-cache-settings.png)



-	[支援和疑難排解設定](#support-amp-troubleshooting-settings)
-	[一般設定](#general-settings)
	-	[屬性](#properties)
	-	[存取金鑰](#access-keys)
	-	[存取連接埠](#access-ports)
	-	[Maxmemory 原則](#maxmemory-policy-and-maxmemory-reserved)
	-	[進階設定 (Keyspace 通知)](#keyspace-notifications-advanced-settings)
	-	[Redis 快取顧問](#redis-cache-advisor)
-	[擴充設定](#scale-settings)
	-	[定價層](#pricing-tier)
	-	[Redis 叢集大小](#cluster-size)
-	[資料管理設定](#data-management-settings)
	-	[Redis 資料永續性](#redis-data-persistence)
	-	[匯入/匯出](#importexport)
-	[管理設定](#administration-settings)
	-	[重新啟動](#reboot)
	-	[排程更新](#schedule-updates)
-	[診斷設定](#diagnostics-settings)
-	[網路設定](#network-settings)
-	[資源管理設定](#resource-management-settings)

## 支援和疑難排解設定

**支援 + 疑難排解**區段中的設定提供選項，讓您解決快取的問題。

![支援與疑難排解](./media/cache-configure/redis-cache-support-troubleshooting.png)

按一下 [疑難排解] 將提供您解決這些常見問題和解決問題的策略。

按一下 [稽核記錄檔]，以檢視在快取上執行的動作。您也可以使用篩選，來展開此檢視以包含其他資源。如需使用稽核記錄檔的詳細資訊，請參閱[檢視事件和稽核記錄檔](../azure-portal/insights-debugging-with-events.md)及[使用 Resource Manager 來稽核作業](../resource-group-audit.md)。如需如何監視 Azure Redis 快取事件的詳細資訊，請參閱[作業和警示](cache-how-to-monitor.md#operations-and-alerts)。

**資源健康狀態**會監看您的資源，並告知您其是否正在如預期般執行。如需 Azure 資源健康狀態服務的詳細資訊，請參閱 [Azure 資源健康狀態概觀](../resource-health/resource-health-overview.md)。

按一下 [新增支援要求] 以開啟快取的支援要求。

## 一般設定

[一般] 區段中的設定可讓您存取和設定下列快取設定。

![一般設定](./media/cache-configure/redis-cache-general-settings.png)

-	[屬性](#properties)
-	[存取金鑰](#access-keys)
-	[存取連接埠](#access-ports)
-	[Maxmemory 原則](#maxmemory-policy-and-maxmemory-reserved)
-	[進階設定 (Keyspace 通知)](#keyspace-notifications-advanced-settings)
-	[Redis 快取顧問](#redis-cache-advisor)

### 屬性

按一下 [屬性] 以檢視快取的相關資訊，包括快取端點和連接埠。

![Redis 快取屬性](./media/cache-configure/redis-cache-properties.png)

### 存取金鑰

按一下 [存取金鑰] 以檢視或重新產生快取的存取金鑰。連接到快取的用戶端會使用這些金鑰以及 [屬性] 刀鋒視窗中的主機名稱和連接埠。

![Redis 快取存取金鑰](./media/cache-configure/redis-cache-manage-keys.png)

### 存取連接埠

根據預設，新的快取會停用非 SSL 存取。若要啟用非 SSL 連接埠，請按一下 [存取連接埠] 刀鋒視窗，然後按一下 [否]。

![Redis 快取存取連接埠](./media/cache-configure/redis-cache-access-ports.png)

### Maxmemory-policy 和 maxmemory-reserved

按一下 [Maxmemory 原則] 以設定快取的記憶體原則。**maxmemory-policy** 設定會設定快取的收回原則，而 **maxmemory-reserved** 設定則會設定保留給非快取程序的記憶體。

![Redis 快取 Maxmemory 原則](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory 原則**可讓您從下列收回原則中選擇。

-	volatile-lru - 這是預設值。
-	allkeys lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

如需 Maxmemory 原則的詳細資訊，請參閱[收回原則](http://redis.io/topics/lru-cache#eviction-policies)。

**maxmemory-reserved** 設定會設定保留給非快取作業 (例如容錯移轉期間的複寫) 的記憶體量 (MB)。當具有高片段比率時，也可使用它。設定此值可讓您在負載變動時具有更一致的 Redis 伺服器體驗。對於頻繁寫入的工作負載，此值應該設定為更高的值。當記憶體保留給這類作業時，無法用於儲存快取的資料。

>[AZURE.IMPORTANT] **maxmemory-reserved** 設定只適用於標準和高階快取。

### Keyspace 通知 (進階設定)

按一下 [進階設定] 以設定 Redis Keyspace 通知。Keyspace 通知可讓用戶端在特定事件發生時收到通知。

![Redis 快取進階設定](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Keyspace 通知和 **notify-keyspace-events** 設定只適用於標準和高階快取。

如需詳細資訊，請參閱 [Redis Keyspace 通知](http://redis.io/topics/notifications) (英文)。如需範例程式碼，請參閱 [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 範例中的 [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) 檔案。

<a name="recommendations"></a>
## Redis 快取顧問

[建議] 刀鋒視窗會顯示適用於快取的建議。在一般作業期間，不會顯示任何建議。

![建議](./media/cache-configure/redis-cache-no-recommendations.png)

如果在您的快取作業期間發生任何狀況 (例如，高記憶體使用量、網路頻寬或伺服器負載)，即會在 [Redis 快取] 刀鋒視窗中顯示警示。

![建議](./media/cache-configure/redis-cache-recommendations-alert.png)

您可以在 [建議] 刀鋒視窗中找到進一步資訊。

![建議](./media/cache-configure/redis-cache-recommendations.png)

您可以在 [Redis 快取] 刀鋒視窗的[監視圖表](cache-how-to-monitor.md#monitoring-charts)和[使用量圖表](cache-how-to-monitor.md#usage-charts)區段上監視這些計量。

每個定價層都有不同的用戶端連線、記憶體和頻寬的限制。如果您的快取持續一段時間接近這些計量的最大容量，即會提供建議。如需透過**建議**工具檢閱的計量和限制詳細資訊，請參閱下表。

| Redis 快取計量 | 如需詳細資訊，請參閱 |
|-------------------------|---------------------------------------------------------------------------|
| 網路頻寬使用量 | [快取效能 - 可用的頻寬](cache-faq.md#cache-performance) |
| 連線的用戶端 | [預設 Redis 伺服器組態 - maxclients](#maxclients) |
| 伺服器負載 | [使用量圖表 - Redis 伺服器負載](cache-how-to-monitor.md#usage-charts) |
| 記憶體使用量 | [快取效能 - 大小](cache-faq.md#cache-performance) |

若要升級您的快取，按一下 [立即升級] 以變更[定價層](#pricing-tier)及調整您的快取。如需選擇定價層的詳細資訊，請參閱[應該使用哪個 Redis 快取供應項目和大小？](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)。

## 擴充設定

[調整] 區段中的設定可讓您存取和設定下列快取設定。

![網路](./media/cache-configure/redis-cache-scale.png)

-	[定價層](#pricing-tier)
-	[Redis 叢集大小](#cluster-size)

### 定價層

按一下 [定價層] 以檢視或變更快取的定價層。如需調整的詳細資訊，請參閱[如何調整 Azure Redis Cache](cache-how-to-scale.md)。

![Redis 快取定價層](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### Redis 叢集大小

按一下 [Redis 叢集大小 (預覽)]，針對已啟用叢集且目前執行中的進階快取，變更叢集大小。

>[AZURE.NOTE] 請注意，雖然 Azure Redis 快取進階層已發行正式上市版，但 Redis 叢集大小功能目前為預覽狀態。

![Redis 叢集大小](./media/cache-configure/redis-cache-redis-cluster-size.png)

若要變更叢集大小，請使用滑桿，或在 [分區計數] 文字方塊中輸入 1 到 10 之間的數字，然後按一下 [確定] 加以儲存。

>[AZURE.IMPORTANT] Redis 叢集只適用於進階快取。如需詳細資訊，請參閱[如何設定進階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md)。


## 資料管理設定

[資料管理] 區段中的設定可讓您存取和設定下列快取設定。

![資料管理](./media/cache-configure/redis-cache-data-management.png)

-	[Redis 資料永續性](#redis-data-persistence)
-	[匯入/匯出](#importexport)

### Redis 資料永續性

按一下 [Redis 資料持續性] 可加以啟用、停用，或設定高階快取的資料持續性。

![Redis 資料永續性](./media/cache-configure/redis-cache-persistence-settings.png)

若要啟用 Redis 持續性，請按一下 [已啟用] 來啟用 RDB (Redis 資料庫) 備份。若要停用 Redis 持續性，請按一下 [已停用]。

若要設定備份間隔，請選取下拉式清單中的 [備份頻率。選項包括 [15 分鐘]、[30 分鐘]、[60 分鐘]、[6 小時]、[12 小時] 及 [24 小]時。在先前的備份作業成功完成後，此間隔便會開始倒數計時，時間過後就會起始新的備份。

按一下 [儲存體帳戶] 選取要使用的儲存體帳戶，然後從 [儲存體金鑰] 下拉式清單中選擇 [主要金鑰] 或 [次要金鑰]。您必須選擇與快取相同區域的儲存體帳戶，建議選取 [進階儲存體] 帳戶，因為進儲存體的輸送量較高。不管任何時候，只要重新產生了持續性帳戶的儲存體金鑰，您就必須從 [儲存體金鑰] 下拉式清單中重新選擇所需的金鑰。

按一下 [確定] 以儲存持續性組態。

>[AZURE.IMPORTANT] Redis 資料持續性僅適用於進階快取。如需詳細資訊，請參閱[如何設定進階 Azure Redis 快取的永續性](cache-how-to-premium-persistence.md)。

### 匯入/匯出

匯入/匯出是 Azure Redis 快取資料管理作業，可讓您將資料匯入 Azure Redis 快取或將資料從 Azure Redis 快取匯出，方法是從進階快取將 Redis 快取資料庫 (RDB) 快照匯入和匯出至 Azure 儲存體帳戶中的分頁 blob。這可讓您在不同的 Azure Redis 快取執行個體之間移轉，或在使用前將資料填入快取。

匯入可以用來從執行雲端或環境的任何 Redis 伺服器 (包含在 Linux、Windows 上執行的 Redis，或任何雲端提供者，例如 Amazon Web Services 等) 引入 Redis 相容 RDB 檔案。匯入資料是使用預先填入資料建立快取的輕鬆方式。在匯入程序期間，Azure Redis 快取會從 Azure 儲存體將 RDB 檔案載入記憶體，然後將金鑰插入快取。

匯出可讓您將儲存在 Azure Redis 快取中的資料匯出至 Redis 相容 RDB 檔案。您可以使用這項功能，將資料從一個 Azure Redis 快取執行個體移到另一個或其他 Redis 伺服器。在匯出程序期間，會在裝載 Azure Redis 快取伺服器執行個體的 VM 上建立站存檔案，並將檔案上傳至指定的儲存體帳戶。當匯出作業完成時的狀態為成功或失敗時，都會刪除暫存檔案。

>[AZURE.IMPORTANT] 匯入/匯出僅供進階層快取使用。如需詳細資訊和指示，請參閱[在 Azure Redis 快取中匯入與匯出資料](cache-how-to-import-export-data.md)。


## 管理設定

[管理] 區段中的設定可讓您針對進階快取執行下列管理工作。

![系統管理](./media/cache-configure/redis-cache-administration.png)

-	[重新啟動](#reboot)
-	[排程更新](#schedule-updates)

>[AZURE.IMPORTANT] 本節中的設定僅適用於進階層快取。

### 重新啟動

[重新啟動] 刀鋒視窗可讓您重新啟動快取的一或多個節點。這可讓您測試應用程式，以便在發生失敗時加以復原。

![重新啟動](./media/cache-configure/redis-cache-reboot.png)

如果您的進階快取已啟用叢集，您可以選取要重新啟動的快取分區。

![重新啟動](./media/cache-configure/redis-cache-reboot-cluster.png)

若要重新啟動快取的一或多個節點，選取所需的節點，然後按一下 [重新啟動]。如果您的進階快取已啟用叢集，選取要重新啟動的分區，然後按一下 [重新啟動]。稍候幾分鐘之後，選取的節點會重新啟動，並在幾分鐘之後重新上線。

>[AZURE.IMPORTANT] 重新啟動僅適用於進階層快取。如需詳細資訊和指示，請參閱 [Azure Redis Cache administration - Reboot (Azure Redis 快取管理 - 重新啟動)](cache-administration.md#reboot)。

### 排程更新

[排程更新] 刀鋒視窗可讓您針對適用於快取的 Redis 伺服器更新指定維護期間。

>[AZURE.IMPORTANT] 請注意，維護期間僅適用於 Redis 伺服器更新，不適用於任何 Azure 更新，或是在裝載快取的 VM 上更新作業系統。

![排程更新](./media/cache-configure/redis-schedule-updates.png)

若要指定維護期間，請檢查所需的天數，並指定每一天的維護期間開始小時，然後按一下 [確定]。請注意，維護期間時間是 UTC。

>[AZURE.IMPORTANT] 排程更新僅適用於進階層快取。如需詳細資訊和指示，請參閱 [Azure Redis Cache administration - Schedule updates (Azure Redis 快取管理 - 排程更新)](cache-administration.md#schedule-updates)。

## 診斷設定

[診斷] 區段可讓您設定 Redis 快取的診斷。

![診斷](./media/cache-configure/redis-cache-diagnostics.png)

按一下 [診斷] 以[設定用來儲存快取診斷的儲存體帳戶](cache-how-to-monitor.md#enable-cache-diagnostics)。

![Redis 快取診斷](./media/cache-configure/redis-cache-diagnostics-settings.png)

按一下 [Redis 度量] 可[檢視快取的計量](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts)，而 [警示規則] 可[設定警示規則](cache-how-to-monitor.md#operations-and-alerts)。

如需有關 Azure Redis 快取診斷的詳細資訊，請參閱[如何監視 Azure Redis 快取](cache-how-to-monitor.md)。


## 網路設定

[網路] 區段中的設定可讓您存取和設定下列快取設定。

![網路](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] 虛擬網路設定只適用於快取建立期間利用 VNET 支援設定的進階快取。如需使用 VNET 支援建立進階快取並更新其設定的相關資訊，請參閱[如何設定進階 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)。

## 資源管理設定

![資源管理](./media/cache-configure/redis-cache-resource-management.png)

[標記] 區段有助於您組織資源。如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](../resource-group-using-tags.md)。

[鎖定] 區段可讓您鎖定訂用帳戶、資源群組或資源，以防止組織中的其他使用者不小心刪除或修改重要資源。如需詳細資訊，請參閱[使用 Azure 資源管理員來鎖定資源](../resource-group-lock-resources.md)。

[使用者] 區段會在 Azure 入口網站中提供角色型存取控制 (RBAC) 支援，協助組織輕鬆且準確地滿足其存取管理需求。如需詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](../active-directory/role-based-access-control-configure.md)。

按一下 [匯出範本] 可建置並匯出您所部署資源的範本，供未來部署使用。如需使用範本的詳細資訊，請參閱[使用 Azure Resource Manager 範本部署資源](../resource-group-template-deploy.md)。

## 預設 Redis 伺服器組態

新的 Azure Redis 快取執行個體是以下列的預設 Redis 組態值設定。

>[AZURE.NOTE] 您無法使用 `StackExchange.Redis.IServer.ConfigSet` 方法變更本區段中的設定。如果在本區段中利用其中一個命令呼叫此方法，則會擲回如下的例外狀況：
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>任何可設定的值 (例如 **max-memory-policy**) 都可以透過 Azure 入口網站或命令列管理工具 (例如 Azure CLI 或 PowerShell) 加以設定。

|設定|預設值|說明|
|---|---|---|
|資料庫|16|資料庫的預設數目為 16，但是您可以根據定價層設定不同的數字。<sup>1</sup> 預設資料庫為 DB 0，您可以根據每個連接使用 `connection.GetDatabase(dbid)` 選取一個不同的資料庫，其中 dbid 是介於 `0` 與 `databases - 1` 之間的數字。|
|maxclients|取決於定價層<sup>2</sup>|這是允許同時連線的用戶端數目上限。一旦達到限制，Redis 將關閉所有新的連接，同時傳送「達到用戶端的數目上限」錯誤。|
|maxmemory-policy|volatile-lru|Maxmemory 原則可設定當達到 maxmemory (建立快取時所選取之快取提供項目的大小) 時 Redis 將如何選取要移除的具目。Azure Redis 快取的預設設定為 volatile-lru，其會移除使用 LRU 演算法設定到期日的金鑰。此設定可以在 Azure 入口網站中設定。如需詳細資訊，請參閱 [Maxmemory-policy 與 maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)。|
|maxmemory-samples|3|LRU 和最小 TTL 演算法不是精確的演算法，而是近似的演算法 (為了節省記憶體)，因此您也可以選取要檢查的範例大小。例如，預設為 Redis 將檢查三個金鑰，並挑選最近較少使用的金鑰。|
|lua-time-limit|5,000|Lua 指令碼的最大執行時間 (以毫秒為單位)。如果已到達最大執行時間，Redis 會記錄指令碼在最大允許的時間之後仍在執行中，並開始回覆查詢發生錯誤。|
|lua-event-limit|500|這是指令碼事件佇列的最大大小。|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|用戶端輸出緩衝區限制可用來強制中斷基於某些原因而無法足夠快地從伺服器讀取資料之用戶端的連線 (常見的原因是 Pub/Sub 用戶端使用訊息的速度無法與發佈者產生這些訊息的速度一樣快)。如需詳細資訊，請參閱 [http://redis.io/topics/clients](http://redis.io/topics/clients)。|

<a name="databases"></a> <sup>1</sup>適用於每個 Azure Redis 快取定價層的 `databases` 限制皆不相同，可在快取建立時加以設定。如果快取建立期間未指定 `databases` 設定，則預設值為 16。

-	基本和標準的快取
	-	C0 (250 MB) 快取 - 最多 16 個資料庫
	-	C1 (1 GB) 快取 - 最多 16 個資料庫
	-	C2 (2.5 GB) 快取 - 最多 16 個資料庫
	-	C3 (6 GB) 快取 - 最多 16 個資料庫
	-	C4 (13 GB) 快取 - 最多 32 個資料庫
	-	C5 (26 GB) 快取 - 最多 48 個資料庫
	-	C6 (53 GB) 快取 - 最多 64 個資料庫
-	進階快取
	-	P1 (6 GB - 60 GB) - 最多 16 個資料庫
	-	P2 (13 GB - 130 GB) - 最多 32 個資料庫
	-	P3 (26 GB - 260 GB) - 最多 48 個資料庫
	-	P4 (53 GB - 530 GB) - 最多 64 個資料庫
	-   所有進階快取均已啟用 Redis 叢集 - Redis 叢集僅支援使用資料庫 0，因此對於已啟用 Redis 叢集的任何進階快取，`databases` 限制實際上是 1，並且不允許 [Select](http://redis.io/commands/select) 命令。如需詳細資訊，請參閱[我需要對我的用戶端應用程式進行任何變更才能使用叢集嗎？](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] `databases` 設定只能在快取建立期間，而且只能使用 PowerShell、CLI 或其他管理用戶端來設定。如需在快取建立期間使用 PowerShell 設定 `databases` 的範例，請參閱 [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases)。


<a name="maxclients"></a> <sup>2</sup>適用於每個 Azure Redis 快取定價層的 `maxclients` 皆不相同。

-	基本和標準的快取
	-	C0 (250 MB) 快取 - 最多 256 個連接
	-	C1 (1 GB) 快取 - 最多 1,000 個連接
	-	C2 (2.5GB) 快取 - 最多 2,000 個連接
	-	C3 (6 GB) 快取 - 最多 5,000 個連接
	-	C4 (13 GB) 快取 - 最多 10,000 個連接
	-	C5 (26 GB) 快取 - 最多 15,000 個連接
	-	C6 (53 GB) 快取 - 最多 20,000 個連接
-	進階快取
	-	P1 (6 GB - 60 GB) - 最多 7,500 個連接
	-	P2 (13 GB - 130 GB) - 最多 15,000 個連接
	-	P3 (26 GB - 260 GB) - 最多 30,000 個連接
	-	P4 (53 GB - 530 GB) - 最多 40,000 個連接

## Azure Redis 快取中不支援的 Redis 命令

>[AZURE.IMPORTANT] 因為 Azure Redis 快取執行個體的設定與管理是由 Microsoft 管理，所以會停用下列命令。如果嘗試叫用它們，會收到類似 `"(error) ERR unknown command"` 的錯誤訊息。
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	SAVE
>-	SHUTDOWN
>-	SLAVEOF
>-	CLUSTER - 叢集寫入命令已停用，但允許唯讀的叢集命令。

如需 Redis 命令的詳細資訊，請參閱 [http://redis.io/commands](http://redis.io/commands)。

## Redis 主控台

您可以使用 [Redis 主控台] \(可供標準與進階快取使用) 安全地發出命令給您的 Azure Redis 快取執行個體。

>[AZURE.IMPORTANT] Redis 主控台無法使用 VNET 或叢集。
>
>-	[VNET](cache-how-to-premium-vnet.md) - 如果您的快取是 VNET 的一部分，只有在 VNET 中的用戶端可以存取快取。Redis 主控台使用的 redis cli.exe 用戶端裝載於不屬於您 VNET 的 VM 上，因此主控台無法連接到您的快取。
>-	[叢集](cache-how-to-premium-clustering.md) - Redis 主控台使用目前不支援叢集的 redis-cli.exe 用戶端。在 GitHub 的 Redis 存放庫[不穩定](http://redis.io/download)分支中的 redis-cli 公用程式，於使用 `-c` 參數啟動時，會實作基本支援。如需詳細資訊，請參閱 [Redis 叢集教學課程](http://redis.io/topics/cluster-tutorial)中 [http://redis.io](http://redis.io) 上的[試用叢集](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster)。

若要存取 Redis 主控台，請從 [Redis 快取] 刀鋒視窗按一下 [主控台]。

![Redis 主控台](./media/cache-configure/redis-console-menu.png)

只需在主控台中輸入想要的命令，即可對您的快取執行個體發出命令。

![Redis 主控台](./media/cache-configure/redis-console.png)

如需對 Azure Redis 快取所停用之 Redis 命令的清單，請參閱先前的 [Azure Redis 快取中不支援的 Redis 命令](#redis-commands-not-supported-in-azure-redis-cache)一節。如需 Redis 命令的詳細資訊，請參閱 [http://redis.io/commands](http://redis.io/commands)。

## 將您的快取移動到新的訂用帳戶

您也可以按一下 [移動]，將快取移到新的訂用帳戶。

![移動 Redis 快取](./media/cache-configure/redis-cache-move.png)

如需將資源從某一個資源群組移到另一個，以及從某一個訂用帳戶移到另一個的相關資訊，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。

## 後續步驟
-	如需使用 Redis 命令的詳細資訊，請參閱[如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)

<!---HONumber=AcomDC_0629_2016-->