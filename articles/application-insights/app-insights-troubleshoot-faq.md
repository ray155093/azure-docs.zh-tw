---
title: "Azure Application Insights 的疑難排解和問題 | Microsoft Docs"
description: "Azure Application Insights 發生不明確或無法運作的情況嗎？ 試試這裡。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: e066a7fc671399ba44bec35a2ea860fccddb4cc5


---
# <a name="questions---application-insights-for-aspnet"></a>問題 - ASP.NET 的 Application Insights
## <a name="configuration-problems"></a>組態問題
*我在設定下列項目時有問題：*

* [.NET 應用程式](app-insights-asp-net-troubleshoot-no-data.md)
* [監視已在執行的應用程式](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Azure 診斷](app-insights-azure-diagnostics.md)
* [Java Web 應用程式](app-insights-java-troubleshoot.md)
* [其他平台](app-insights-platforms.md)

我的伺服器沒有傳回資料

* [設定防火牆例外狀況](app-insights-ip-addresses.md)
* [設定 ASP.NET 伺服器](app-insights-monitor-performance-live-website-now.md)
* [設定 Java 伺服器](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>我是否可以搭配 ... 來使用 Application Insights 嗎？
[請參閱「平台」][platforms]

## <a name="is-it-free"></a>它是免費的嗎？

是，可作為實驗用途。 在基本定價方案中，您的應用程式每個月可以免費傳送一定額度的資料。 免費額度足夠因應開發用途和為少量使用者發佈應用程式。 您可以設定上限以防止處理超過指定的資料量。

您需要使用「企業」方案才能獲得特定的功能，例如「連續匯出」。 這會產生每日費用。

[請參閱定價方案](https://azure.microsoft.com/pricing/details/application-insights/)。


## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights 在我的專案中修改什麼？
詳細資料視專案類型而定。 若是 Web 應用程式：

* 將這些檔案新增至您的專案：

  * ApplicationInsights.config。
  * ai.js
* 安裝這些 NuGet 套件：

  * *Application Insights API* - 核心 API
  * *Application Insights API for Web Applications* - 用來從伺服器傳送遙測
  * *Application Insights API for JavaScript Applications* - 用來從用戶端傳送遙測

    套件包含這些組件：
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 將項目插入至：

  * Web.config
  * packages.config
* (僅限新專案 - 如果您[將 Application Insights 新增至現有的專案][start]，則必須手動執行。)將程式碼片段插入至用戶端和伺服器程式碼中，以使用 Application Insights 資源識別碼將這些片段初始化。 例如，在 MVC 應用程式中，程式碼會插入至主版頁面 Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>如何從舊版 SDK 升級？
請參閱您的應用程式類型所適用之 SDK 的[版本資訊](app-insights-release-notes.md)。

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>如何變更我的專案將資料傳送到哪一個 Azure 資源？
在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config`，然後選擇 [更新 Application Insights]。 您可以將資料傳送至 Azure 中的現有資源或新資源。 更新精靈會變更 ApplicationInsights.config 中的檢測金鑰，這決定伺服器 SDK 將您的資料送往何處。 除非您取消選取 [全部更新]，否則也會變更金鑰出現在您網頁中的位置。

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>資料會保留在入口網站多久？ 是否安全？
請參閱[資料保留和隱私權][data]。

## <a name="logging"></a>記錄
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>如何在診斷搜尋中查看 POST 資料？
我們不會自動記錄 POST 資料，但您可以使用 TrackTrace 呼叫：將資料放置到訊息參數中。 相較於字串屬性的限制，此呼叫可容許較長的大小，不過您無法篩選。

## <a name="security"></a>安全性
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>保留在入口網站上的資料安全嗎？ 會保留多久？
請參閱[資料保留和隱私權][data]。

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> 我是否已啟用 Application Insights 中的所有項目？
| 您應該會看到 | 如何取得 | 取得原因 |
| --- | --- | --- |
| 可用性圖表 |[Web 測試](app-insights-monitor-web-app-availability.md) |知道您的 Web 應用程式已啟動 |
| 伺服器應用程式效能：回應時間... |[將 Application Insights 新增至您的專案](app-insights-asp-net.md)或[在伺服器上安裝 AI 狀態監視器](app-insights-monitor-performance-live-website-now.md) (或撰寫您自己的程式碼來[追蹤相依性](app-insights-api-custom-events-metrics.md#trackdependency)) |偵測效能問題 |
| 相依性遙測 |[在伺服器上安裝 AI 狀態監視器](app-insights-monitor-performance-live-website-now.md) |診斷資料庫或其他外部元件的問題 |
| 取得例外狀況的堆疊追蹤 |[在程式碼中插入 TrackException 呼叫](app-insights-search-diagnostic-logs.md#exceptions) (但部分會自動報告) |偵測並診斷例外狀況 |
| 搜尋記錄追蹤 |[新增記錄配接器](app-insights-search-diagnostic-logs.md) |診斷例外狀況、效能問題 |
| 用戶端使用基本概念：頁面檢視、工作階段... |[網頁中的 JavaScript 初始設定式](app-insights-javascript.md) |流量分析 |
| 用戶端自訂度量 |[追蹤網頁中的呼叫](app-insights-api-custom-events-metrics.md) |增強使用者經驗 |
| 伺服器自訂度量 |[追蹤伺服器中的呼叫](app-insights-api-custom-events-metrics.md) |商業智慧 |

## <a name="automation"></a>自動化
您可以[撰寫 PowerShell 指令碼](app-insights-powershell.md)來建立和更新 Application Insights 資源。

## <a name="more-answers"></a>更多解答
* [Application Insights 論壇](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Feb17_HO2-->


