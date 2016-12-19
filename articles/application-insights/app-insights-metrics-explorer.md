---
title: "在 Application Insights 中探索度量 | Microsoft Docs"
description: "如何解譯計量瀏覽器上的圖表，以及如何自訂計量瀏覽器刀鋒視窗。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 040556d42f246ab2876cc5bab031036ec78e13bc


---
# <a name="exploring-metrics-in-application-insights"></a>在 Application Insights 中探索度量
[Application Insights][start] 中的度量為從您的應用程式傳送的遙測中的測量值和事件計數。 它們幫助您偵測效能問題，並觀察使用應用程式方式的趨勢。 標準度量的範圍很廣泛，而您也可以建立自己的自訂度量和事件。

度量和事件計數會顯示在彙總值 (例如總和、平均或計數) 的圖表中。

以下是範例圖表：

![開啟 Azure 入口網站中應用程式的 [概觀] 刀鋒視窗](./media/app-insights-metrics-explorer/01-overview.png)

有些圖表經過分段：任何點的圖表高度總計為顯示的度量的總合。 圖例預設會顯示最大數量。

虛線顯示前一週度量的值。

## <a name="time-range"></a>時間範圍
您可以在任何刀鋒視窗上變更圖表或格線涵蓋的時間範圍。

![開啟 Azure 入口網站中應用程式的 [概觀] 刀鋒視窗](./media/app-insights-metrics-explorer/03-range.png)

如果您預期的部分資料尚未出現，請按一下 [重新整理]。 圖表本身會定期重新整理，但是時間範圍越大，間隔時間會越長。 在發行模式中，資料從分析管線往圖表上顯示可能需要一些時間。

若要放大圖表的局部，請拖曳過該部分︰

![拖曳過圖表的一部分。](./media/app-insights-metrics-explorer/12-drag.png)

按一下 [復原縮放] 按鈕可將它還原。

## <a name="granularity-and-point-values"></a>資料粒度和點值
將滑鼠移至圖表上可顯示該點度量的值。

![將滑鼠移至圖表上](./media/app-insights-metrics-explorer/02-focus.png)

特定點的度量值會繼著前一個取樣間隔而彙總。

取樣間隔或「資料粒度」會顯示在刀鋒視窗的頂端。

![刀鋒視窗的標題。](./media/app-insights-metrics-explorer/11-grain.png)

您可以在時間範圍刀鋒視窗中調整資料粒度：

![刀鋒視窗的標題。](./media/app-insights-metrics-explorer/grain.png)

可提供的資料粒度取決於您選取的時間範圍。 明確的資料粒度可替代時間範圍內的「自動」資料粒度。

## <a name="metrics-explorer"></a>計量瀏覽器
按一下 [概觀] 刀鋒視窗上的任何圖表，可查看更詳細的一組相關圖表和格線。 您可以編輯這些圖表和格線，以著重在感興趣的詳細資料。

或者，您也可以按一下概觀刀鋒視窗頂端的 [計量瀏覽器] 按鈕。

例如，逐一點選 Web 應用程式的「失敗的要求」圖表：

![在 [概觀] 刀鋒視窗上，按一下圖表](./media/app-insights-metrics-explorer/14-trix.png)

## <a name="what-do-the-figures-mean"></a>數據代表意義為何？
旁邊的圖例通常預設會顯示圖表在這段期間的彙總值。 如果您將滑鼠停留在圖表上，它會顯示在該點的值。

圖表上的每個資料點是在先前取樣間隔或「資料粒度」中所收到的資料值彙總。 資料粒度會顯示在刀鋒視窗頂端，並隨著圖表的時幅而有所不同。

度量可以用不同方式彙總：

* **總和** ：將取樣間隔或圖表期間收到的所有資料點的值相加。
* **平均** ：將總和除以間隔期間收到的資料點數目。
* **唯一** ：計數會用於使用者及帳戶的計數。 在取樣間隔或圖表期間，圖形顯示在該時間看到的不同使用者的計數。

您可以變更彙總方法：

![選取圖表，然後選取彙總](./media/app-insights-metrics-explorer/05-aggregation.png)

當您建立新的圖表或所有度量皆已取消選取時，會顯示每個度量的預設方法：

![取消選取所有度量以查看預設值](./media/app-insights-metrics-explorer/06-total.png)

## <a name="editing-charts-and-grids"></a>編輯圖表和格線
若要對刀鋒視窗加入新圖表：

![在 [計量瀏覽器] 中，選擇 [加入圖表]](./media/app-insights-metrics-explorer/04-add.png)

選取現有或新圖表上的 [編輯]  來編輯其顯示的內容：

![選取一或多個度量](./media/app-insights-metrics-explorer/08-select.png)

您可以在圖表上顯示一個以上的度量，不過，可以一起顯示的組合有一些限制。 只要您選取一個度量，便會停用某些其他度量。

如果您編寫[自訂度量][track]到您的應用程式 (對 TrackMetric 和 TrackEvent 呼叫)，會在這裡予以列出。

## <a name="segment-your-data"></a>分段資料
您可以依照屬性分割度量 - 例如，比較使用不同作業系統的用戶端上的頁面檢視。

選取圖表或格線，將分組切換成開啟，並選取要分組依據的屬性：

![選取 [分組開啟]，然後在 [群組依據] 中選取屬性](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> 當您使用群組時，[區域] 及 [橫條圖] 類型會提供堆疊的顯示。 這適合於 [彙總] 方法是 [加總] 的情況。 但如果彙總類型是 [平均]，則選擇 [線條] 或[格線] 顯示類型。
>
>

如果您將[自訂度量][track]編寫至您的應用程式，並且它們包含屬性值，將可以在清單中選取屬性。

圖表是否對分段的資料來說太小？ 調整其高度：

![調整滑桿](./media/app-insights-metrics-explorer/18-height.png)

## <a name="filter-your-data"></a>篩選資料
若只要查看選取的一組屬性值的度量：

![按一下 [篩選器]，然後檢查一些值](./media/app-insights-metrics-explorer/19-filter.png)

如果您不為特定屬性選取任何值，這與將它們全選相同：該屬性上沒有篩選器。

請注意每個屬性值旁的事件計數。 選取一個屬性的值時，會調整計數與其他屬性值。

篩選會套用至刀鋒視窗上的所有圖表。 如果您要將不同的篩選套用到不同的圖表，請建立並儲存不同的計量刀鋒視窗。 如果想要，您可以將不同刀鋒視窗中的圖表釘選到儀表板，以便並排查看兩者。

### <a name="remove-bot-and-web-test-traffic"></a>移除 Bot 和 Web 測試流量
使用篩選器**真實或綜合流量**並勾選**真實**。

您也可以依 **綜合流量的來源**篩選。

### <a name="to-add-properties-to-the-filter-list"></a>將屬性加入篩選器清單
您要根據自己選擇的類別篩選遙測嗎？ 例如，您可能將使用者劃分成不同的類別，且您想要依照這些類別來分割資料。

[建立您自己的屬性](app-insights-api-custom-events-metrics.md#properties)。 在 [遙測初始設定式](app-insights-api-custom-events-metrics.md#defaults) 中設定，以使其顯示在所有遙測中 - 包括不同 SDK 模組所傳送的標準遙測。

## <a name="edit-the-chart-type"></a>編輯圖表類型
請注意您可以在格線與圖形之間切換：

![選取格線或圖表，然後選擇圖表類型](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>儲存您的度量刀鋒視窗
建立一些圖表後，請將它們儲存為我的最愛。 如果您使用組織帳戶，可以選擇是否要將它與他小組成員分享。

![選擇 [我的最愛]](./media/app-insights-metrics-explorer/21-favorite-save.png)

若要再次查看分頁，請 **前往 [概觀] 分頁** ，並開啟 [我的最愛]：

![在 [概觀] 刀鋒視窗中，選擇 [我的最愛]](./media/app-insights-metrics-explorer/22-favorite-get.png)

如果儲存時選擇「相對」時間範圍，會以最新度量更新刀鋒視窗。 如果選擇「絕對」時間範圍，它會每次都顯示相同資料。

## <a name="reset-the-blade"></a>重設刀鋒視窗
如果您編輯刀鋒視窗，但之後想要回到原始儲存的集合，只需要按一下 [重設]。

![在 [計量瀏覽器] 上方的按鈕中](./media/app-insights-metrics-explorer/17-reset.png)

<a name="live-metrics-stream"></a>

## <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>即時計量串流：適用於關閉監視的即時計量
即時計量串流說明您的應用程式計量正處於這個非常時刻，接近即時的延遲為 1 秒。 當您發行了新的組建且想要確定一切都如預期般運作，或者要即時調查某個事件時，這是非常有用的。

![在 [概觀] 刀鋒視窗中，按一下 [即時資料流]](./media/app-insights-metrics-explorer/live-stream.png)

不同於計量瀏覽器，即時計量串流會顯示一組固定的計量。 只要資料仍在圖表上就會保存，之後便會捨棄該資料。

### <a name="live-failures"></a>即時失敗

如果有記錄任何失敗或例外狀況，「即時資料流」就會對其進行取樣。 按一下 [暫停] 以保存特定樣本，然後選取事件來顯示其詳細資料。

![取樣的即時失敗](./media/app-insights-metrics-explorer/live-stream-failures.png)


[Application Insights SDK for Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的最新版本有提供「即時計量資料流」。

## <a name="set-alerts"></a>設定警示
若要在任何度量有不尋常的值時收到電子郵件通知，請加入警示。 您可以選擇將電子郵件傳送給帳戶管理員，或傳送給特定的電子郵件地址。

![在 [計量瀏覽器] 中，選擇 [警示規則]，然後選擇 [加入警示]](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[深入了解警示][alerts]。

## <a name="export-to-excel"></a>匯出至 Excel
您可以將 [計量瀏覽器] 中顯示的度量資料匯出到 Excel 檔案。 匯出的資料包括入口網站中所示所有圖表和資料表的資料。

![在 [計量瀏覽器] 中，選擇 [警示規則]，然後選擇 [加入警示]](./media/app-insights-metrics-explorer/31-export.png)

每個圖表或資料表的資料都會匯出到 Excel 檔案中的個別工作表。

您看到的內容即為匯出的內容。 如果您想要變更所匯出資料的範圍，請變更時間範圍或篩選器。 對於資料表，如果有顯示 [ **載入更多** ] 命令，您可以先按它再按一下 [匯出]，以匯出更多資料。

*匯出動作目前僅適用於 Internet Explorer 和 Chrome。我們正努力新增其他瀏覽器的支援。*

## <a name="continuous-export"></a>連續匯出
如果您想要連續匯出資料，讓您能夠在外部加以處理，請考慮使用 [連續匯出](app-insights-export-telemetry.md)。

### <a name="power-bi"></a>Power BI
如果您希望更完整地檢視您的資料，您可以 [匯出至 Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)。

## <a name="analytics"></a>Analytics
[分析](app-insights-analytics.md) 是使用強大的查詢語言來分析遙測的更靈活方式。 如果您想要合併或計算計量的結果，或執行您的應用程式近期效能的深入探索，請使用它。 另一方面，如果您想要自動重新整理儀表板上的圖表以及警示，請使用計量瀏覽器。

## <a name="troubleshooting"></a>疑難排解
*我看不到我的圖表上的任何資料。*

* 篩選會套用至刀鋒視窗上的所有圖表。 請確定，當您將焦點放在某個圖表時，您未在其他圖表上設定會排除所有資料的篩選。

    如果您想要在不同的圖表上設定不同的篩選，請在不同的刀鋒視窗中建立圖表，將它們儲存為個別的最愛圖表。 如果想要，您可以將這些圖表釘選到儀表板，以便並排查看兩者。
* 如果您依據計量上未定義的屬性將圖表分組，則圖表上不會有任何資料。 請嘗試清除 [分組依據]，或選擇不同的群組屬性。
* 效能資料 (CPU、IO 速率等等) 適用於 Java Web 服務、Windows 傳統型應用程式、[IIS Web 應用程式和服務 (若您安裝狀態監視器)](app-insights-monitor-performance-live-website-now.md) 和 [Azure 雲端服務](app-insights-azure.md)。 它不適用於 Azure 網站。

## <a name="next-steps"></a>後續步驟
* [使用 Application Insights 監視使用情況](app-insights-overview-usage.md)
* [使用診斷搜尋](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md



<!--HONumber=Nov16_HO3-->


