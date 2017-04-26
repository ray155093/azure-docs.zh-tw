---
title: "使用 Azure Application Insights 進行 Web 應用程式的使用量分析 | Microsoft Docs"
description: "使用 Application Insights 進行使用量分析的概觀"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2715207e548fc57b1896f5736731be3881256cbf
ms.lasthandoff: 04/13/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>使用 Application Insights 進行 Web 應用程式的使用量分析
了解使用者如何使用您的應用程式，可讓您將開發工作的焦點放在對您的使用者來說最重要的案例上，並深入解析他們認為較容易或較難達成的目標。

[Azure Application Insights](app-insights-overview.md) 提供兩種層級的使用情況追蹤：

* **使用者、工作階段與頁面檢視資料** - 預設提供。  
* **自訂遙測** - 您可以[撰寫程式碼](app-insights-api-custom-events-metrics.md)來透過應用程式的使用者體驗追蹤您的使用者。 


## <a name="get-started"></a>開始使用

若要獲得最佳體驗，請同時在您的應用程式伺服器程式碼和網頁中安裝 Application Insights。 您應用程式的用戶端和伺服器元件會將遙測資料傳送回 Azure 入口網站以供分析。

1. **伺服器程式碼：**為您的 [ASP.NET](app-insights-asp-net.md)、[Azure](app-insights-azure.md)、[Java](app-insights-java-get-started.md)、[Node.js](app-insights-nodejs.md) 或[其他](app-insights-platforms.md)應用程式安裝適當的模組。

    * *不想安裝伺服器程式碼嗎？請直接[建立 Azure Application Insights 資源](app-insights-create-new-resource.md)。*

2. **網頁程式碼：**開啟 [Azure 入口網站](https://portal.azure.com)、開啟您應用程式的 Application Insights 資源，然後開啟 [快速入門] > [監視及診斷用戶端應用程式]。 

    ![將指令碼複製到您主版頁面的標頭。](./media/app-insights-web-track-usage/02-monitor-web-page.png)


3. **取得遙測資料：**以偵錯模式執行您的專案幾分鐘，然後在 Application Insights 的 [概觀] 刀鋒視窗中尋找結果。

    發佈您的應用程式以監視應用程式的效能，並了解使用者如何利用您的應用程式。

## <a name="usage-analysis-out-of-the-box"></a>現成的使用情況分析
開啟 [使用量] 刀鋒視窗。

![使用者數、工作階段數及頁面檢視數圖表](./media/app-insights-web-track-usage/14-usage.png)

將滑鼠移至圖形上的空白部分，以查看特定點的計數。 否則，數字會顯示一段期間的彙總值，例如一段期間的平均、總計或獨特使用者計數。

這些圖表顯示什麼？

* **使用者：** 圖表的時間範圍內不同作用中使用者的計數。 在 Web 應用程式中，是使用 Cookie 來計算使用者。 使用數個瀏覽器、清除 Cookie 或使用私密瀏覽功能的人員將被計入數次。
* 如果您已在程式碼中插入對 [setAuthenticatedUser()](app-insights-api-custom-events-metrics.md#authenticated-users) 的呼叫，就會計算「已驗證的使用者」。
* **工作階段：**使用中工作階段計數。 Web 工作階段在 30 分鐘無活動後會被計入。 
* **頁面檢視** 計算對 trackPageView() 呼叫的數目，通常在每個網頁中呼叫一次。

逐一點選任何圖表以查看詳細資料。 請注意，您可以變更圖表的時間範圍。

### <a name="where-do-my-users-live"></a>我的使用者住在何處？
按一下 [使用者] 圖表的空白部分，以開啟顯示更多詳細資料的另一個刀鋒視窗：

![在 [使用量] 刀鋒視窗中，按一下 [使用者] 圖表](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>他們使用什麼瀏覽器或作業系統？

按一下 [使用者] 圖表上的 [編輯]，然後依屬性 (例如 [瀏覽器]、[作業系統] 或 [城市]) 將資料分組 (區段)： 

![選取顯示單一度量的圖表，將分組切換成開啟，並選擇屬性](./media/app-insights-web-track-usage/03-browsers.png)

若要查看完整的一組計數，請將圖表類型切換成 [方格]。 您也可以選擇顯示額外的計量：

![多資料行方格圖表](./media/app-insights-web-track-usage/multi-column-grid.png)

針對圖形化圖表類型，您可以依屬性分組或選取多個計量，但不能同時使用兩者。 此圖表會比較兩個計量、使用者及[已驗證的使用者](app-insights-api-custom-events-metrics.md#authenticated-users)。 

![選取圖表，搜尋並勾選或取消勾選度量。](./media/app-insights-web-track-usage/031-dual.png)



## <a name="page-views"></a>頁面檢視
從 [使用量] 刀鋒視窗，一路點選進入頁面檢視圖表以取得更詳細的版本，以及最受歡迎頁面的明細：

![從 [概觀] 刀鋒視窗，按一下 [頁面檢視] 圖表](./media/app-insights-web-track-usage/05-games.png)

上述範例來自遊戲網站。 從這些圖表，我們可以立即看到：

* 在上一週使用量未改善。 也許我們應該考慮搜尋引擎最佳化？
* 網球是最受歡迎的遊戲頁面。 讓我們將焦點放在此頁面的進一步改善上。
* 平均而言，使用者大約每週瀏覽網球頁面三次。 (與使用者相比，工作階段數大約多出三倍)。
* 大多數使用者都是在美國工作週且在工作時間瀏覽此網站。 也許我們應該在網頁上提供一個 [快速隱藏] 按鈕。
* 圖表上的[註解](app-insights-annotations.md)會顯示新版網站的部署時間。 沒有任何一個最近的部署對使用量有明顯的影響。

## <a name="custom-tracking"></a>自訂追蹤
我們假設不要在個別網頁實作每個遊戲，您決定將它們全部重新分解成相同的單一頁面應用程式，其中多數功能的網頁都是以 Javascript 編寫。 這可讓使用者在遊戲之間快速切換，或甚至是在一個頁面中有數個遊戲。

但您仍想要 Application Insights 記錄開啟每個遊戲的次數，以他們在不同頁面上完全相同的方式記錄。 那很簡單：只需要插入呼叫到 JavaScript 中您要記錄已開啟新「頁面」的遙測模組即可：

```JavaScript
    telemetryClient.trackPageView(game.Name);
```
這個呼叫會模擬記錄頁面檢視的遙測。  不過，您不會總是想要將訊息與頁面檢視混在一起。 改為使用自訂事件。 您可以從網頁或 Web 伺服器傳送它們：


```JavaScript

    telemetryClient.trackEvent("GameEnd");
```

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");
```

```VB

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")
```

您可以透過許多方式使用[插入到您 Web 或伺服器程式碼中的自訂遙測](app-insights-api-custom-events-metrics.md#trackevent)，以了解您應用程式的使用情況。

若要檢視 TrackEvent() 所傳送的事件：開啟 [計量瀏覽器]、新增一個新的圖表，然後編輯它。 您的計量會顯示在 [自訂計量] 底下。 

![顯示自訂事件的圖表。](./media/app-insights-web-track-usage/06-eventsSegment.png)

如果您在事件中設定[屬性值](app-insights-api-custom-events-metrics.md#properties) (也稱為維度)，您便可以依這些屬性值進行分組或篩選。

您可以建立多個圖表來將其他計量和事件中的變更相互關聯。 例如，在玩很多遊戲的時候，您會預期看到被放棄的遊戲也有上升。 但是，被放棄的遊戲上升不成比例，您會想要知道高負載是否造成問題，使得使用者覺得無法接受。

## <a name="drill-into-specific-events"></a>深入特定事件
若要更了解一般工作階段的進行情況，您可能想要著重在包含特定事件類型的特定使用者工作階段。

從事件方格中，一路點選進入感興趣的事件，然後選取最近的特定出現項目：

![在摘要圖表下的清單中，按一下事件](./media/app-insights-web-track-usage/08-searchEvents.png)

讓我們查看工作階段的所有遙測，其中發生了特定 NoGame 事件。

![按一下 [工作階段的所有遙測]](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

沒有例外狀況，因此沒有因為一些錯誤而防止使用者玩遊戲。

為此原因，我們可以將頁面檢視以外的所有類型的遙測篩選掉：

![](./media/app-insights-web-track-usage/10-filter.png)

而現在我們可以看到，這個使用者登入只是為了要查看最新的分數。 也許我們應該考慮開發可讓這個動作更易於進行的使用者劇本。 (而我們應該實作自訂事件，在這個特定劇本發生時提出報告。)

## <a name="filter-search-and-segment-your-data-with-properties"></a>使用屬性來篩選、搜尋和分割您的資料
您可以附加任意標記和數值到事件。

*網頁中的 JavaScript*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*伺服器上的 C#*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string>
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*伺服器上的 VB*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

以相同的方式將屬性附加至頁面檢視：

*網頁中的 JavaScript*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

在「診斷搜尋」中，逐一點選個別事件出現次數可檢視屬性。

![在事件清單中，開啟某個事件，然後按一下 [...] 以查看更多屬性](./media/app-insights-web-track-usage/11-details.png)

使用 [搜尋] 欄位來查看具有特定屬性值的事件出現次數。

![在 [搜尋] 欄位中輸入值](./media/app-insights-web-track-usage/12-searchEvents.png)

## <a name="edit-and-create-queries-over-your-telemetry"></a>編輯和建立與您遙測資料相關的查詢

按一下任何圖表上的 [Azure 分析] 圖示，以開啟可供您編輯的對等查詢。
向下捲動以查看是否有多個產生的查詢。 將游標放在任何查詢中，然後按一下 [執行]。 

或者，從 [概觀] 刀鋒視窗上的圖示開啟 [分析]，然後撰寫您自己的查詢，或在 [分析] 首頁上嘗試一些範例查詢。


![含有已產生之查詢的 [分析] 視窗](./media/app-insights-web-track-usage/open-analytics.png)

[深入了解 Azure 分析查詢語言](app-insights-analytics.md)。

針對使用量分析，您可能會對下列資料表特別感興趣：

* `customEvents` - [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 呼叫的結果。
* `pageViews` - 在用戶端瀏覽器中開啟的頁面計數，或對 [trackPageView()](app-insights-api-custom-events-metrics.md#page-views) 的呼叫。


## <a name="a--b-testing"></a>A | B 測試
如果您不知道哪個功能的變數將更可能成功，請兩者都釋出，讓兩者都可供不同使用者存取。 測量每一個的成功，然後移至整合的版本。

針對此技術，您會附加獨特的標記到每個應用程式版本所傳送的所有遙測。 您可以在作用中 TelemetryContext 中定義屬性來執行該動作。 這些預設屬性會加入到應用程式傳送的每個遙測訊息 - 不只是您的自訂訊息，還有標準遙測。

在 Application Insights 入口網站中，您將可以依標記篩選並分組 (分段) 資料，以便比較不同版本。


```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```


```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

個別的遙測可以覆寫預設值。

您可以設定全域初始設定式，使得所有新 TelemetryClients 會自動使用您的內容。

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

在應用程式初始設定式例如 Global.asax.cs 中：

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## <a name="build---measure---learn"></a>建置 - 測量 - 了解
使用分析時，它會成為開發週期的整合部分 - 而不只是協助解決問題的用途。 以下是一些秘訣：

* 判斷應用程式的關鍵度量。 您是想要盡可能多的使用者，或您只需要少量開心使用的使用者？ 您要讓造訪或銷售獲得最大效益？
* 計劃測量每個劇本。 在草擬新使用者劇本或功能，或計劃更新現有功能時，一律要考慮如何測量變更的成功。 編寫開始之前，詢問「如果有作用，這對我們的度量將有什麼影響？ 我們是否應該追蹤任何新事件？
  」 而當然，當功能上線時，請確定您查看分析並依據結果採取行動。
* 將其他度量與關鍵度量產生交互關聯。 例如，如果您加入「我的最愛」功能，您會想要知道使用者加入最愛的頻率。 但也許知道他們回到我的最愛的頻率會更有趣。 同時，更重要的是，使用我的最愛的客戶最終是否購買您的更多產品？
* Canary 測試。 設定可讓您使新功能僅對部分使用者顯示的功能開關。 使用 Application Insights 來查看新功能是否正依您設想的方式提供使用。 進行調整，然後發行給更廣大的對象。
* 與您的使用者討論！ 單單分析是不足夠的，但可以補充以保有良好的客戶關係。

## <a name="learn-more"></a>詳細資訊
* [偵測、分及和診斷應用程式中的損毀和效能問題](app-insights-detect-triage-diagnose.md)
* [開始使用 .NET 的 Application Insights](app-insights-detect-triage-diagnose.md)
* [使用 API - 概觀](app-insights-api-custom-events-metrics.md)
* [JavaScript API 參考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)



