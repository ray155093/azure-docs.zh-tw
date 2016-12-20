---
title: "開發人員分析"
description: "使用 Visual Studio、Application Insights 和 HockeyApp 的 DevOps"
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 1f1207a5-9019-451f-bff0-c67c4685ba32
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: c19e0837ccff447ebcd4319aaebfdc763b71233c


---
# <a name="developer-analytics-with-application-insights-and-hockeyapp"></a>使用 Application Insights 和 HockeyApp 的開發人員分析


許多專案是以快速的 [DevOps](https://en.wikipedia.org/wiki/DevOps) 週期進行運作。 他們會建置和散發其應用程式、取得有關其執行方式和使用者使用方式的意見反應，然後使用該知識進一步規劃開發週期。 

若要監視使用方式和效能，具有來自即時應用程式的遙測以及來自使用者本身的意見反應是很重要的。 

有許多系統是從多個元件建置而來的：Web 服務、後端處理器或資料存放區，以及在使用者瀏覽器中執行的用戶端軟體，或者做為手機或其他裝置中的應用程式。 來自這些不同元件的遙測必須結合在一起。

某些發行是有限制地散發給指定的測試人員；我們也安排了正式發行前小眾測試 (透過有限的對象來測試新功能) 和 A|B 測試 (平行測試替代的 UI)。

管理散發套件，以及整合多個用戶端和伺服器元件上的監視並不是件簡單的工作。 此程序是應用程式架構不可或缺的一部分︰我們無法在沒有反覆開發週期和良好監視工具的情況下建立這種系統。

在本文中，我們將探討如何使 devOps 週期的監視層面與程序的其他部分相符。 

如果您想要查看特定範例，有一個含有多個用戶端和伺服器元件的 [有趣案例研究](http://aka.ms/mydrivingdocs) 。

## <a name="a-devops-cycle"></a>DevOps 週期
Visual Studio 和開發人員分析工具提供良好的 devOps 體驗。 例如，以下是 Web 應用程式 (這可以是 Java、Node.js 或 ASP.NET) 的一般週期︰

![Web 應用程式 DevOps 週期](./media/app-insights-developer-analytics/040.png)

* 開發人員會簽入程式碼儲存機制，或合併到主要分支。 儲存機制是此圖中的 Git，但它也可以是 [Team Foundation 版本控制](https://www.visualstudio.com/docs/tfvc/overview)。
* 變更會觸發組建和單元測試。 組建服務可以位於 [Visual Studio Team Services 或其內部部署對應 Team Foundation Server](https://www.visualstudio.com/docs/vsts-tfs-overview)中。 
* 成功的建置和單元測試可以 [觸發自動部署](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。 Web 應用程式主機可以是您自己的 Web 伺服器或 Microsoft Azure。 
* 來自即時 App 的遙測，不論是來自伺服器還是[來自用戶端瀏覽器](app-insights-javascript.md)，都會傳送至 [Application Insights](app-insights-overview.md)。 您可以在該處分析 App 和使用模式的效能。 功能強大的 [搜尋工具](app-insights-analytics.md) 可協助您診斷任何問題。 [警示](app-insights-alerts.md) 可確保您會在問題發生時立即得知相關資訊。 
* 您的下一個開發週期是透過您的即時遙測分析來通知。

### <a name="device-and-desktop-apps"></a>裝置和傳統型應用程式
針對裝置和傳統型應用程式，週期的散發部分會稍有不同，因為我們不只上傳到一或兩部伺服器。 相反地，成功的組建和單元測試可以 [觸發上傳至 HockeyApp](https://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs)。 HockeyApp 會管理要給測試使用者 (或一般大眾，如果您偏好的話) 的散發套件。 

![裝置 DevOps 週期](./media/app-insights-developer-analytics/030.png)

HockeyApp 也會以下列形式收集效能和使用資料︰

* 一字不漏的使用者意見反應與螢幕擷取畫面
* 當機報告
* 由您編碼的自訂遙測。

再次提醒，當您按照取得的意見反應來擬定未來開發計劃時，即會完成 devOps 週期。

## <a name="setting-up-developer-analytics"></a>設定開發人員分析
針對應用程式的每個元件 (行動、Web 或傳統型)，步驟基本上都一樣。 對於許多類型的 App，Visual Studio 會自動執行這其中的一些步驟。

1. 將適當的 SDK 新增至您的 App。 針對裝置應用程式，它是 HockeyApp，針對 Web 服務則是 Application Insights。 每個對於不同的平台都有數個變化。 (可以使用任一個適用於傳統型應用程式的 SDK，不過我們建議使用 HockeyApp)。
2. 根據您使用的 SDK，利用 Application Insights 或 HockeyApp 入口網站註冊您的 App。 您將能在此處查看來自您即時 App 的分析。 您會取得您設定至 App 的檢測金鑰或識別碼，如此一來，SDK 就會知道要將其遙測傳送到何處。
3. 新增自訂程式碼 (如有需要) 來記錄事件或度量，以協助您診斷或分析效能或使用方式。 有許多內建的監視功能，因此您不需要在第一個週期中使用此功能。
4. 針對裝置應用程式：
   * 將偵錯組建上傳至 HockeyApp。 您可以從該處將它散發給測試使用者小組。 每當您上傳後續的組建之後，該小組將會收到通知。
   * 當您設定持續建置服務時，請建立發行定義，使用外掛程式步驟來上傳至 HockeyApp。

### <a name="analytics-and-export-for-hockeyapp-telemetry"></a>分析和匯出 HockeyApp 遙測
您可以 [設定橋接器](app-insights-hockeyapp-bridge-app.md)，以使用 Application Insights 的分析和連續匯出功能來調查 HockeyApp 自訂和記錄遙測。

## <a name="next-steps"></a>後續步驟
以下是適用於不同 App 類型的詳細指示︰

* [ASP.NET Web 應用程式](app-insights-asp-net.md) 
* [Java Web 應用程式](app-insights-java-get-started.md)
* [Node.js Web 應用程式](https://github.com/Microsoft/ApplicationInsights-node.js)
* [iOS App](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X App](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android App](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [通用 Windows App](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 和 8.1 App](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation App](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)




<!--HONumber=Nov16_HO3-->


