<properties 
	pageTitle="在 Visual Studio 中使用 Application Insights" 
	description="偵錯期間和生產環境中的效能分析與診斷。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="awills"/>


# 在 Visual Studio 中使用 Application Insights

在 Visual Studio (2015 和更新版本) 中，您可以使用來自 [Visual Studio Application Insights](app-insights-overview.md) 的遙測，在偵錯時和在生產環境中分析效能並診斷問題。

如果您尚未[在應用程式中安裝 Application Insights](app-insights-asp-net.md)，請立即安裝。

## <a name="run"></a> 偵錯您的專案

利用 F5 執行應用程式並立即試用：開啟不同的頁面來產生一些遙測。

在 Visual Studio 中，您可以看見已記錄的事件計數。

![在 Visual Studio 中，[Application Insights] 按鈕會在偵錯期間顯示。](./media/app-insights-visual-studio/appinsights-09eventcount.png)

按一下此按鈕以開啟診斷搜尋。



## 診斷搜尋

[搜尋] 視窗會顯示已記錄的事件。(如果您在設定 Application Insights 時登入至 Azure，就能夠在入口網站搜尋相同的事件。)

![以滑鼠右鍵按一下專案，然後選擇 [Application Insights]、[搜尋]](./media/app-insights-visual-studio/34.png)

任意文字搜尋適用於事件中的任何欄位。例如，搜尋頁面的 URL 的一部分；或者如用戶端城市的屬性值；或者追蹤記錄檔中的特定單字。

按一下任何事件以查看其詳細屬性。

您也可以開啟 [相關項目] 索引標籤，以協助診斷失敗的要求或例外狀況。


![](./media/app-insights-visual-studio/41.png)



## 診斷中樞

診斷中樞 (在 Visual Studio 2015 或更新版本) 會顯示其產生的 Application Insights 伺服器遙測。即使您選擇只安裝 SDK，而不連接至 Azure 入口網站中的資源，也會運作。

![開啟 [診斷工具] 視窗，並檢查 Application Insights 事件。](./media/app-insights-visual-studio/31.png)


## 例外狀況

如果您已[設定例外狀況監視](app-insights-asp-net-exceptions.md)，例外狀況報告會顯示在 [搜尋] 視窗中。

按一下例外狀況以取得堆疊追蹤。如果應用程式的程式碼在 Visual Studio 中開啟，您可以從堆疊追蹤點選至程式碼的相關程式碼行。


![](./media/app-insights-visual-studio/17.png)


## 本機監視



(從 Visual Studio 2015 Update 2 開始) 如果您尚未設定 SDK 以將遙測傳送至 Application Insights 入口網站 (以便讓 ApplicationInsights.config 中沒有任何檢測金鑰)，則 [診斷] 視窗會顯示來自最新偵錯工作階段的遙測。

如果您已發佈過應用程式先前的版本，這是比較好的做法。您不會想讓來自偵錯工作階段的遙測與 Application Insights 入口網站中來自已發佈之應用程式的遙測混在一起。

如果您在將遙測傳送至入口網站之前有一些[自訂遙測](app-insights-api-custom-events-metrics.md)想要偵錯，它也很有用。


* 首先，我完全設定 Application Insights 將遙測傳送至入口網站。但是現在我只想要查看在 Visual Studio 中的遙測。

 * 在 [搜尋] 視窗的 [設定] 中，即使您的應用程式將遙測傳送至入口網站，也有選項可搜尋本機診斷。
 * 若要停止將遙測傳送至入口網站，請將 ApplicationInsights.config 中的 `<instrumentationkey>...` 程式行註解化。當您準備再次將遙測傳送至入口網站時，請取消註解。





## 若要升級至未來的 SDK 版本

若要升級至[新版的 SDK](app-insights-release-notes-dotnet.md)，請再次開啟 NuGet 封裝管理員，並篩選出已安裝的封裝。選取 Microsoft.ApplicationInsights.Web 然後選擇 [升級]。

如果您已對 ApplicationInsights.config 進行任何的自訂，請在升級前儲存複本，並在升級後合併您的變更到新版本中。



## 後續步驟

||
|---|---
|**[新增更多資料](app-insights-asp-net-more.md)**<br/>監視使用狀況、可用性、相依性、例外狀況。整合來自記錄架構的追蹤。撰寫自訂遙測。 | ![Visual studio](./media/app-insights-asp-net/64.png)
|**[使用 Application Insights 入口網站](app-insights-dashboards.md)**<br/>儀表板、功能強大的診斷和分析工具、警示、即時的應用程式相依性對應，以及遙測匯出。 |![Visual studio](./media/app-insights-asp-net/62.png)


 

<!---HONumber=AcomDC_0608_2016-->