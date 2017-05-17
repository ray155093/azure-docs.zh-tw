---
title: ".NET 應用程式的 Azure Application Insights 快照集偵錯工具 | Microsoft Docs"
description: "在生產環境 .NET 應用程式中擲回例外狀況時，會自動收集偵錯快照集"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 應用程式中的例外狀況偵錯快照集

*這項功能處於預覽狀態。*

例外狀況發生時，會自動從即時 web 應用程式收集偵錯快照集。 快照集會顯示擲回例外狀況時原始程式碼和變數的狀態。 [Application Insights](app-insights-overview.md) 中的快照集偵錯工具會監視 web 應用程式的例外狀況遙測。 它會收集前幾個擲回例外狀況的快照集，讓您取得診斷生產環境中問題所需的資訊。 將[快照集收集器 NuGet 套件](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)納入您的應用程式，並選擇性地設定 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中的集合參數。 快照集會顯示在 Application Insights 入口網站中的[例外狀況](app-insights-asp-net-exceptions.md)。

您可以檢視入口網站中的偵錯快照集，以查看呼叫堆疊並檢查每個呼叫堆疊框架的變數。 若要使用原始程式碼取得更強大的偵錯體驗，可[下載 Visual Studio 的快照集偵錯工具擴充功能](https://aka.ms/snapshotdebugger)，以 Visual Studio 2017 Enterprise 開啟快照集。

快照集的集合可用於執行 .NET Framework 4.6 和更新版本、且在 Azure Compute 的 IIS 或 Azure App Service 中託管的 ASP.NET Web Apps。

## <a name="configure-snapshot-collection"></a>設定快照集的集合

1. 如果您尚未這麼做，請[啟用 Web 應用程式中的 Application Insights](app-insights-asp-net.md)。

2. 將 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件納入您的應用程式。 

3. 檢閱套件已新增至 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 的預設選項。

```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
      <IsEnabled>true</IsEnabled>
      <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
      <!-- DeveloperMode is a property on the active TelemetryChannel. -->
      <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
      <!-- How many times we need to see an exception before we ask for snapshots. -->
      <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
      <!-- The maximum number of examples we create for a single problem. -->
      <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
      <!-- The maximum number of problems that we can be tracking at any time. -->
      <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
      <!-- How often to reset problem counters. -->
      <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
      <!-- The maximum number of snapshots allowed in one minute. -->
      <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
      <!-- The maximum number of snapshots allowed per day. -->
      <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
    </Add>
  </TelemetryProcessors>
```

快照集只會收集 Application Insights SDK 可見的例外狀況。 在某些情況下，您可能需要[設定例外狀況集合](app-insights-asp-net-exceptions.md#exceptions)，才可在入口網站中出現快照集時查看例外狀況。

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>Application Insights 入口網站中的偵錯快照集

如果快照集可用於指定的例外狀況或問題識別碼，則 Application Insights 入口網站中的[例外狀況](app-insights-asp-net-exceptions.md)會出現 [開啟偵錯快照集] 連結。

![例外狀況的 [開啟偵錯快照集] 按鈕](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

在 [偵錯快照集] 檢視中，您會看到呼叫堆疊和變數窗格。 在 [呼叫堆疊] 窗格中選取呼叫堆疊的框架，可讓您檢視 [變數] 窗格中的本機變數和該函式呼叫的參數。

![檢視入口網站中的偵錯快照集](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

快照集可能包含機密資訊，依預設為不可檢視。 若要檢視快照集，您必須擁有在入口網站中指派給您訂用帳戶或資源的 `Application Insights Snapshot Debugger` 角色。 此角色目前只能由訂用帳戶擁有者對每個使用者逐一進行指派。 目前不支援將角色指派給 Azure Active Directory 群組。

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>Visual Studio 2017 Enterprise 的偵錯快照集
您可以按 [下載快照集] 按鈕，下載可利用 Visual Studio 2017 Enterprise 開啟的 `.diagsession` 檔案。 開啟 `.diagsession` 檔案目前需要您先[下載並安裝 Visual Studio 的快照集偵錯工具擴充功能](https://aka.ms/snapshotdebugger)。

開啟快照集檔案之後，即會進入 Visual Studio 的 [小型傾印偵錯] 頁面，您可以在當中按一下 [偵錯受管理程式碼]，開始將快照集進行偵錯。 系統會帶您前往擲回例外狀況的程式碼行，您可將程序的目前狀態進行偵錯。

![檢視 Visual Studio 中的偵錯快照集](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

下載的快照集會包含您 Web 應用程式伺服器上找到的任何符號檔。 若要建立快照集資料與原始程式碼的關聯，就需要這些符號檔。 針對 Azure App Service 應用程式，當您發佈 Web Apps 時，請務必啟用符號部署。

## <a name="how-snapshots-work"></a>快照集運作方式

當您的應用程式啟動時，會建立個別的快照集上傳者程序，可針對快照集要求監視應用程式。 要求快照集時，會在大約 10-20 毫秒內製作執行程序的陰影副本。 接著會將陰影程序進行分析，且在主要程序繼續執行並將流量提供給使用者時，會建立快照集。 接著，會將快照集與檢視快照集所需的任何相關符號 (.pdb) 檔案一起上傳至 Application Insights。

## <a name="current-limitations"></a>目前限制

### <a name="publishing-symbols"></a>發佈符號
快照集偵錯工具需要符號檔出現在生產環境伺服器上，才可將變數解碼並提供 Visual Studio 中的偵錯體驗。 15.2 版 Visual Studio 2017 發佈至 Azure App Service 時，預設會發佈版本組建的符號。 在舊版中，您必須將下列這一行新增至發佈設定檔 `.pubxml` 檔案，才會在發行模式中將符號發佈。

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

針對 Azure Compute 和其他類型，請確定符號檔案與主要應用程式 .dll 位於相同資料夾 (通常為 `wwwroot/bin`)，或可在目前的路徑使用。

### <a name="optimized-builds"></a>最佳化的組建
在某些情況下，由於建置程序期間所套用的最佳化，使版本組建無法檢視本機變數。 未來版本的 NuGet 套件中會固定這項限制。

## <a name="next-steps"></a>後續步驟

* [診斷 Web Apps 中的例外狀況](app-insights-asp-net-exceptions.md)說明如何讓 Application Insights 看見更多的例外狀況。 
* [智慧型偵測](app-insights-proactive-diagnostics.md)會自動探索效能異常。

