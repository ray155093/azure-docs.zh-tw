---
title: "新增 Application Insights SDK 以監視 Node.js 應用程式 | Microsoft Docs"
description: "使用 Application Insights 分析內部部署或 Microsoft Azure Web 應用程式的使用情況、可用性和效能。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: fd089f0cc5c23dcddb392df55c65907519f59248


---
# <a name="add-application-insights-sdk-to-monitor-your-nodejs-app"></a>新增 Application Insights SDK 以監視 Node.js 應用程式


[Azure Application Insights](app-insights-overview.md) 監視您的即時應用程式，協助您[偵測並診斷效能問題和例外狀況](app-insights-detect-triage-diagnose.md)，同時[探索應用程式的使用情況](app-insights-overview-usage.md)。 這適用於裝載在專屬內部部署 IIS 伺服器或 Azure VM 上的應用程式，以及 Azure Web 應用程式。

SDK 可自動收集內送 HTTP 要求率和回應、效能計數器 (CPU、記憶體、RPS) 和未處理的例外狀況。 此外，您也可以新增自訂呼叫來追蹤相依性、度量或其他事件。

![範例效能監視圖表](./media/app-insights-nodejs/10-perf.png)

#### <a name="before-you-start"></a>開始之前
您需要：

* Visual Studio 2013 或更新版本。 越新版越好。
* [Microsoft Azure](http://azure.com)訂用帳戶。 如果您的小組或組織擁有 Azure 訂用帳戶，擁有者就可以使用您的 [Microsoft 帳戶](http://live.com)將您加入。

## <a name="a-nameaddacreate-an-application-insights-resource"></a><a name="add"></a>建立 Application Insights 資源
登入 [Azure 入口網站][]，並建立新的 Application Insights 資源。 Azure 中的[資源][roles]是服務的執行個體。 此資源是來自您應用程式的遙測將經過分析並呈現的地方。

![按一下 [新增]，然後按一下 [Application Insights]](./media/app-insights-nodejs/01-new-asp.png)

選擇 [其他] 做為應用程式類型。 應用程式類型的選擇會設定[計量瀏覽器][metrics]中可見的資源刀鋒視窗和屬性的預設內容。

#### <a name="copy-the-instrumentation-key"></a>複製檢測金鑰
該金鑰識別資源，您很快就會將它安裝在 SDK 中，以將資源導向資料。

![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-nodejs/02-props-asp.png)

## <a name="a-namesdka-install-the-sdk-in-your-application"></a><a name="sdk"></a> 在應用程式中安裝 SDK
```
npm install applicationinsights --save
```

## <a name="usage"></a>使用量
這會啟用要求監視、未處理的例外狀況追蹤和系統效能監視 (CPU/記憶體/RPS)。

```javascript

var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>").start();
```

檢測金鑰也可設定於環境變數 APPINSIGHTS_INSTRUMENTATIONKEY 中。 如果這麼做，則在呼叫 `appInsights.setup()` 或 `appInsights.getClient()` 時就不需要引數。

您可以在不傳送遙測的情況下嘗試 SDK︰將檢測金鑰設定為非空白字串。

## <a name="a-nameruna-run-your-project"></a><a name="run"></a> 執行專案
執行應用程式並立即試用：開啟不同的頁面來產生一些遙測。

## <a name="a-namemonitora-view-your-telemetry"></a><a name="monitor"></a> 檢視遙測
返回 [Azure 入口網站](https://portal.azure.com) ，並且瀏覽至您的 Application Insights 資源。

在 [概觀] 頁面中尋找資料。 剛開始的時候，您只會看見一或兩個資料點。 例如：

![Click through to more data](./media/app-insights-nodejs/12-first-perf.png)

按一下任何圖表以查看詳細度量。 [深入了解度量。][]

#### <a name="no-data"></a>沒有資料？
* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 開啟 [ [搜尋](app-insights-diagnostic-search.md) ] 磚來查看個別事件。 有時候，事件通過計量管線所需的時間較長。
* 請稍等片刻，然後按一下 [重新整理 ]。 圖表會定期自行重新整理，但是如果您在等待一些要顯示的資料，您可以手動重新整理。
* 請參閱[疑難排解][qna]。

## <a name="publish-your-app"></a>發佈您的應用程式
現在請將應用程式部署至 IIS 或 Azure，並觀看資料累積情形。

#### <a name="no-data-after-you-publish-to-your-server"></a>發佈資料到伺服器之後，卻沒有資料？
請在您的伺服器防火牆中，開啟這些連出流量的連接埠：

* `dc.services.visualstudio.com:443`
* `f5.services.visualstudio.com:443`

#### <a name="trouble-on-your-build-server"></a>組建伺服器發生問題？
請參閱 [此疑難排解項目](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild)。

## <a name="customized-usage"></a>自訂的使用量
### <a name="disabling-auto-collection"></a>停用自動收集
```javascript
import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    // no telemetry will be sent until .start() is called
    .start();
```

### <a name="custom-monitoring"></a>自訂監視
```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

client.trackEvent("custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");
```

[深入了解遙測 API](app-insights-api-custom-events-metrics.md)。

### <a name="using-multiple-instrumentation-keys"></a>使用多個檢測金鑰
```javascript
import appInsights = require("applicationinsights");

// configure auto-collection with one instrumentation key
appInsights.setup("<instrumentation_key>").start();

// get a client for another instrumentation key
var otherClient = appInsights.getClient("<other_instrumentation_key>");
otherClient.trackEvent("custom event");
```

## <a name="examples"></a>範例
### <a name="tracking-dependency"></a>追蹤相依性
```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

var startTime = Date.now();
// execute dependency call
var endTime = Date.now();

var elapsedTime = endTime - startTime;
var success = true;
client.trackDependency("dependency name", "command name", elapsedTime, success);
```



### <a name="manual-request-tracking-of-all-get-requests"></a>所有 "GET" 要求的手動要求追蹤
```javascript
var http = require("http");
var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false) // disable auto-collection of requests for this example
    .start();

// assign common properties to all telemetry sent from the default client
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};

// track a system startup event
appInsights.client.trackEvent("server start");

// create server
var port = process.env.port || 1337
var server = http.createServer(function (req, res) {
    // track all "GET" requests
    if(req.method === "GET") {
        appInsights.client.trackRequest(req, res);
    }

    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello World\n");
}).listen(port);

// track startup time of the server as a custom metric
var start = +new Date;
server.on("listening", () => {
    var end = +new Date;
    var duration = end - start;
    appInsights.client.trackMetric("StartupTime", duration);
});
```

## <a name="next-steps"></a>後續步驟
* [在入口網站中監視遙測](app-insights-dashboards.md)
* [[寫您的遙測的分析查詢](app-insights-analytics-tour.md)

<!--Link references-->

[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md



<!--HONumber=Nov16_HO3-->


