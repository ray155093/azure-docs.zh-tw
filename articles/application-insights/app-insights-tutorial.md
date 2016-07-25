<properties 
	pageTitle="Application Insights 教學課程| Microsoft Azure" 
	description="追蹤即時 Web 應用程式的使用情况和效能。偵測、分級和診斷問題。持續監視並改善您的使用者的成功。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/11/2016" 
	ms.author="awills"/>
 
# Application Insights - 教學課程

[Visual Studio Application Insights](app-insights-get-started.md) 是一項可延伸分析服務，會監視您的即時 Web 應用程式。您可以透過該服務來偵測並診斷效能問題，並了解實際上使用者如何運用您的應用程式。它是針對開發人員所設計，可協助您持續改善效能和可用性。它適用於各種不同平台上的應用程式，包括裝載在內部部署或雲端的 .NET、Node.js 和 J2EE。

![製作使用者活動統計資料的圖表，或深入特定事件。](./media/app-insights-overview/00-sample.png)


你可以[在開發期間將 SDK 加入至您的程式碼](app-insights-asp-net.md)，或[在執行階段檢測您的應用程式](app-insights-monitor-performance-live-website-now.md)來開始使用。

從您的應用程式收集而來的遙測資料會儲存並在 [Azure 入口網站](https://portal.azure.com/)中分析，其中有直覺式的檢視，以及用於快速診斷和分析的功能強大的工具。


[看一下簡介動畫](https://www.youtube.com/watch?v=fX2NtGrh-Y0)。


## 運作方式

您會在應用程式中安裝小型檢測套件，並且在 Microsoft Azure 入口網站中設定 Application Insights 資源。此檢測套件會監視您的應用程式，並將遙測資料傳送至入口網站。入口網站會顯示統計圖表，並提供強大的搜尋工具以協助您診斷任何問題。

![您的應用程式中的 Application Insights 檢測功能會將遙測傳送到 Azure 入口網站中的 Application Insights 資源。](./media/app-insights-overview/01-scheme.png)


Application Insights 有數個[標準檢測模組](app-insights-configuration-with-applicationinsights-config.md)，可收集不同類型的遙測。例如，有一個模組可監視您的 Web 應用程式對 HTTP 要求的回應；另一個模組可監視例外狀況；而其他模組可監視相依性、效能計數器等等。

如果您在開發階段檢測您的程式碼，您也可以[撰寫自己的自訂程式碼](app-insights-api-custom-events-metrics.md)，將遙測資料傳送至入口網站。自訂遙測在追蹤使用者案例時特別有用：您可以計算事件，例如查詢、銷售、達成特定目標。

### 用戶端監視

此外，還有採用小型指令碼的形式插入每個網頁的[用戶端檢測](app-insights-javascript.md)。遙測會直接從使用者的瀏覽器傳送至 Azure 入口網站。它可監視頁面載入時間、AJAX 呼叫和指令碼例外狀況。您也可以撰寫自訂程式碼來追蹤使用者動作，例如按鈕點選或其他事件。

用戶端監視可以與伺服器端監視獨立使用。


### 負荷為何？

對您的應用程式效能的影響很小。追蹤呼叫非封鎖性，而且會在個別的執行緒中分批傳送。

## 取得 Azure 訂用帳戶

Application Insights 是 Microsoft Azure 中裝載的多項服務之一，而遙測資料會送至該處進行分析及呈現。所以在執行任何動作前，您需要 [Microsoft Azure](http://azure.com) 訂用帳戶。您可以免費註冊，並選擇 Application Insights 的免費[定價層](https://azure.microsoft.com/pricing/details/application-insights/)。


## 開始使用

有兩種主要方式可以開始監視您的 Web 應用程式。從最適合您的方式著手。

|當|方式|得到什麼結果
|---|---|---
| 開發 |將 SDK 加入至程式碼 (伺服器及/或用戶端)| 監視應用程式回應時間和失敗率，以及在開發、測試和生產期間診斷問題。<br/>撰寫程式碼以加入自訂追蹤和診斷。 
|執行階段 |在主機上執行代理程式 (在內部部署或 Azure 中；J2EE 或 ASP.NET) | 監視回應時間和失敗率，以及在執行階段診斷問題。<br/>不需要存取程式碼。

兩種方法是互補的︰使用兩者可進行最完整的遙測。

### 在執行階段開始使用

在執行階段以多種方法檢測您的應用程式是最簡單的選項。您不用修改您的程式碼，您甚至可以在已部署應用程式後設定程式碼。(如果您想要，稍後可以切換到開發階段選項)。

此選項適用於：

* 不需要任何自訂遙測的應用程式。您高興取得標準要求和相依性回應時間、失敗率、例外狀況報告和效能計數器。
* 執行中的應用程式有一些問題 - 您需要取得一些快速診斷資料。

**您的應用程式在哪一部伺服器上執行？**

* **IIS 內部部署或在 VM 上**：在您的伺服器上[安裝 Application Insights 狀態監視器](app-insights-monitor-performance-live-website-now.md)。您將需要系統管理存取權。
* **Azure web 應用程式或 VM**：在應用程式主面板上[選取 Application Insights 效能監視](app-insights-monitor-performance-live-website-now.md#if-your-app-runs-as-an-azure-web-app)。
* **Java**：在您的伺服器上[安裝 Java 代理程式](app-insights-java-live.md)。

除了 Java 選項，這些工具會自動設定必要的 Application Insights 資源，以便您查看遙測。

登入 [Azure 入口網站](https://portal.azure.com)，瀏覽至您的應用程式的 Application Insights 概觀，您會看到資料送達。

稍後，如果您想加入[自訂遙測](app-insights-api-custom-events-metrics.md)，則後續遵循「在開發期間開始使用」路徑毫無問題。

### 在開發期間開始使用

若要取得 Application Insights 的完整功能，請在開發期間將適當的 Application Insights SDK 加入至您的程式碼。

新增 SDK，您即可：

* 在開發與測試期間取得診斷
* 取得例外狀況報告中的堆疊追蹤
* 自訂您的遙測︰針對商務和診斷目的，撰寫自己的程式碼來計算及追蹤事件和度量。

**您使用何種開發工具？**

* **ASP.NET on Visual Studio** 2013 Update 2 或更新版本 - 使用功能表命令 [將 Application Insights 加入您的 Web 專案](app-insights-asp-net.md)，或如果您要建立新的專案，只需檢查 Application Insights 選項。
* **Eclipse 中的 Java** - [使用 Eclipse 增益集](app-insights-java-eclipse.md)
* **Java** 與其他工具 - [安裝 Java SDK](app-insights-java-get-started.md)
* **其他平台** - [安裝適當的 SDK](app-insights-platforms.md)


![在 Visual Studio 中將 Application Insights 加入現有的專案](./media/app-insights-tutorial/appinsights-03-addExisting.png)

在大部分情況下，工具會將 SDK 加入專案，在 Application Insights 入口網站中建立資源，並將兩者連結在一起。不過，您可以視需要[在 Azure 中以手動方式建立新的資源](app-insights-create-new-resource.md)。

### 網頁選項

您永遠可以檢測您的網頁，即使您無法檢測您的伺服器程式碼亦然。

這可提供您的使用者、工作階段和頁面檢視計數、頁面載入效能資料、AJAX 呼叫回應時間和失敗率。您也可以撰寫程式碼以追蹤其他事件，例如按鈕點選或選取動作。這對單一頁面的 Web 應用程式特別有用。

* **網頁** - [將我們的程式碼片段加入至您的網頁。](app-insights-javascript.md)

## 探索度量

執行您的應用程式 (以開發電腦上的偵錯模式，或將它部署到伺服器)，並使用一段時間。然後登入 [Azure 入口網站](https://portal.azure.com)。

瀏覽至您的應用程式的 Application Insights 概觀刀鋒視窗︰

![概觀刀鋒視窗](./media/app-insights-tutorial/01-find.png)

概觀可讓您立即瞭解如何執行您的應用程式。您可以比較負載 (就要求的速率而論) 與您的應用程式回應要求的時間。如果在負載升提高時的回應時間增加不成比例，您可能要配置更多資源給您的應用程式。如果在您部署新組建之後，概觀立即顯示更多失敗的回應，您可能會想要回復。


#### 取得詳細資料

點選任何圖表以取得更詳細的圖表集。例如，「伺服器回應時間」圖表會導向可顯示要求率、回應時間、相依項目 (也就是您的應用程式所呼叫的服務) 的回應時間的圖表。

![伺服器刀鋒視窗](./media/app-insights-tutorial/04.png)

相依項目圖表很實用，因為它可協助您查看您的應用程式使用的資料庫和 REST API 是否回應良好，或者造成延遲。

#### 自訂圖表

試著編輯其中一個圖表。例如，如果 Web 應用程式在一組伺服器執行個體上執行，您可以比較在不同伺服器執行個體上的回應時間︰

![編輯圖表](./media/app-insights-tutorial/05.png)

1. 將滑鼠暫留在圖表上並按一下 [編輯]。
2. 選擇度量。多個度量可以顯示在一個圖表上，但只在特定組合中︰您可能必須在選取您想要的度量之前，先取消選取一個度量。
3. 使用 Group-By 依照屬性區隔度量。在此範例中，我們會針對不同的回應時間顯示不同的行。

    請注意，您必須選取度量的有效屬性，否則圖表不會顯示任何資料。
4. 選取圖表類型。區域圖和長條圖會顯示當彙總類型為「總和」時適用的堆疊顯示。

[深入探索度量](app-insights-metrics-explorer.md)。

## 搜尋執行個體資料

若要調查問題，最好能檢查特定的事件執行個體。

點選度量圖表，以徹底搜尋具有相關篩選器和時間範圍的執行個體資料。例如，逐一點選伺服器要求計數，以查看個別的要求報告。

或者，您可以從 [概觀] 頁面上的搜尋直接到達執行個體資料︰

![搜尋執行個體](./media/app-insights-tutorial/06.png)

使用 [篩選器] 將焦點放在特定類型的事件和已選擇的屬性值︰

![篩選屬性](./media/app-insights-tutorial/07.png)

按一下 "..." 以查看完整的屬性清單，或開啟與相同要求相關聯的其他事件。在此範例中，失敗的要求會有相關聯的例外狀況報告︰

![相關項目和屬性詳細資料](./media/app-insights-tutorial/08.png)

開啟事件 - 在此範例中為相關的例外狀況 - 而且您可以建立工作項目 (如果您使用 Visual Studio Team Services 來追蹤工作)。
 
![建立工作項目](./media/app-insights-tutorial/09.png)

## Analytics

[分析](app-insights-analytics.md)是更強大的搜尋和分析功能，您可以在其中對您的遙測資料撰寫類似 SQL 的查詢，或尋找特定問題，或編譯統計資訊。

![Analytics](./media/app-insights-tutorial/10.png)

開啟教學課程視窗，請查看並執行對您的資料的查詢範例，或讀取較長的[教學課程逐步解說](app-insights-analytics-tour.md)。Intellisense 會提示您可以使用的查詢，而且有[完整語言參考](app-insights-analytics-reference.md)。

查詢通常會以遙測串流的名稱開頭，例如要求、例外狀況或相依性。在左邊快速開啟結構描述列，以查看可用的遙測串流清單。查詢是[查詢作業](app-insights-analytics-reference.md#queries-and-operators)的管線，例如 `where` (布林值篩選器) 或 `project` (可計算新的屬性)。`summarize` 會[彙總執行個體](app-insights-analytics-tour.md#aggregate-groups-of-rows)、依您定義的函式進行分組，然後將彙總函式套用於已分組的資料。

結果會[以表各或各種類型的圖表呈現](app-insights-analytics-tour.md#charting-the-results)。

## 自訂遙測

您藉由安裝 Application Insights 所取得的內建遙測，可讓您針對對您的應用程式和相依項目 (也就是，從您的應用程式對 SQL、REST API 進行的呼叫) 所提出的 Web 要求分析計數、成功率和回應時間。您也可以取得例外狀況追蹤和 (利用您的伺服器上的狀態監視器) 系統效能計數器。如果您將用戶端程式碼片段加入至您的網頁，您會取得頁面檢視計數和載入時間、用戶端例外狀況，以及 AJAX 呼叫成功和回應率。

完整分析此遙測可讓您深入了解您的應用程式的效能和使用情況。但有時候並不足夠。您可能想要監視佇列的長度，以便微調效能；或計算銷售並依照位置加以區隔；或者，在用戶端上，找出使用者按一下特定按鈕的頻率，以便調整使用者經驗。

[Application Insights API](app-insights-api-custom-events-metrics.md) 提供 `TrackEvent(name)` 和 `TrackMetric(name, value)` 呼叫，以便您傳送自己的自訂事件和度量。沒有同等的呼叫可供用戶端使用。

比方說，如果您的網頁是單頁遊戲應用程式，您可能會在適當的位置插入一行，以記錄使用者何時贏得或輸掉遊戲︰

    
    appInsights.trackEvent("WinGame");
    ...
    appInsights.trackEvent("LoseGame");

然後我們可以將自訂事件計數繪製成圖表，並依照事件名稱加以區隔︰

![Analytics](./media/app-insights-tutorial/11.png)

### 記錄追蹤

為了方便診斷，您可將自訂事件 `TrackTrace(message)` 用於執行追蹤。在搜尋和分析功能中，您可以搜尋訊息的內容，該內容的長度可能會超過事件名稱。

如果您已經使用 Log4Net、NLog、Log4J 或 System.Diagnostic.Trace 等紀錄架構，則 Application Insights 可以擷取這些追蹤呼叫並顯示在其他遙測旁邊。Visual Studio 工具會自動加入適當的 SDK 模組。

## 儀表板

許多應用程式都包含數個元件，例如 Web 服務以及一或多個後端處理器。每個元件會受到個別的 Application Insights 資源監視。如果您的系統在 Azure 上執行，您可能會使用及監視事件中樞和機器學習等服務。

若要監視整個系統，您就可以從不同的應用程式中選取最有趣的圖表並將它們釘選到 Azure [儀表板](app-insights-dashboards.md)，讓您持續監控整個系統。

![儀表板](./media/app-insights-tutorial/12.png)

事實上，您可以建立多個儀表板 -例如可供監視一般系統健康狀態的小組聊天室儀表板；著重於不同功能的使用方式的設計儀表板；適用於測試中元件的個別儀表板等等。

如同資源一樣，小組成員間可以共用儀表板。

## 在 Visual Studio 中開發

如果您使用 Visual Studio 來開發您的應用程式，您可以找到數個內建的 Application Insights 工具。



### 診斷搜尋

[搜尋] 視窗會顯示已記錄的事件。(如果您在設定 Application Insights 時登入至 Azure，就能夠在入口網站搜尋相同的事件。)

![以滑鼠右鍵按一下專案，然後選擇 [Application Insights]、[搜尋]](./media/app-insights-visual-studio/34.png)

任意文字搜尋適用於事件中的任何欄位。例如，搜尋頁面的 URL 的一部分；或者如用戶端城市的屬性值；或者追蹤記錄檔中的特定單字。

按一下任何事件以查看其詳細屬性。

您也可以開啟 [相關項目] 索引標籤，以協助診斷失敗的要求或例外狀況。


![](./media/app-insights-visual-studio/41.png)



### 診斷中樞

診斷中樞 (在 Visual Studio 2015 或更新版本) 會顯示其產生的 Application Insights 伺服器遙測。即使您選擇只安裝 SDK，而不連接至 Azure 入口網站中的資源，也會運作。

![開啟 [診斷工具] 視窗，並檢查 Application Insights 事件。](./media/app-insights-visual-studio/31.png)


### 例外狀況

如果您已[設定例外狀況監視](app-insights-asp-net-exceptions.md)，例外狀況報告會顯示在 [搜尋] 視窗中。

按一下例外狀況以取得堆疊追蹤。如果應用程式的程式碼在 Visual Studio 中開啟，您可以從堆疊追蹤點選至程式碼的相關程式碼行。


![例外狀況堆疊追蹤](./media/app-insights-visual-studio/17.png)

此外，在每個方法上的程式碼功能濾鏡一行中，您會看到在過去 24 小時由 Application Insights 記錄的例外狀況計數。

![例外狀況堆疊追蹤](./media/app-insights-visual-studio/21.png)


### 本機監視



(從 Visual Studio 2015 Update 2 開始) 如果您尚未設定 SDK 以將遙測傳送至 Application Insights 入口網站 (以便讓 ApplicationInsights.config 中沒有任何檢測金鑰)，則 [診斷] 視窗會顯示來自最新偵錯工作階段的遙測。

如果您已發佈過應用程式先前的版本，這是比較好的做法。您不會想讓來自偵錯工作階段的遙測與 Application Insights 入口網站中來自已發佈之應用程式的遙測混在一起。

如果您在將遙測傳送至入口網站之前有一些[自訂遙測](app-insights-api-custom-events-metrics.md)想要偵錯，它也很有用。


* 首先，我完全設定 Application Insights 將遙測傳送至入口網站。但是現在我只想要查看在 Visual Studio 中的遙測。

 * 在 [搜尋] 視窗的 [設定] 中，即使您的應用程式將遙測傳送至入口網站，也有選項可搜尋本機診斷。
 * 若要停止將遙測傳送至入口網站，請將 ApplicationInsights.config 中的 `<instrumentationkey>...` 程式行註解化。當您準備再次將遙測傳送至入口網站時，請取消註解。

## 趨勢

趨勢是用來將應用程式一段時間內行為方式進行視覺化的工具。

從 Application Insights 工具列按鈕或 [Application Insights 搜尋] 視窗選擇 [探索遙測趨勢]。選擇五種常見查詢的其中一個，以便開始使用。您可以根據遙測類型、時間範圍和其他屬性，分析不同的資料集。

若要尋找資料中的異常狀況，請選擇 [檢視類型] 下拉式清單底下的其中一個異常選項。視窗底部的篩選選項可讓您輕鬆地全神貫注於特定的遙測子集。

![趨勢](./media/app-insights-visual-studio/51.png)


## 發行新組建

### 即時計量串流

即時計量串流說明您的應用程式計量正處於這個非常時刻，接近即時的延遲為 1 秒。當您發行了新的組建且想要確定一切都如預期般運作，或者要即時調查某個事件時，這是非常有用的。

![在 [概觀] 刀鋒視窗中，按一下 [即時資料流]](./media/app-insights-metrics-explorer/live-stream.png)

不同於計量瀏覽器，即時計量串流會顯示一組固定的計量。只要資料仍在圖表上就會保存，之後便會捨棄該資料。

### 註解

度量圖表上的[版本註解](app-insights-annotations.md)會顯示您在哪邊部署了新組建。註解可讓您輕鬆查看變更是否對應用程式的效能有任何影響。[Visual Studio Team Services 建置系統](https://www.visualstudio.com/zh-TW/get-started/build/build-your-app-vs)可自動建立註解，您也可以[從 PowerShell 建立](#create-annotations-from-powershell)。

![註解範例，其會顯示與伺服器回應時間的相互關聯](./media/app-insights-annotations/00.png)

發行註解是 Visual Studio Team Services 的雲端型組建和發行服務的功能。

## Alerts

如果您的應用程式發生問題，您會想要立即得知問題。

Application Insights 會提供三種類型的警示 (透過電子郵件傳送)。

### 主動診斷 

[主動式診斷](app-insights-nrt-proactive-diagnostics.md)** 會自動設定 - 您不需要進行設定。假設您的網站有足夠的流量，如果一天當中某個時間的失敗要求或要求率不尋常增加，您就會收到一封電子郵件。警示包含診斷資訊。

以下是警示範例。

![顯示失敗之相關叢集分析的智慧警示範例](./media/app-insights-nrt-proactive-diagnostics/010.png)

第二種主動式偵測可發現失敗與因素 (例如位置、用戶端作業系統或瀏覽器類型) 之間的相互關聯。

### 度量警示

您可以設定[度量警示](app-insights-alerts.md)，以在任何度量超出某些期間的臨界值 (例如失敗計數、記憶體或頁面檢視) 的時候通知您。

![在 [計量瀏覽器] 中，選擇 [警示規則]，然後選擇 [加入警示]](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

### Availability

[可用性 Web 測試](app-insights-monitor-web-app-availability.md)會從我們位於世界各地的伺服器將要求傳送至您的網站。這類測試會在您的網站無法在網際網路上使用或回應速度很慢時通知您。

![Web 測試範例](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

## 匯出

有數種方式可讓您從 Application Insights 入口網站存取遙測資料︰

* 如果您想要讓大部分的遙測保留比標準保留期還久的時間，很適合使用[連續匯出](app-insights-export-telemetry.md)。
* 度量或搜尋刀鋒視窗頂端的[匯出按鈕](app-insights-metrics-explorer.md#export-to-excel)，可讓您傳送資料表和圖表到 Excel 試算表。
* [分析](app-insights-analytics.md)可提供功能強大的遙測查詢語言，也可以匯出結果。
* 資料存取 REST API 可用來搜尋及擷取資料。它可以執行分析查詢。
* 如果您想要[在 Power BI 中探索資料](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)，不需要用到「連續匯出」也可以這麼做。
 
## 資料管理

Application Insights 的使用方式有所限制，在某個程度上這取決於您選擇的價格機制。主要限制如下：

* 每分鐘遙測速率
* 每個月的資料點計數
* 資料的保留期

[取樣](app-insights-sampling.md)是一種降低成本和避免節流的機制。它會捨棄某個比例的遙測，以保留具代表性的樣本。相關聯的項目 (例如例外狀況和導致其發生的要求) 會保留下來或一起捨棄。在 ASP.NET 應用程式中，取樣會自動執行並套用於應用程式；不然，您可以將它設為要套用於對入口網站的擷取。

## 後續步驟

在執行階段開始使用︰

* [IIS 伺服器](app-insights-monitor-performance-live-website-now.md)
* [J2EE 伺服器](app-insights-java-live.md)

在開發階段開始使用︰

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

## 支援與意見反應

* 疑難排解與問題：
 * [疑難排解][qna]
 * [MSDN 論壇](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* 建議：
 * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* 部落格：
 * [Application Insights 部落格](https://azure.microsoft.com/blog/tag/application-insights)




## 影片

[![動畫簡介](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]

> [簡介動畫](https://www.youtube.com/watch?v=fX2NtGrh-Y0)


<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0713_2016-->