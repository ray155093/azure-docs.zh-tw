---
title: "行動應用程式的效能監視 - Azure Application Insights | Microsoft Docs"
description: "行動應用程式開發人員的應用程式效能和使用情況監視。 具有 HockeyApp 與 Application Insights 的 DevOps。"
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: c4077aa88a401a1c75c0d3fbcd58186f7cb137c2


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>使用 Application Insights 和 HockeyApp 進行的行動分析
使用 [HockeyApp](https://hockeyapp.net/) 來監視您測試版測試與已部署之行動及傳統型應用程式的效能和使用情況。 使用 [Azure Application Insights](app-insights-overview.md) 來監視支援 Web 服務和後端應用程式。 在「分析」中分析來自用戶端和伺服器應用程式的資料，並在 Azure 儀表板中將圖表並排顯示。

「Microsoft 開發人員分析」提供兩種應用程式效能監視解決方案：

* **適用於行動應用程式的 HockeyApp**：也適用於傳統型應用程式。
  * 將測試版本散發給選取的使用者。
  * 損毀分析。
  * 用於使用情況分析的自訂遙測。
* **適用於網站的 Application Insights**：也適用於服務和後端應用程式。
  * 效能與使用情況度量及警示。
  * 例外狀況報告和診斷追蹤。
  * 搭配篩選的診斷搜尋和相關的遙測連結。

兩種解決方案都提供下列功能：

* 用於診斷和分析的強大**[分析查詢語言](app-insights-analytics.md)**。
* **[將資料匯出](app-insights-export-telemetry.md)**到您自己的儲存體。
* 分析圖表和表格的**整合式儀表板**顯示。

## <a name="monitor-your-app-components"></a>監視您的應用程式元件
請依照這些頁面中的指示，在您的程式碼中安裝 SDK 並開始追蹤您的應用程式。

### <a name="web-apps---application-insights"></a>Web 應用程式 - Application Insights
* [ASP.NET Web 應用程式](app-insights-asp-net.md) 
* [Java Web 應用程式](app-insights-java-get-started.md)
* [Node.js Web 應用程式](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Azure 雲端服務](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>行動應用程式 - HockeyApp
* [iOS App](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X App](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android App](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [通用 Windows App](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 和 8.1 App](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation App](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

針對 Windows 傳統型應用程式︰建議使用 HockeyApp。 但是您也可以[將遙測從 Windows 應用程式傳送到 Application Insights](app-insights-windows-desktop.md)。 您可以這麼做來使用 Application Insights 進行實驗。

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>分析和匯出 HockeyApp 遙測
您可以 [設定橋接器](app-insights-hockeyapp-bridge-app.md)，以使用 Application Insights 的分析和連續匯出功能來調查 HockeyApp 自訂和記錄遙測。




<!--HONumber=Jan17_HO4-->


