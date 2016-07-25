<properties 
	pageTitle="充分利用 Application Insights" 
	description="開始使用 Application Insights 之後，以下是您可以瀏覽的功能摘要。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2016" 
	ms.author="awills"/>

# 更多來自 Application Insights 的遙測

以下摘要說明您可能尚未在 [Visual Studio Application Insights](app-insights-overview.md) 中嘗試使用的功能。我們假設您已經[開始使用](app-insights-asp-net.md)。Application Insights 可讓您監視 Web 應用程式的可用性、效能和使用情形。當您取得有關應用程式在現實世界的效能和效率的意見反應時，您可以在每個開發生命週期中針對設計方向做出明智的抉擇。

## 更多遙測

||
|---|---
|[**可用性測試**](app-insights-monitor-web-app-availability.md)<br/>定期從世界各地傳送您的 Web 應用程式 HTTP 要求。如果回應太慢或不可靠，我們會通知您。| 
|[**相依性呼叫**](app-insights-asp-net-dependencies.md)<br/>監視 SQL 查詢，以及呼叫 REST 或其他資源。|
|[**例外狀況**](app-insights-asp-net-exceptions.md)<br/>計算已處理和未處理的例外狀況、取得堆疊追蹤，以及點擊至程式碼。|
|[**網頁**](app-insights-javascript.md)<br/>檢測您的網頁來監視網頁使用狀況、效能和 AJAX 呼叫。
|**主機效能︰[Azure 診斷](app-insights-azure-diagnostics.md)、[Windows 效能計數器](app-insights-web-monitor-performance.md)**<br/>查看 CPU 使用率和其他內容計量。 |![](./media/app-insights-asp-net-more/04.png)
|[**SDK API - 自訂遙測**](app-insights-api-custom-events-metrics.md)<br/>可傳送您自己的事件和計量，以取得您的應用程式效能和使用狀況的更詳細檢視 (在伺服器和用戶端程式碼中)。|
|[**記錄整合**](app-insights-asp-net-trace-logs.md)<br/>如果您使用記錄架構 (例如 Log4Net、NLog 或 System.Diagnostics.Trace)，就會有可將這些追蹤與其他遙測一起傳送至 Application Insights 的配接器。|
|[**TelemetryProcessors**](app-insights-api-filtering-sampling.md)<br/>篩選、修改或擴大從您應用程式中的 SDK 所傳送的遙測。 |


## 功能強大的分析和呈現方式

||
|---|---
|[**適用於搜尋執行個體的診斷資料**](app-insights-visual-studio.md)<br/>搜尋和篩選事件，例如要求、例外狀況、相依性呼叫、記錄追蹤，以及頁面檢視。在 Visual Studio 中，從堆疊追蹤移至程式碼。|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**彙總資料的計量瀏覽器**](app-insights-metrics-explorer.md)<br/>瀏覽、篩選和分割彙總的資料，例如，要求、錯誤和例外狀況的比率；回應時間、頁面載入時間。|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**儀表板**](app-insights-dashboards.md#dashboards)<br/>來自多個資源的交互式資料並與其他人員共用。非常適用於多元件的應用程式，以及小組聊天室中的連續顯示。 |![儀表板範例](./media/app-insights-asp-net/62.png)
|[**即時計量串流**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>當您部署新的組建時，請觀看這些近乎即時的效能指標，以確定一切如預期運作。|![分析範例](./media/app-insights-asp-net-more/050.png)
|[**分析**](app-insights-analytics.md)<br/>使用這個功能強大的查詢語言，回答有關您應用程式效能和使用方式的艱難問題。|![分析範例](./media/app-insights-asp-net-more/010.png)
|[**自動和手動警示**](app-insights-alerts.md)<br/>如果在常見模式之外發生一些狀況，則自動警示會適應您應用程式的一般遙測和觸發程式模式。您也可以在自訂或標準計量的特定層級上設定警示。|![警示範例](./media/app-insights-asp-net-more/020.png)

## 資料管理

|||
|---|---|
|[**連續匯出**](app-insights-export-telemetry.md)<br/>將您的所有遙測複製到儲存體，讓您可以自己的方式來分析它。|
|**資料存取 API**<br/>即將推出。|
|[**取樣**](app-insights-sampling.md)<br/>縮短資料速率，並協助您維持在您定價層的限制內。|![取樣圖格](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0713_2016-->