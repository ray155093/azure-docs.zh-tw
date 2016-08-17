<properties
	pageTitle="在 Visual Studio 中分析趨勢 | Microsoft Azure"
	description="在 Visual Studio 中分析、 視覺化及探索您的 Application Insights 遙測的趨勢。"
	services="application-insights"
    documentationCenter=".net"
	authors="numberbycolors"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/14/2016"
	ms.author="daviste"/>

# 在 Visual Studio 中分析趨勢

「Application Insights 趨勢」工具會以視覺化方式呈現應用程式的重要遙測事件如何隨著時間變更，協助您快速識別問題和異常。將您連結至更詳細的診斷資訊，「趨勢」可協助您改善應用程式效能、追蹤例外狀況的原因，以及揭露您的自訂事件情資。

![範例趨勢視窗](./media/app-insights-trends/app-insights-trends-hero-750.png)

> [AZURE.NOTE] Application Insights 趨勢可用於 Visual Studio 2015 Update 3 及更新版本，或適用於[開發人員分析工具擴充功能](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) 5.209 版和更新版本。

## 開啟 Application Insights 趨勢

您可以使用下列方法之一開啟 [Application Insights 趨勢] 視窗︰

* 從 Application Insights 工具列按鈕，選擇 [探索遙測趨勢]。
* 從專案內容功能表，選擇 **[Application Insights] > [探索遙測趨勢]**。
* 從 Visual Studio 功能表列，選擇 [檢視] > [其他視窗] > [Application Insights 趨勢]。

您可能會看到選取資源的提示。若是如此，按一下 [選取資源]，登入 Azure 訂用帳戶，然後選擇您要分析遙測趨勢的 Application Insights 資源。

## 選擇趨勢分析

![趨勢分析的常見類型功能表](./media/app-insights-trends/app-insights-trends-1-750.png)

首先選擇其中一個通用趨勢分析 (共 5 個)，而這些通用趨勢分析會各自分析過去 24 小時內的資料︰

* **調查伺服器要求的效能問題**：對您的服務所做的要求 (依回應時間分組)
* **分析伺服器要求中的錯誤**：對您的服務所做的要求 (依 HTTP 回應碼分組)
* **檢查應用程式中的例外狀況**：您的服務的例外狀況 (依例外狀況類型分組)
* **檢查應用程式相依項目的效能**：您的服務所呼叫的服務 (依回應時間分組)
* **檢查您的自訂事件**：您為服務設定的自訂事件 (依事件類型分組)

稍後可從 [趨勢] 視窗左上角的 [檢視常見的遙測分析類型] 按鈕取得這些預先建置的分析。

## 以視覺化方式呈現您的應用程式的趨勢

Application Insights 趨勢會從您的應用程式的遙測建立時間序列視覺效果。每一個時間序列視覺效果會顯示特定時間範圍內一種類型的遙測 (依該遙測的其中一個屬性分組)。

例如，您可能想要檢視過去 24 小時內的伺服器要求 (依源自的國家/地區分組)。在此範例中，視覺效果上的每一個泡泡代表在一小時內某一特定國家/地區的伺服器要求計數。

使用視窗頂端的控制項來調整您可檢視的遙測類型。首先，選擇您感興趣的遙測類型︰

* **遙測類型**：伺服器要求、例外狀況、相依性或自訂事件
* **時間範圍**：從過去 30 分鐘到過去 3 天的任何時間
* **分組依據**：例外狀況類型、問題識別碼、國家/區域等等

然後，按一下 [分析遙測] 來執行查詢。

若要在視覺效果中的泡泡之間巡覽︰

* 按一下以選取泡泡，該泡泡會更新視窗底部的篩選器，彙整在特定期間內發生的事件。
* 按兩下泡泡以瀏覽至「搜尋」工具，並查看在該時間內發生的所有個別遙測事件。
* 按住 **CTRL** 鍵並按一下泡泡，以在視覺效果中取消選取該泡泡。

> [AZURE.TIP] 「趨勢」和「搜尋」工具協助您找出對服務造成問題的遙測事件。例如，如果您的客戶注意到應用程式某天下午回應較慢，您便可以開始以「趨勢」分析問題。分析在過去幾個小時內對您的服務所做的要求 (依回應時間分組)。查看是否有非常大量的慢速要求。然後連按兩下該泡泡來移至「搜尋」工具 (已篩選成這些要求事件)。在「搜尋」中，您可以探索這些要求的內容並尋找相關程式碼以解決問題。

## 篩選特定的趨勢

使用視窗底部的篩選控制項，探索更明確的趨勢。若要套用篩選器，請按一下其名稱。您可以快速切換不同的篩選器，以探索可能隱藏在遙測的特定維度中的趨勢。如果您在一個維度 (如例外狀況類型維度) 中套用一個篩選器，則其他維度中的篩選器即使呈現灰色，仍然可加以點選。若要取消套用篩選器，請再按一次。按住 **CTRL** 鍵並按一下滑鼠，以選取相同維度中的多個篩選器。

![趨勢篩選器](./media/app-insights-trends/TrendsFiltering-750.png)

如果您想要套用多個篩選器，該怎麼辦？

1. 套用第一個篩選器。
2. 按一下第一個篩選器的維度名稱旁邊的 [套用選取的篩選器並再次查詢] 按鈕。這會重新查詢您的遙測中符合第一個篩選條件的事件。
3. 套用第二個篩選器。
4. 重複此程序以在遙測的特定子集中尋找趨勢。例如，您可以查詢名為 "GET Home/Index" 且來自德國並收到 500 狀態碼的伺服器要求。

若要取消套用上述其中一個篩選器，請按一下維度的 [移除選取的篩選器並再次查詢] 按鈕。

![多個篩選器](./media/app-insights-trends/TrendsFiltering2-750.png)

## 尋找異常

「趨勢」工具會醒目提示異常的事件泡泡 (相較於同一個時間序列中的其他泡泡)。在 [檢視類型] 下拉式功能表中，選擇 [時間貯體中的計數 (醒目提示異常)] 或 [時間貯體中的百分比 (醒目提示異常)]。紅色泡泡為異常。

異常的泡泡其計數/百分比與發生在過去兩個時間週期 (舉例來說，如果您正在檢視過去 24 小時的資料，則為 48 小時) 內之計數/百分比的標準差超過 2.1 倍。

![彩色的點表示異常](./media/app-insights-trends/TrendsAnomalies-750.png)

> [AZURE.TIP] 在小型泡泡的時間序列中尋找看起來可能大小相似的離群值時，將異常者醒目提示非常實用。

## <a name="next"></a>接續步驟


[在 Visual Studio 中使用 Application Insights](app-insights-visual-studio.md)：搜尋遙測、查看 CodeLens 中的資料，以及設定 Application Insights，一切都可以在 Visual Studio 中完成。

[新增更多資料](app-insights-asp-net-more.md)：監視使用狀況、可用性、相依性、以及例外狀況。整合來自記錄架構的追蹤。撰寫自訂遙測。

[使用 Application Insights 入口網站](app-insights-dashboards.md)：儀表板、功能強大的診斷和分析工具、警示、即時的應用程式相依性對應，以及遙測匯出等功能。

<!---HONumber=AcomDC_0810_2016-->