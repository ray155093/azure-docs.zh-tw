---
title: "Azure Application Insights 中的即時計量串流 | Microsoft Docs"
description: "即時監視 Web 應用程式的效能，以觀察新版或其他變更的效果。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc6acf7b24bb017e4a93df1c15ed493a4442d6b5
ms.openlocfilehash: 917261e32845b53f2f2f3d12f60ca5fa87d441c7
ms.lasthandoff: 02/27/2017


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>即時計量串流：適用於關閉監視的即時計量
即時計量串流顯示您的 [Application Insights](app-insights-overview.md) 計量正處於這個非常時刻，接近即時的延遲為&1; 秒。 當您發行了新的組建且想要確定一切都如預期般運作，或者要即時調查某個事件時，此種立即監視非常有用。

不同於[計量瀏覽器](app-insights-metrics-explorer.md)，即時計量串流會顯示一組固定的計量。 只要資料仍在圖表上就會保存，之後便會捨棄該資料。

即時計量串流資料是免費的︰它不會加至您的帳單。

![即時計量串流影片](./media/app-insights-live-stream/youtube.png) [即時計量串流影片](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

![在 [概觀] 刀鋒視窗中，按一下 [即時資料流]](./media/app-insights-live-stream/live-stream.png)



## <a name="live-failures"></a>即時失敗

如果有記錄任何失敗或例外狀況，「即時資料流」就會對其進行取樣。 按一下 [暫停] 以保存特定樣本，然後選取事件來顯示其詳細資料。

![取樣的即時失敗](./media/app-insights-live-stream/live-stream-failures.png)


[Application Insights SDK for Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的最新版本有提供「即時計量資料流」。

## <a name="filter-by-server-instance"></a>依伺服器執行個體篩選

如果您想要監視特定伺服器角色執行個體，可以依伺服器篩選。

![取樣的即時失敗](./media/app-insights-live-stream/live-stream-filter.png)


## <a name="troubleshooting"></a>疑難排解

沒有資料？ 即時計量串流會使用不同於其他 Application Insights 遙測的連接埠。 請確定[這些連接埠](app-insights-ip-addresses.md)在您的防火牆中為開啟狀態。



## <a name="next-steps"></a>後續步驟
* [使用 Application Insights 監視使用情況](app-insights-overview-usage.md)
* [使用診斷搜尋](app-insights-diagnostic-search.md)


