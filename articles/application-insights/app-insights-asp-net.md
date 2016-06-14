<properties 
	pageTitle="使用 Application Insights 設定 ASP.NET 的 Web 應用程式分析" 
	description="針對裝載在內部部署環境或 Azure 的 ASP.NET 網站設定效能、可用性及使用情況分析。" 
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


# 設定 ASP.NET 的 Application Insights

[Visual Studio Application Insights](app-insights-overview.md) 監視您的即時應用程式，協助您[偵測並診斷效能問題和例外狀況](app-insights-detect-triage-diagnose.md)，同時[探索應用程式的使用情況](app-insights-overview-usage.md)。這適用於裝載在專屬內部部署 IIS 伺服器或雲端 VM 上的應用程式，以及 Azure Web 應用程式。


## 開始之前

您需要：

* Visual Studio 2013 Update 3 或更新版本。越新版越好。
* [Microsoft Azure](http://azure.com) 訂用帳戶。如果您的小組或組織擁有 Azure 訂用帳戶，擁有者就可以使用您的 [Microsoft 帳戶](http://live.com)將您加入。 

如果您對下列內容感興趣，請查看其他文章︰

* [在執行階段檢測 Web 應用程式](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Azure 雲端服務](app-insights-cloudservices.md)

## <a name="ide"></a> 1.加入 Application Insights SDK


### 對於新專案

當您在 Visual Studio 中建立新專案時，請務必選取 Application Insights。


![Create an ASP.NET project](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### ... 對於現有專案

在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [新增 Application Insights 遙測]] 或 [設定 Application Insights]。

![Choose Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)




## <a name="run"></a> 2.執行您的應用程式

利用 F5 執行應用程式並立即試用：開啟不同的頁面來產生一些遙測。

在 Visual Studio 中，您可以看見已記錄的事件計數。

![在 Visual Studio 中，[Application Insights] 按鈕會在偵錯期間顯示。](./media/app-insights-asp-net/54.png)

## 3\.查看遙測...

### ... 在 Visual Studio 中

在 Visual Studio 中開啟 [Application Insights] 視窗︰按一下 [Application Insights] 按鈕，或以滑鼠右鍵按一下 [方案總管] 中的專案︰

![在 Visual Studio 中，[Application Insights] 按鈕會在偵錯期間顯示。](./media/app-insights-asp-net/55.png)

此檢視會顯示應用程式的伺服器端所產生的遙測。試驗篩選器，然後按一下任何事件以查看更多詳細資料。

[深入了解 Visual Studio 中的 Application Insights 工具](app-insights-visual-studio.md)。

<a name="monitor"></a>
### ... 在入口網站中

除非您選擇 [僅安裝 SDK]，否則您也可以在 Application Insights Web 入口網站查看遙測。

此入口網站中的圖表、分析工具和儀表板比 Visual Studio 還多。


在 [Azure 入口網站](https://portal.azure.com/)中開啟 Application Insights 資源。

![Right-click your project and open the Azure portal](./media/app-insights-asp-net/appinsights-04-openPortal.png)

入口網站會開啟應用程式的遙測檢視︰![](./media/app-insights-asp-net/66.png)

* 個別事件會出現在 [搜尋] (1)。資料會先出現在這裡 (和即時串流中)。按一下任何事件以查看其屬性。 
* 彙總度量會出現在圖表 (2)。資料可能需要一兩分鐘的時間才會出現在這裡。按一下任何圖表即可開啟內含更多詳細資料的刀鋒視窗。

[深入了解在 Azure 入口網站中使用 Application Insights](app-insights-dashboards.md)。

##<a name="land"></a>「加入 Application Insights」執行了哪些動作？

Application Insights 會將應用程式的遙測傳送至 Application Insights 入口網站 (裝載於 Microsoft Azure)︰

![](./media/app-insights-asp-net/01-scheme.png)

因此命令會執行三項工作︰

1. 將 Application Insights Web SDK NuGet 封裝加入您的專案。若要在 Visual Studio 中看到它，請以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]。
2. 在 [Azure 入口網站](https://portal.azure.com/) 中建立 Application Insights 資源。這是您會看到您的資料的位置。它會擷取可識別資源的「檢測金鑰」。
3. 在 `ApplicationInsights.config` 中插入檢測金鑰，讓 SDK 可以將遙測傳送至入口網站。

如果您想要的話，可以[手動進行這些步驟](app-insights-asp-net-manual.md)。


## 後續步驟

| | 
|---|---
|**[在 Visual Studio 中使用 Application Insights](app-insights-visual-studio.md)**<br/>使用遙測來偵錯、診斷搜尋、鑽研程式碼。|![Visual studio](./media/app-insights-asp-net/61.png)
|**[使用 Application Insights 入口網站](app-insights-dashboards.md)**<br/>儀表板、功能強大的診斷和分析工具、警示、即時的應用程式相依性對應，以及遙測匯出。 |![Visual studio](./media/app-insights-asp-net/62.png)
|**[新增更多資料](app-insights-asp-net-more.md)**<br/>監視使用狀況、可用性、相依性、例外狀況。整合來自記錄架構的追蹤。撰寫自訂遙測。 | ![Visual studio](./media/app-insights-asp-net/64.png)

<!---HONumber=AcomDC_0608_2016-->