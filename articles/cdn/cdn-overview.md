<properties
	pageTitle="Azure CDN 概觀"
	description="了解何謂 Azure 內容傳遞網路 (CDN)，以及如何使用它透過快取 Blob 和靜態內容來傳遞高頻寬內容。"
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/15/2016" 
	ms.author="casoper"/>

# Azure 內容傳遞網路 (CDN) 概觀

Azure 內容傳遞網路 (CDN) 會在策略性放置的位置上快取網站所使用的 Azure Blob 和靜態內容，以提供最大輸送量來將內容傳遞給使用者。CDN 為開發人員提供一套全球解決方案，以在全球實體節點上快取內容來傳遞高頻寬內容。如需目前的 CDN 節點位置清單，請參閱 [Azure CDN POP 位置](cdn-pop-locations.md)。

使用 CDN 來快取 Azure 資料的優點包括：

- 讓使用者享有更好的效能和使用者經驗，尤其是當使用的應用程式需要反覆存取多次才能載入內容時。
- 可進行大幅調整以更妥善地處理瞬間大量負載 (例如產品上市事件的開始)。
- 透過分散使用者要求以及從 Edge Server 提供內容，傳送至原始來源的流量將會減少。


## 運作方式

![CDN 概觀](./media/cdn-overview/cdn-overview.png)

1. 使用者 (Alice) 使用具有特殊網域名稱的 URL (例如 `<endpointname>.azureedge.net`) 要求檔案 (也稱為資產)。DNS 將要求路由傳送到最佳的存在點 (POP) 位置。這通常是地理位置最接近使用者的 POP。

2. 如果 POP 中的 Edge Server 在其快取中沒有該檔案，Edge Server 便會從原始來源要求檔案。原始來源可以是 Azure Web 應用程式、Azure 雲端服務、Azure 儲存體帳戶或任何可公開存取的 Web 伺服器。

3. 原始來源將檔案傳回給 Edge Server，包括描述檔案存留時間 (TTL) 的選擇性 HTTP 標頭。

4. Edge Server 快取檔案，並將檔案傳回給原始要求者 (Alice)。在 TTL 到期之前，檔案會一直快取在 Edge Server 上。如果原始來源未指定 TTL，預設 TTL 為 7 天。

5. 其他使用者 (例如 Bob) 後來可能會使用相同 URL 要求相同檔案，而且也可能導向至該相同 POP。

6. 如果檔案的 TTL 尚未過期，Edge Server 便會從快取傳回檔案。這會產生更快、更靈敏回應的使用者經驗。


## 標準功能

標準 CDN 層包含下列功能：

- 很容易與[儲存體](cdn-create-a-storage-account-with-cdn.md)、[雲端服務](cdn-cloud-service-with-cdn.md)、[Web Apps](../app-service-web/cdn-websites-with-cdn.md) 和[媒體服務](../media-services/media-services-manage-origins.md#enable_cdn)等 Azure 服務整合
- HTTPS 支援
- 負載平衡
- DDoS 保護
- [查詢字串快取](cdn-query-string.md)
- [自訂網域名稱支援](cdn-map-content-to-custom-domain.md)
- [國家 (地區) 篩選](cdn-restrict-access-by-country.md)
- [核心分析](cdn-analyze-usage-patterns.md)
- [快速清除](cdn-purge-endpoint.md)
- [資產預先載入](cdn-preload-endpoint.md)
- [透過 REST API 管理](https://msdn.microsoft.com/library/mt634456.aspx)


## 進階功能

高階 CDN 層包含所有標準層的功能，以及以下這些額外功能：

- [可自訂的、規則式內容傳遞引擎](cdn-rules-engine.md)
- [進階 HTTP 報告](cdn-advanced-http-reports.md)
- [即時統計資料](cdn-real-time-stats.md)

## 後續步驟

若要開始使用 CDN，請參閱[使用 Azure CDN](./cdn-create-new-endpoint.md)。

現有的 CDN 客戶現在可以透過 [Microsoft Azure 入口網站](https://portal.azure.com)管理您的 CDN 端點。

若要查看作用中的 CDN，請參閱 [Build 2016 會議的影片](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/)。

<!---HONumber=AcomDC_0504_2016-->