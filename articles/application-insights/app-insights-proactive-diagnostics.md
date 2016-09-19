<properties 
	pageTitle="Application Insights 中的主動式診斷 | Microsoft Azure" 
	description="Application Insights 會自動深入分析您的 App 遙測，並且警告您有潛在的問題。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="rakefetj" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="awills"/>

#  Application Insights 中的主動式診斷

 主動式診斷會自動警告您 Web 應用程式中可能有效能問題。它會針對您的應用程式傳送給 [Visual Studio Application Insights](app-insights-overview.md) 的遙測執行智慧分析。如果失敗率急遽上升或是用戶端或伺服器效能出現異常模式，您就會收到警示。這項功能不需要進行任何設定。只要您的應用程式傳送的遙測足夠，它就能發揮作用。

您可以存取來自所接收之電子郵件和來自 [主動式偵測] 刀鋒視窗的主動式偵測警示。



## 檢閱主動式偵測

您可以透過兩種方式探索偵測︰

* **接收來自 Application Insights 的電子郵件**。以下是典型範例︰

    ![電子郵件警示](./media/app-insights-proactive-diagnostics/03.png)

    按一下大型按鈕以在入口網站中開啟更多詳細資料。

* 應用程式的 [概觀] 刀鋒視窗上的 **[主動式偵測] 圖格**會顯示最新警示的計數。按一下圖格即可查看最新警示的清單。

![檢視最近的偵測](./media/app-insights-proactive-diagnostics/04.png)

選取警示來查看其詳細資料。


## 可偵測到哪些問題？

共有三種偵測︰

* [近乎即時的失敗警示](app-insights-proactive-failure-diagnostics.md)。我們使用機器學習服務來為應用程式設定預期的失敗要求率，其會與負載和其他因素相互關聯。如果失敗率超過預期限制，我們便會傳送警示。
* [異常診斷](app-insights-proactive-anomaly-diagnostics.md)。我們每天都會搜尋回應時間和失敗率中是否有異常模式。我們會將這些問題與位置、瀏覽器、用戶端作業系統、伺服器執行個體以及當天的時間等屬性相互關聯。
* [Azure 雲端服務](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/)。如果您的應用程式裝載於 Azure 雲端服務，而且角色執行個體有啟動失敗、經常回收或執行階段損毀等現象，您便會收到警示。

(每個通知中的說明連結會帶您前往相關文章。)


## 後續步驟

這些診斷工具可協助您檢查來自您的應用程式的遙測︰

* [計量瀏覽器](app-insights-metrics-explorer.md)
* [搜尋總管](app-insights-diagnostic-search.md)
* [分析 - 功能強大的查詢語言](app-insights-analytics-tour.md)

主動式偵測是完全自動的。但是，或許您會想要再設定一些警示？

* [手動設定的度量警示](app-insights-alerts.md)
* [可用性 Web 測試](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->