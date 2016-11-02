<properties
    pageTitle="Microsoft Azure 中的度量概觀 | Microsoft Azure"
    description="Microsoft Azure 中的度量及其用法概觀"
    authors="kamathashwin"
    manager=""
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="ashwink"/>


# <a name="overview-of-metrics-in-microsoft-azure"></a>Microsoft Azure 中的度量概觀 

本文章說明何謂 Microsoft Azure 中的度量、其優點，以及如何開始使用它們。  

## <a name="what-are-metrics?"></a>何謂度量？

Azure 監視器可讓您取用遙測來查看您 Azure 工作負載的效能與健全狀況。 Azure 遙測資料最重要的類型是由大多數 Azure 資源所發出的度量 (也稱為效能計數器)。 Azure 監視器提供數種方式可設定及取用這些度量進行監視與疑難排解。


## <a name="what-can-you-do-with-metrics?"></a>度量能讓您做什麼？

度量是遙測的寶貴來源，可讓您執行下列動作：

- **追蹤資源** (例如 VM、網站或邏輯應用程式) 的效能，方法是在入口網站圖表上繪製其度量，並將該圖表釘選到儀表板。
- **取得問題的通知** ，該問題在度量超出特定的閾值時會影響資源的效能。
- **設定自動化動作**，例如自動調整資源，或在度量超出特定的閾值時觸發 Runbook。
- **執行進階分析** 或報告您資源的效能或使用量趨勢。
- **封存**您資源的效能或健全狀況歷程記錄以用於**相容性/稽核**。

##  <a name="metric-characteristics"></a>度量特性
度量具有下列特性︰

- 所有度量皆有 **1 分鐘的頻率**。 您每分鐘會從您的資源收到度量值，讓您可幾乎即時地看到資源的狀態和健全狀況。
- 度量是 **現成可用的，不需要選擇加入** 或設定額外的診斷。
- 您可以針對每個度量存取 **30 天的歷程記錄** 。 您可以快速查看最近和每個月的資源效能或健全狀況趨勢。

您可以：

- 當您選取資源並將資源繪製在圖表上時，透過 Azure 入口網站輕鬆探索、存取及 **檢視所有度量** 。 
- 設定當度量超出您設定的閾值時， **會傳送通知或採取自動化動作的度量警示規則** 。 自動調整是一種特殊的自動化動作，可讓您相應放大資源來符合您網站或計算資源的連入要求或負載。 您可以設定自動調整設定規則，根據超出閾值的度量相應放大/縮小。
- **封存** 度量來延長時間或用於離線報告。 當您設定資源的診斷設定時，可以將度量路由至 Blob 儲存體。
- **串流** 度量到事件中樞，可讓您將它們路由至 Azure 串流分析或自訂應用程式，以進行近乎即時的分析。 您可以使用診斷設定。
- **路由** 所有度量至 OMS Log Analytics，以解除鎖定即時分析、搜尋及自訂來自您資源的度量資料警示。
- **取用** 度量。
- **查詢** 度量。

 ![Azure 監視器中的度量路由](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>透過入口網站存取度量
以下是透過 Azure 入口網站建立度量圖表的快速逐步解說

### <a name="view-metrics-after-creating-a-resource"></a>建立資源之後檢視度量
1. 開啟 Azure 入口網站
2. 建立新的 App Service - 網站。
3. 建立網站之後，請移至網站的 [概觀] 刀鋒視窗。
4. 您可以「監視」圖格檢視新度量。 您可以編輯圖格並選取更多度量

 ![Azure 監視器中的資源度量](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>在單一位置存取所有度量
1. 開啟 Azure 入口網站 
2. 瀏覽至新的 [監視] 索引標籤，然後選取其下方的 [度量] 選項 
3. 您可以從下拉式清單選取您的訂用帳戶、資源群組和資源的名稱。 
4. 您現在可以檢視可用的度量清單。 
5. 選取您感興趣的度量並繪製它。 
6. 您可以按一下右上角的 [釘選]，將它釘選至儀表板。

 ![在 Azure 監視器中的單一位置存取所有度量](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] 您可以從 VM (以 Azure Resource Manager 為基礎) 存取主機層級的度量和 VM 擴展集，而不需要任何額外的診斷設定。 這些新的主機層級度量可供 Windows 和 Linux 執行個體使用。 這些度量不會與您在 VM 或 VMSS 上開啟 Azure 診斷時具有存取權的客體 OS 層級度量混淆。 若要深入了解設定 Azure 診斷，請參閱 [什麼是 Microsoft Azure 診斷](../azure-diagnostics.md)。

## <a name="access-metrics-via-rest-api"></a>透過 REST API 存取度量
可以透過 Azure 監視器 API 存取 Azure 度量。 有兩個 API 可協助您探索及存取度量。 使用： 

- [Azure 監視器度量定義 REST API](https://msdn.microsoft.com/library/mt743621.aspx) 來存取可供服務使用的度量清單。
- [Azure 監視器度量 REST API](https://msdn.microsoft.com/library/mt743622.aspx) 來存取實際的度量資料

>[AZURE.NOTE] 本文章透過 Azure 資源的 [新度量 API](https://msdn.microsoft.com/library/dn931930.aspx) 涵蓋度量。 新度量定義 API 的 API 版本為 2016-03-01，度量 API 的版本為 2016-09-01。 可以使用 API 版本 2014-04-01 存取舊版的度量定義和度量。

如需使用 Azure 監視器 REST API 的更詳細逐步解說，請參閱 [Azure 監視器 REST API 逐步解說](monitoring-rest-api-walkthrough.md)。

## <a name="export-options-for-metrics"></a>度量的匯出選項
您可以移至 [監視] 索引標籤下的 [診斷記錄檔] 刀鋒視窗，並檢視度量的匯出選項。 針對本文中先前所述的使用案例，您可以選取要傳送至 Blob 儲存體、事件中樞或 OMS Log Analytics 的度量 (和診斷記錄檔)。 

 ![在 Azure 監視器中匯出度量選項](./media/monitoring-overview-metrics/MetricsOverview3.png)   

您可以透過 Resource Manager 範本、[PowerShell](insights-powershell-samples.md)、[CLI](insights-cli-samples.md) 或 [REST API](https://msdn.microsoft.com/library/dn931943.aspx) 來進行設定。 

## <a name="take-action-on-metrics"></a>採取度量動作
您可以接收關於度量資料的通知或採取度量資料的動作。 若要這樣做，您可以設定警示規則或自動調整設定。

### <a name="alert-rules"></a>警示規則
您可以設定度量的警示規則。 這些警示規則可以檢查度量是否已超過某個閥值，並透過電子郵件或引發可用於執行任何自訂指令碼的 Webhook 來通知您。 您也可以使用 Webhook 來設定第 3 個產品整合。

 ![Azure 監視器中的度量和警示規則](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Autoscale
某些 Azure 資源可支援多個執行個體，相應放大或縮小來處理您的工作負載。 自動調整套用至 App Services (Web 應用程式)、虛擬機器擴展集 (VMSS) 和傳統雲端服務。 您可以設定自動調整規則，以在影響您工作負載的特定度量超出您所指定的閾值時相應放大或縮小。 如需詳細資訊，請參閱 [自動調整的概觀](monitoring-overview-autoscale.md)。

 ![Azure Monitor 中的度量和自動調整](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>支援的服務和度量
Azure 監視器是新的度量基礎結構。 它支援下列 Azure 入口網站中的 Azure 服務和新版本的 Azure 監視器 API︰

- VM (以 Azure Resource Manager 為基礎)
- VM 擴展集
- 批次
- 事件中樞命名空間 
- 服務匯流排命名空間 (僅限進階 SKU)
- SQL (版本 12)
- SQL 彈性集區
- 網站
- Web 伺服器陣列
- Logic Apps
- IoT 中樞
- Redis 快取
- 網路：應用程式閘道
- 搜尋

您可以在 [Azure 監視器度量 - 每個資源類型支援的度量](monitoring-supported-metrics.md)檢視所有支援服務及其度量的詳細清單。 


## <a name="next-steps"></a>後續步驟

請參閱這整篇文章的連結。 此外，了解：  

- 關於 [自動調整的常用度量](insights-autoscale-common-metrics.md)
- 如何 [建立警示規則](insights-alerts-portal.md)







<!--HONumber=Oct16_HO2-->


