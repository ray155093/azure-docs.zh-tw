<properties 
	pageTitle="充分利用 Application Insights | Microsoft Azure" 
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
	ms.date="08/30/2016" 
	ms.author="awills"/>

# 更多來自 Application Insights 的遙測

在您已經[將 Application Insights 加入到您的 ASP.NET 程式碼](app-insights-asp-net.md)之後，您可以採取幾個動作來取得更多遙測。

## 如果您的應用程式是在您的 IIS 伺服器上執行...

如果您的應用程式是在您的控制下裝載在 IIS 伺服器上，請在伺服器上安裝 Application Insights 狀態監視器。如果已經安裝，您就不需要採取任何動作。

1. 在每部 IIS Web 伺服器上，以系統管理員認證登入。
2. 下載並執行[狀態監視器安裝程式](http://go.microsoft.com/fwlink/?LinkId=506648)。
3. 在安裝精靈中，登入 Microsoft Azure。

您不需要採取任何其他動作，但您可以確認已經針對您的應用程式啟用監視。

![在 Azure 中擴充](./media/app-insights-asp-net-more/025.png)

(即使您沒有在 Visual Studio 中檢測您的應用程式，也可以使用狀態監視器[在執行階段啟用監視](app-insights-monitor-performance-live-website-now.md)。)

### 您可以取得什麼？

如果狀態監視器是安裝在您的伺服器機器上，您可以取得一些額外的遙測：

* .NET 4.5 應用程式的相依性遙測 (由您的應用程式執行的 SQL 呼叫和 REST 呼叫)。(對於更新版本的 .NET，不需要狀態監視器就可以取得相依性遙測。)
* 例外狀況堆疊追蹤會顯示更多詳細資料。
* 效能計數器。在 Application Insights 中，這些計數器會顯示在 [伺服器] 刀鋒視窗中。

![在 Azure 中擴充](./media/app-insights-asp-net-more/070.png)

若要查看更多或較少內容，[編輯圖表](app-insights-metrics-explorer.md)。如果可用的集合中沒有您想要的效能計數器，您可以[將它新增至效能計數器模組收集的集合中](app-insights-web-monitor-performance.md#system-performance-counters)。

## 如果它是 Azure Web 應用程式 ...

如果您的應用程式是以 Azure Web 應用程式的方式執行，請移至應用程式或 VM 的 Azure 控制台，然後新增 Application Insights 擴充功能。在 [工具] 中，開啟 [效能監視] 並設定 [Application Insights]。出現提示時，請選擇您已經建立的 Application Insights 資源。

![在 Azure 中擴充](./media/app-insights-asp-net-more/05-extend.png)

### 您可以取得什麼？

* 例外狀況堆疊追蹤會顯示更多詳細資料。
* .NET 4.5 應用程式的相依性遙測 (由您的應用程式執行的 SQL 呼叫和 REST 呼叫)。(對於更新版本的 .NET，不需要擴充功能就可以取得相依性遙測。)

![在 Azure 中擴充](./media/app-insights-asp-net-more/080.png)

(即使您沒有在 Visual Studio 中檢測您的應用程式，也可以使用這個方法[在執行階段啟用效能監視](app-insights-monitor-performance-live-website-now.md)。)

## 用戶端監視

您已安裝會從應用程式的伺服器 (後端) 傳送遙測資料的 SDK。現在您可以加入用戶端監視。這可提供有關在瀏覽器中發生的使用者、工作階段、頁面檢視和任何例外狀況或損毀的相關資料。您也可以撰寫自己的程式碼，來追蹤您的使用者如何使用您的應用程式，徹底得知詳細的點按和按鍵層級。

將 Application Insights JavaScript 程式碼片段新增到每個網頁，以從用戶端瀏覽器取得遙測。

1. 在 Azure 中，為您的應用程式開啟 Application Insights 資源。
2. 開啟 [快速入門]、[監視用戶端]，然後複製程式碼片段。
3. 張貼程式碼片段，使其出現在每個網頁的標頭中 - 通常您可以張貼到主要版面配置頁面來達到這個目的。

![在 Azure 中擴充](./media/app-insights-asp-net-more/100.png)

請注意，此程式碼包含可識別您的應用程式資源的檢測金鑰。

### 您可以取得什麼？

* 您可以撰寫 JavaScript 來[從您的網頁傳送自訂遙測](app-insights-api-custom-events-metrics.md)，例如追蹤按一下按鈕的動作。
* 在 [分析](app-insights-analytics.md) 中，是使用 `pageViews` 來取得資料，以及使用 `dependencies` 來取得 AJAX 資料。
* 瀏覽器刀鋒視窗中的[用戶端效能與使用狀況資料](app-insights-javascript.md)。

![在 Azure 中擴充](./media/app-insights-asp-net-more/090.png)


[深入了解網頁追蹤。](app-insights-web-track-usage.md)



## 追蹤應用程式版本

請確定 `buildinfo.config` 是藉由您的 MSBuild 處理序所產生。在您的 .csproj 檔案中加入：

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

當它有組建資訊時，Application Insights Web 模組會自動新增**應用程式版本**，做為每個遙測項目的屬性。如此可讓您在執行[診斷搜尋](app-insights-diagnostic-search.md)或在[探索度量](app-insights-metrics-explorer.md)時，依據版本來篩選。

但請注意，組建版本號碼只能由 MS 組建產生，不是 Visual Studio 中的開發人員組建產生。


## 可用性 Web 測試

定期從世界各地傳送您的 Web 應用程式 HTTP 要求。如果回應太慢或不可靠，我們會通知您。

在您應用程式的 Application Insights 資源中，按一下 [可用性] 圖格來新增、編輯及檢視 Web 測試。

您可以新增在多個位置執行的多個測試。

![在 Azure 中擴充](./media/app-insights-asp-net-more/110.png)

[深入了解](app-insights-monitor-web-app-availability.md)

## 自訂遙測和記錄

您新增至程式碼的 Application Insights 封裝可提供您可以從應用程式呼叫的 API。

* [產生您自己的事件和計量](app-insights-api-custom-events-metrics.md)，例如計算商務事件或監視效能。
* 從 Log4Net、NLog 或 System.Diagnostics.Trace [擷取記錄追蹤](app-insights-asp-net-trace-logs.md)。
* 透過撰寫遙測處理器來[篩選、修改或擴大](app-insights-api-filtering-sampling.md)從您的應用程式傳送的標準遙測。


## 功能強大的分析和呈現方式

有許多方式可以探索您的資料。如果您最近已經開始使用 Application Insights，請參閱下列文章：

||
|---|---
|[**適用於搜尋執行個體的診斷資料**](app-insights-visual-studio.md)<br/>搜尋和篩選事件，例如要求、例外狀況、相依性呼叫、記錄追蹤，以及頁面檢視。在 Visual Studio 中，從堆疊追蹤移至程式碼。|![Visual studio](./media/app-insights-asp-net/61.png)
|[**彙總資料的計量瀏覽器**](app-insights-metrics-explorer.md)<br/>瀏覽、篩選和分割彙總的資料，例如，要求、錯誤和例外狀況的比率；回應時間、頁面載入時間。|![Visual studio](./media/app-insights-asp-net-more/060.png)
|[**儀表板**](app-insights-dashboards.md#dashboards)<br/>來自多個資源的交互式資料並與其他人員共用。非常適用於多元件的應用程式，以及小組聊天室中的連續顯示。 |![儀表板範例](./media/app-insights-asp-net/62.png)
|[**即時計量串流**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>當您部署新的組建時，請觀看這些近乎即時的效能指標，以確定一切如預期運作。|![分析範例](./media/app-insights-asp-net-more/050.png)
|[**分析**](app-insights-analytics.md)<br/>使用這個功能強大的查詢語言，回答有關您應用程式效能和使用方式的艱難問題。|![分析範例](./media/app-insights-asp-net-more/010.png)
|[**自動和手動警示**](app-insights-alerts.md)<br/>如果在常見模式之外發生一些狀況，則自動警示會適應您應用程式的一般遙測和觸發程式模式。您也可以在自訂或標準計量的特定層級上設定警示。|![警示範例](./media/app-insights-asp-net-more/020.png)

## 資料管理

|||
|---|---|
|[**連續匯出**](app-insights-export-telemetry.md)<br/>將您的所有遙測複製到儲存體，讓您能夠以自己的方式來分析它。|
|**資料存取 API**<br/>即將推出。|
|[**取樣**](app-insights-sampling.md)<br/>縮短資料速率，並協助您維持在您定價層的限制內。|![取樣圖格](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0907_2016-->