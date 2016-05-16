<properties
	pageTitle="CDN - 檔案壓縮疑難排解"
	description="針對 CDN 檔案壓縮的問題進行疑難排解。"
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
	ms.topic="article"
	ms.date="04/28/2016" 
	ms.author="casoper"/>
    
# CDN 檔案壓縮疑難排解

這篇文章可協助您針對 [CDN 檔案壓縮](cdn-improve-performance.md)的問題進行疑難排解。

若您對本文中的任何步驟有需要進一步協助的地方，請連絡 [MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [取得支援]。

## 徵狀

已為您的端點啟用壓縮，但會傳回未壓縮的檔案。

## 原因

有幾個可能的原因，包括︰

- 要求的內容不適合進行壓縮。
- 要求的檔案類型未啟用壓縮。
- HTTP 要求未包含要求有效壓縮類型的標頭。

## 疑難排解步驟

> [AZURE.TIP] 隨著新端點的部署，CDN 組態變更會需要一些時間才能傳播至整個網路。在大部分情況下，變更會在 90 分鐘內套用完成。如果這是您第一次設定 CDN 端點壓縮，您應該考慮先等候 1-2 小時，確定壓縮設定已傳播至 POP。

### 驗證要求

首先，我們應該對要求進行快速的例行性檢查。您可以使用瀏覽器的[開發人員工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)來檢視所做的要求。

- 驗證要求正傳送至您的端點 URL `<endpointname>.azureedge.net`，而不是您的來源。
- 驗證要求包含 **Accept-Encoding** 標頭，且該標頭的值包含 **gzip**、**deflate** 或 **bzip2**。

![CDN 要求標頭](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### 驗證壓縮設定 (標準 CDN 設定檔)

> [AZURE.NOTE] 此步驟僅適用於您的 CDN 設定檔位於**標準**定價層的情況。

在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的端點，然後按一下 [設定] 按鈕。

- 驗證已啟用壓縮。
- 驗證要壓縮之內容的 MIME 類型已包含在壓縮格式清單中。

![CDN 壓縮設定](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### 驗證壓縮設定 (進階 CDN 設定檔)

> [AZURE.NOTE] 此步驟僅適用於您的 CDN 設定檔位於**進階**定價層的情況。

在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的端點，然後按一下 [管理] 按鈕。即會開啟補充入口網站。將滑鼠移至 [**HTTP 大型**] 索引標籤上，然後將滑鼠移至 [**快取設定**] 飛出視窗上。按一下 [**壓縮**]。

- 驗證已啟用壓縮。
- 驗證 [檔案類型] 清單包含以逗號分隔 (無空格) 的 MIME 類型清單。
- 驗證要壓縮之內容的 MIME 類型已包含在壓縮格式清單中。

![CDN 進階壓縮設定](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### 驗證已快取內容

使用您瀏覽器的開發人員工具，檢查回應標頭以確保檔案會快取在要求它的區域中。

- 檢查 **Server** 回應標頭。標頭應該具有格式 **平台 (POP/伺服器識別碼)**，如以下範例所示。
- 檢查 **X-Cache** 回應標頭。標頭應為 **HIT**。  

![CDN 回應標頭](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### 驗證檔案符合大小需求

若要進行壓縮，檔案必須符合下列的大小需求︰

- 超過 128 個位元組。
- 小於 1 MB。

<!---HONumber=AcomDC_0504_2016-->