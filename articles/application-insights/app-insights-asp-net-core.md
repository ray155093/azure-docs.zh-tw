<properties 
	pageTitle="ASP.NET Core 的 Application Insights" 
	description="監視 Web 應用程式的可用性、效能和使用方式。" 
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
	ms.date="05/18/2016" 
	ms.author="awills"/>

# ASP.NET Core 的 Application Insights

Visual Studio Application Insights 可讓您監視 Web 應用程式的可用性、效能和使用情形。當您取得有關應用程式在現實世界的效能和效率的意見反應時，您可以在每個開發生命週期中針對設計方向做出明智的抉擇。

![範例](./media/app-insights-asp-net-five/sample.png)

您需要 [Microsoft Azure](http://azure.com) 的訂用帳戶。使用 Microsoft 帳戶登入，可能是針對 Windows、XBox Live 或其他 Microsoft 雲端服務具備的帳戶。您的小組可能已有 Azuare 組織訂用帳戶：請洽詢擁有者將您的 Microsoft 帳戶新增至其中。


## 開始使用

如果您是在 Visual Studio 2015 中建立您的專案，您應該已經有 Application Insights。否則，請依照[快速入門指南](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started)的指示進行。

## 使用 Application Insights

登入 [Microsoft Azure 入口網站](https://portal.azure.com)，瀏覽至您建立用來監視應用程式的資源。

在另一個瀏覽器視窗中，使用您的應用程式一段時間。您會看到資料出現在 Application Insights 圖表。(您可能需要按一下 [重新整理])。 在您的開發過程只會有少量的資料，但是當您發行應用程式並有許多使用者時，這些圖表就會真正活躍起來。

[概觀] 頁面顯示您最可能有興趣的效能圖表：伺服器回應時間、頁面載入時間和失敗的要求計數。按一下任一圖表以查看更多的圖表和資料。

入口網站中的檢視分成兩個主要類別：

* [計量瀏覽器](app-insights-metrics-explorer.md)，顯示計量和計數 (如回應時間、失敗率或您使用 [API](app-insights-api-custom-events-metrics.md) 自行建立的計量) 的圖形與表格。依屬性值篩選和分割資料，以進一步了解您的應用程式和其使用者。
* [搜尋總管](app-insights-diagnostic-search.md)，列出個別事件，例如特定要求、例外狀況、記錄檔追蹤或您使用 [API](app-insights-api-custom-events-metrics.md) 自行建立的事件。在事件中篩選和搜尋，並瀏覽相關事件以調查問題。
* [分析](app-insights-analytics.md)可讓您在遙測上執行類似 SQL 的查詢，而且這是一個功能強大的分析與診斷工具。

## Alerts

* 您將會自動取得[調適性警示](app-insights-nrt-proactive-diagnostics.md)，其將會通知您，在失敗要求的比率中出現異常變更的相關資訊。
* 設定[可用性測試](app-insights-monitor-web-app-availability.md)，持續從全球各地的位置 測試您的網站，並在有任何測試失敗時立即取得電子郵件。
* 設定[計量警示](app-insights-monitor-web-app-availability.md)，以了解計量 (如回應時間或例外狀況率) 是否超出可接受的限制。

## 取得更多的遙測

* [將遙測加入至您的網頁](app-insights-javascript.md)以監視頁面使用情況和效能。
* [監視相依性](app-insights-dependencies.md)，可查看 REST、SQL 或其他外部資源是否降低您的效能。
* [使用 API](app-insights-api-custom-events-metrics.md) 可傳送您自己的事件和計量，以取得您的應用程式效能和使用方式的更詳細檢視。
* [可用性測試](app-insights-monitor-web-app-availability.md)可持續從世界各地檢查您的應用程式。 


## 開放原始碼

[讀取和貢獻程式碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

<!---HONumber=AcomDC_0525_2016-->