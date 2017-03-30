---
title: "分析 - 強大的 Azure Application Insights 搜尋工具 | Microsoft Docs"
description: "分析概觀，強大的 Application Insights 診斷搜尋工具。 "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 4fbd80f7e9775fe3c12a54302be4a162d2102e2f
ms.lasthandoff: 03/16/2017


---
# <a name="analytics-in-application-insights"></a>Application Insights 中的分析
[分析](app-insights-analytics.md)是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。 這些頁面說明 Analytics 查詢語言。 

* **[觀看簡介影片](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* 如果您的應用程式還未將資料傳送至 Application Insights，則**[在我們的模擬資料上測試分析](https://analytics.applicationinsights.io/demo)**。
* **[SQL 使用者的功能提要](https://aka.ms/sql-analytics)**會翻譯成最常見的習慣用語。
* **[語言參考](app-insights-analytics-reference.md)** 了解如何使用 Analytics 查詢語言的分析查詢語言的所有強大功能。

## <a name="queries-in-analytics"></a>分析中的查詢功能
標準的查詢是一個「來源」資料表，後面接著一系列由 `|` 隔開的「運算子」**。 

例如，讓我們來了解海德拉巴的市民在一天當中的哪些時間試用我們的 Web 應用程式。 同時我們要看看針對他們的 HTTP 要求傳回哪些結果碼。 

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

我們會計算不同的用戶端 IP 位址，並以過去 7 天每天的小時為單位將它們群組。 

使用橫條圖展示來顯示結果，選擇以不同的回應碼來堆疊結果：

![選擇橫條圖、X 和 Y 軸，然後分割](./media/app-insights-analytics/020.png)

看來我們的應用程式在海德拉巴的午休時間及就寢時間最受歡迎。 (而且我們應該調查那些 500 的代碼。)

也有強大的統計運算功能︰

![](./media/app-insights-analytics/025.png)

這個語言具有許多吸引人的功能︰

* [篩選](app-insights-analytics-reference.md#where-operator) 未經處理的應用程式遙測，包括您的自訂屬性和計量。
* [加入](app-insights-analytics-reference.md#join-operator) 多個資料表 – 將要求與頁面檢視、相依性呼叫、例外狀況和記錄追蹤相互關聯。
* 功能強大的統計 [彙總](app-insights-analytics-reference.md#aggregations)。
* 功能如同 SQL 一般強大，但更容易用來進行複雜的查詢︰您可以使用管線將資料從某一個基本運算傳送到下一個運算，而不需使用巢串陳述式。
* 立即且功能強大的視覺效果。
* [將圖表釘選到 Azure 儀表板](app-insights-analytics-using.md#pin-to-dashboard)。
* [將查詢匯出至 Power BI](app-insights-analytics-using.md#export-to-power-bi)。
* 您可以使用 [REST API](https://dev.applicationinsights.io/) 以程式設計方式 (例如從 Powershell) 執行查詢。


## <a name="connect-to-your-application-insights-data"></a>連接到您的 Application Insights 資料
在 Application Insights 中，從 app 的 [概觀刀鋒視窗](app-insights-dashboards.md) 開啟 [分析]： 

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-insights-analytics/001.png)


## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>後續步驟
* 我們建議您從 [語言教學](app-insights-analytics-tour.md)開始。



