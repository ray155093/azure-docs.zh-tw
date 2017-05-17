---
title: "使用 Azure Application Insights 進行 Web 應用程式的使用者保留期分析 | Microsoft Docs"
description: "有多少使用者會回來使用您的應用程式？"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17f6062537714fbef7c8509261ac4875f8a44b6e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>使用 Application Insights 進行 Web 應用程式的使用者保留期分析

[Azure Application Insights](app-insights-overview.md) 中的 [保留] 刀鋒視窗可協助您分析回來使用您應用程式的使用者人數，以及他們執行特定工作或達成目標的頻率。 例如，如果您執行遊戲網站，您可以比較使用者在遊戲落敗與遊戲獲勝後，回到網站的數目。 這項知識可以協助您改善使用者體驗和商務策略。

## <a name="get-started"></a>開始使用

如果您在 Application Insights 入口網站中尚未看到 [保留期] 刀鋒視窗中的資料，[請了解如何開始使用使用量工具](app-insights-usage-overview.md)。

## <a name="the-retention-tool"></a>保留期工具

![保留期工具](./media/app-insights-usage-retention/retention.png)

a. [概觀] 頁面一律在這裡。 此外，您可以使用不同的參數設定來儲存已命名的報告。 儲存到「共用」可讓其他可存取資源的使用者能看見報告。

b.這是另一個 C# 主控台應用程式。 依預設，圖表所計算的所有使用者，是已使用任何自訂事件或從您應用程式接收到的頁面檢視。 選取一個或是子集，將重點放在特定的使用者活動。 

c. 在屬性新增一或多個篩選條件。 例如，您可以將重點放在特定國家或區域中的使用者。 設定篩選條件後，按一下 [更新]。

d. **還原成預設值**一律會清除自訂篩選條件和事件篩選條件。

e. 摘要圖表會顯示所選時間期間內的總計。

f. 此方格所顯示的使用者數目是在特定期間內回來並重複進行選取的動作。 每個資料列所代表的同群使用者，是在所顯示的時間週期內執行其中一個選取的動作。 資料列中的每個資料格會顯示該同群使用者中在稍後期間內至少回來使用一次的數目。 有些使用者可能會在不只一個期間內回來使用。

在圖表的時間範圍內使用應用程式的每位使用者，都會以圖表的一個資料列來代表。 每位使用者在圖表的時間範圍內第一次執行選取的動作時，都會計算在期間內。 因此，通常第一個資料列中都會有較大的數字。


## <a name="use-business-events-to-track-retention"></a>使用商務事件來追蹤保留期

若要取得最有用的保留期分析，請測量代表重要商務活動的事件。 

例如，許多在應用程式中開啟頁面的使用者可能不會玩它所顯示的遊戲。 因此，僅追蹤頁面檢視所提供的先前玩過遊戲後再回來玩的人數估計可能會有誤。 若要取得舊玩家的明確內容，您的應用程式應該在使用者實際玩過遊戲時才傳送自訂事件。  

最佳做法是將代表重要商務動作的自訂事件編碼，並使用這些來進行保留期分析。 若要擷取遊戲的結果，您需要撰寫一行程式碼，將自訂事件傳送至 Application Insights。 如果您是以網頁程式碼或 Node.JS 來撰寫，它看起來會像這樣︰

```JavaScript
    appinsights.trackEvent("won game");
```

或是以 ASP.NET 伺服器程式碼撰寫：

```C#
   telemetry.TrackEvent("won game");
```

[深入了解撰寫自訂事件](app-insights-api-custom-events-metrics.md#trackevent)。


## <a name="next-steps"></a>後續步驟

* [使用量概觀](app-insights-usage-overview.md)
* [使用者和工作階段](app-insights-usage-segmentation.md)
* [撰寫自訂事件的程式碼](app-insights-api-custom-events-metrics.md)


