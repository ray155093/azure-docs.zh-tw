---
title: 使用 Application Insights 入口網站
description: Application Insights 入口網站中的計量、搜尋、儀表板與設定。
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 07/30/2016
ms.author: awills

---
# Application Insights 入口網站中的導覽與儀表板
[在您的專案上設定 Application Insights](app-insights-overview.md) 之後，有關應用程式效能和使用情況的遙測資料會出現在 [Azure 入口網站](https://portal.azure.com)之專案的 Application Insights 資源中。

## 尋找遙測
登入 [Azure 入口網站](https://portal.azure.com)，並瀏覽至您為應用程式建立的 Application Insights 資源。

![按一下 [瀏覽]，選取 [Application Insights]，然後選取您的應用程式。](./media/app-insights-dashboards/00-start.png)

[概觀] 頁面可提供您一些基本的遙測以及連結等。內容取決於您的應用程式類型，並且可以自訂。

## 應用程式概觀刀鋒視窗
應用程式的 [概觀] 刀鋒視窗 (頁面) 會顯示您應用程式的關鍵診斷資料，且為入口網站其他功能的閘道。

按一下：

* **任何圖表或圖格**，以查看更多關於圖表上所顯示內容的詳細資訊。
* **設定**，以取得其他計量預先定義的刀鋒視窗以及組態頁面。
* [計量瀏覽器](app-insights-metrics-explorer.md) 來建立您自己選擇的計量頁面。
* [搜尋](app-insights-diagnostic-search.md) 調查特定事件的執行個體，例如要求、例外狀況或紀錄追蹤。
* [分析](app-insights-analytics.md) 來取得針對遙測的強大查詢。
* [**即時資料流**](app-insights-metrics-explorer.md#live-stream)，適用於一組固定且幾近即時的計量，在部署新組建或偵錯時非常實用。

![檢視您的遙測的主要路由](./media/app-insights-dashboards/010-oview.png)

### 自訂 [概觀] 刀鋒視窗
選擇您要在 [概觀] 上查看的內容。您可以在 [自訂] 中插入區段標題、拖曳磚和圖表、移除項目以及從組件庫中加入新的磚和圖表。

![按一下 [編輯]。拖曳磚和圖表。從組件庫加入磚。然後按一下 [完成]。](./media/app-insights-dashboards/020-customize.png)

## 儀表板
您登入 [Microsoft Azure 入口網站](https://portal.azure.com)之後最先看到的是儀表板。您可以在這裡結合對您而言最重要的所有 Azure 資源的圖表，包括來自 [Visual Studio Application Insights](app-insights-overview.md) 的遙測。

![自訂的儀表板。](./media/app-insights-dashboards/31.png)

1. 使用儀表板工具列來編輯、建立和共用儀表板。
2. 回到目前的儀表板，或切換至其他最近的檢視。
3. 切換儀表板。
4. 建立、編輯和共用儀表板。
5. 將滑鼠停留在圖格上，然後使用其頂端列加以移動、自訂或移除。

## 加入儀表板中
當您尋找特別感興趣的刀鋒視窗或一組圖表時，您可將其釘選一份到儀表板。您將會在下次返回該處時見到它。

![若要釘選圖表，可將滑鼠停留在其上方，然後按一下標頭中的 [...]。](./media/app-insights-dashboards/33.png)

1. 將圖表釘選到儀表板。圖表的複本會出現在儀表板上。
2. 將整個刀鋒視窗釘選到儀表板 - 它會在儀表板上顯示為圖格以供您點選。
3. 按一下左上角以返回目前的儀表板。然後您可以使用下拉式功能表，返回目前的檢視。

請注意，圖表會分組為圖格︰一個圖格中可包含多個圖表。您將整個圖格釘選至儀表板。

### 分析圖表
您也可以[釘選分析](app-insights-analytics-using.md#pin-to-dashboard)圖表到[共用](#share-dashboards-with-your-team)儀表板。這可讓您新增任意查詢以及標準度量的圖表。

## 調整儀表板上的圖格
圖格在儀表板上之後，您就可以進行調整。

![暫留在一個圖表，以進行編輯。](./media/app-insights-dashboards/36.png)

1. 將圖表加入圖格中。
2. 設定度量、分組依據的維度，和圖表的樣式 (資料表、圖形)。
3. 針對圖格上的圖表設定時間範圍和篩選屬性。您可以設定圖表自己的時間範圍，或繼承自儀表板的時間範圍。
4. 設定圖格的標題。

從計量瀏覽器刀鋒釘選的圖格，編輯選項會比從 [概觀] 刀鋒視窗釘選的圖格多。

您釘選的原始圖格不會受到您的編輯影響。

## 切換儀表板
您可以儲存多個儀表板並在之間進行切換。當您釘選圖表或刀鋒視窗時，即會將它們加入目前的儀表板。

![若要在儀表板間進行切換，按一下 [儀表板]，然後選取已儲存的儀表板。若要建立並儲存新的儀表板，按一下 [新增]。若要重新排列，按一下 [編輯]。](./media/app-insights-dashboards/32.png)

例如，您可能必須使用一個儀表板，在小組室中以全螢幕顯示，並使用另一個儀表板進行一般開發。

在儀表板上，刀鋒視窗會顯示為磚：按一下即可移至刀鋒視窗。圖表會複製其原始位置中的圖表。

![按一下圖格以開啟它所代表的刀鋒視窗](./media/app-insights-dashboards/35.png)

## 與小組共用儀表板
建立儀表板之後，您可以與其他使用者共用它。

![在儀表板標題中按一下 [共用]](./media/app-insights-dashboards/41.png)

深入了解[角色和存取控制](app-insights-resources-roles-access-control.md)。

## Settings
[概觀] 刀鋒視窗上的 [設定] 按鈕不只與組態有關，也提供一種方式來轉換為數種圖表類型。

* **稽核記錄檔** - 檢閱已在您的應用程式資源上執行的警示和動作。
* [新增支援要求](app-insights-get-dev-support.md) - 適用於 Azure 各方面的協助選項。針對 Application Insights，您也可以使用視窗右上方的笑臉按鈕來取得說明。
* [應用程式對應](app-insights-dependencies.md#application-map) - 顯示應用程式元件的使用中對應，其衍生自相依性資訊。
* [即時計量串流](app-insights-metrics-explorer.md#live-metrics-stream) - 約有 1 秒延遲的關鍵計量，可用來監視新版本。
* **失敗** - 失敗的要求、失敗的相依性呼叫和例外狀況。[可編輯的計量刀鋒視窗](app-insights-metrics-explorer.md)。
* **效能** - 回應時間、相依性回應時間。[可編輯的計量刀鋒視窗](app-insights-metrics-explorer.md)。
* [伺服器](app-insights-web-monitor-performance.md) - 效能計數器。當您[安裝狀態監視器](app-insights-monitor-performance-live-website-now.md)時適用。
* **瀏覽器** - 頁面檢視和 AJAX 效能。當您[檢測網頁](app-insights-javascript.md)時適用。
* **使用量** - 頁面檢視、使用者和工作階段計數。當您[檢測網頁](app-insights-javascript.md)時適用。
* **開始使用** - 內嵌教學課程。
* **屬性** - 檢測金鑰、訂用帳戶和資源識別碼。
* [可用性](app-insights-monitor-web-app-availability.md) - Web 測試組態和結果。
* [警示](app-insights-alerts.md) - 計量警示組態。
* [連續匯出](app-insights-export-telemetry.md) - 設定將遙測匯出至 Azure 儲存體。
* [效能測試](app-insights-monitor-web-app-availability.md#performance-tests) - 設定您的網站上的模擬負載。
* [配額和定價](app-insights-pricing.md)和[擷取取樣](app-insights-sampling.md)。
* **API 存取** - 目前用來建立 [版本註解](app-insights-annotations.md)以及資料存取 API。
* [**工作項目**](app-insights-diagnostic-search.md#create-work-item) - 連線到工作追蹤系統，讓您可以在檢查遙測時建立 Bug。
* [**使用者**](app-insights-resources-roles-access-control.md) - 管理可存取您應用程式資源的人員。
* [**角色**](app-insights-resources-roles-access-control.md) - 管理使用者可以執行的動作。
* [**標記**](../resource-group-using-tags.md) - 組織 Azure 資源。
* [**鎖定**](../resource-group-lock-resources.md) - 鎖定 Azure 資源
* [**匯出範本**](app-insights-powershell.md) - 匯出 Azure 資源的定義，如此您就能用其做為範本來建立新資源。

## 後續步驟
|  |  |
| --- | --- |
| [計量瀏覽器](app-insights-metrics-explorer.md)<br/>篩選與分割計量 |![搜尋範例](./media/app-insights-dashboards/64.png) |
| [診斷搜尋](app-insights-diagnostic-search.md)<br/>尋找和檢查事件、相關的事件，並建立 Bug |![搜尋範例](./media/app-insights-dashboards/61.png) |
| [分析](app-insights-analytics.md)<br/>功能強大的查詢語言 |![搜尋範例](./media/app-insights-dashboards/63.png) |

<!---HONumber=AcomDC_0817_2016-->