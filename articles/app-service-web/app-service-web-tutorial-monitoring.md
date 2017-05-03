---
title: "監視 Web 應用程式 | Microsoft Docs"
description: "了解如何在 Web 應用程式上設定監視"
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 8d9b4a4fa3b62659fc7e2aa1c6329fdc5e01fe39
ms.lasthandoff: 04/21/2017

---
# <a name="monitor-app-service"></a>監視 App Service
本教學課程將逐步引導您監視您的應用程式，並使用內建的平台工具來解決所發生的問題。

這份文件的每個章節都會復習特定的功能。 將功能一起使用可讓您︰
- 識別應用程式中的問題。
- 當您的程式碼或平台引起問題時進行判斷。
- 縮小程式碼中的問題來源。
- 偵錯和修正問題。

## <a name="before-you-begin"></a>開始之前
- 您必須有要監視的 Web 應用程式，並遵循概略的步驟。 
    - 您可以遵循[在 Azure 中搭配 SQL Database 建立 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)教學課程中所述的步驟來建立應用程式。

- 如果您想要試著進行應用程式的**遠端偵錯**，則您需要 Visual Studio。 
    - 如果尚未安裝 Visual Studio 2017，您可以下載並使用免費的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 
    - 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

## <a name="metrics"></a> 步驟 1 - 檢視計量
**計量**有助於您了解︰ 
- 應用程式健全狀況
- 應用程式效能
- 資源耗用量

調查應用程式問題時，檢閱計量是不錯的起點。 Azure 入口網站有一種快速方式，可使用 **Azure 監視器**，以視覺化方式檢查應用程式的計量。

計量會提供應用程式之數個金鑰彙總的歷程記錄檢視。 您應該針對 App Service 中裝載的任何應用程式，監視 Web 應用程式和 App Service 方案。

> [!NOTE]
> App Service 方案代表用來裝載應用程式的實體資源集合。 所有指派給 App Service 方案的所有應用程式都會共用它所定義的資源，而讓您節省裝載多個應用程式時的成本。
>
> App Service 方案可定義：
> * 區域：北歐、美國東部、東南亞等。
> * 執行個體大小：小型、中型、大型等。
> * 級別計數：一、二或三個執行個體等。
> * SKU：免費、共用、基本、標準、進階等。

若要檢視 Web 應用程式的計量，請移至您想要監視之應用程式的 [概觀] 刀鋒視窗。 從這裡開始，您能以**監視圖格**的形式檢視應用程式計量的圖表。 按一下圖格，以編輯和設定要檢視的計量和要顯示的時間範圍。 

根據預設，資源刀鋒視窗提供了應用程式要求的檢視和最後一個小時的錯誤。
![監視應用程式](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

如您在範例中所見，我們有一個應用程式產生了許多 **HTTP 伺服器錯誤**。 大量的錯誤是我們要調查此應用程式的第一個徵兆。

> [!TIP]
> 透過下列連結深入了解 Azure 監視器：
> - [開始使用 Azure 監視器](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Azure 計量](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [支援 Azure 監視器的計量](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Azure 儀表板](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a> 步驟 2 - 設定警示
**警示**可以設定成在您的應用程式發生特定條件時進行觸發。

在[步驟 1 - 檢視計量](#metrics)中，我們看到應用程式有大量的錯誤。 

讓我們將警示設定為發生錯誤時會自動取得通知。 在此情況下，我們希望每次 HTTP 50 X 錯誤數目超過某個臨界值時，警示會傳送電子郵件。

若要建立警示，請瀏覽至 [監視] > [警示]，然後按一下 [+ 新增警示]。

![Alerts](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

提供警示設定的值︰
- **資源︰**使用警示來監視的網站。 
- **名稱︰**您警示的名稱，在此案例中為︰高 HTTP 50X。
- **描述︰**這項警示所查看的純文字說明。
- **警示項目︰**警示可以查看計量或事件，此範例中，我們要查看計量。
- **計量︰**要監視的計量，在此情況下︰HTTP 伺服器錯誤。
- **條件︰**發出警示的時機，在此情況下，選取大於選項。
- **臨界值︰**所要尋找的值，在此情況下︰400。
- **期間︰**警示在計量的平均值期間運作。 較短的時間會造成警示較敏感。 在此情況下，我們看看 5 分鐘。 
- **電子郵件擁有者和參與者︰**在此情況下︰已啟用。

現在已建立警示，每次應用程式超過設定的閾值時，就會傳送電子郵件。 也可以在 Azure 入口網站中檢閱作用中警示。

![觸發的警示](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts-triggered.png)


> [!TIP]
> 透過下列連結深入了解 Azure 警示：
> - [Microsoft Azure 中的警示是什麼](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [對計量採取動作](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [建立計量警示](..\monitoring-and-diagnostics\insights-alerts-portal.md)

## <a name="companion"></a> 步驟 3 - App Service Companion
**App Service Companion** 提供一種便利的方式，利用行動裝置 (iOS 或 Android) 中的原生體驗來監視您的應用程式。

使用 Azure App Service Companion 來執行下列動作：
- 檢閱應用程式計量
- 檢閱應用程式警示和建議並採取動作
- 執行基本疑難排解 (瀏覽、啟動、停止、重新啟動應用程式)
- 取得重大事件的推播通知。

![App Service Companion](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![App Service Companion App Store](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![App Service Companion Google Play](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

您可以從 [App Store](https://itunes.apple.com/app/azure-app-service-companion/id1146659260) 或 [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps) 安裝 App Service Companion

## <a name="diagnose"></a> 步驟 4 - 診斷並解決問題
**診斷並解決問題**可協助您區隔出應用程式問題與平台問題。 它也可以建議可能的補救措施，讓您的 Web 應用程式回到良好的健全狀況。
 
![診斷並解決問題](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

繼續上一個步驟的範例，我們可以看到應用程式不斷發生可用性問題。 相反地，平台可用性並未低於 100%。

當應用程式發生問題而平台持續運作時，我們很清楚知道，需要處理的是應用程式問題。

## <a name="logging"></a> 步驟 5 - 記錄
現在我們已將失敗範圍縮小到應用程式問題，我們可以查看應用程式和伺服器記錄來取得詳細資訊。

記錄功能可讓您同時收集 Web 應用程式的**應用程式診斷**和 **Web 伺服器診斷**記錄。

### <a name="application-diagnostics"></a>應用程式診斷
應用程式診斷功能可讓您擷取應用程式在執行階段所產生的追蹤。 

將追蹤新增至您的應用程式，可大幅提升您的偵錯和固定點問題能力。

在 ASP.NET 中，您可以使用 [System.Diagnostics.Trace 類別](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx)來記錄應用程式追蹤，以產生由記錄基礎結構所擷取的事件。 您也可以指定追蹤的嚴重性，讓篩選變得更簡單。

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```
若要啟用應用程式記錄功能，請移至 [監視] > [診斷記錄]，並使用切換來啟用應用程式記錄。

![監視應用程式](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

應用程式記錄可以儲存至 Web 應用程式的檔案系統或推送至 Blob 儲存體。 如果是生產案例，建議使用 Blob 儲存體。

> [!IMPORTANT]
> 啟用記錄會影響您的應用程式效能與資源使用率。 如果是生產案例，建議使用錯誤記錄檔。 只有在調查問題時，才能啟用更詳細資料記錄。

 ### <a name="web-server-diagnostics"></a>Web 伺服器診斷
即使未檢測您的應用程式，仍會產生 Web 伺服器記錄。 App Service 可以收集三種不同類型的伺服器記錄：

- **Web 伺服器記錄** 
    - 使用 [W3C 擴充記錄檔格式 (英文)](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx) 的 HTTP 交易相關資訊。 
    - 當您需要判斷整體網站計量 (例如，處理的要求數量，或者有多少要求來自特定的 IP 位址) 時，非常實用。
- **詳細錯誤記錄** 
    - 對於表示失敗的 HTTP 狀態碼 (狀態碼 400 或更大) 的詳細錯誤資訊。 
    - [深入了解詳細的錯誤記錄](https://www.iis.net/learn/troubleshoot/diagnosing-http-errors/how-to-use-http-detailed-errors-in-iis)
- **失敗要求的追蹤** 
    - 關於失敗要求的詳細資訊，包括用於處理要求的 IIS 元件追蹤，以及每個元件所花費的時間。 
    - 在嘗試隔離導致特定 HTTP 錯誤的項目時，失敗的要求記錄就非常實用。
    - [深入了解失敗的要求追蹤](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis)

啟用伺服器記錄：
- 移至 [監視] > [診斷記錄]。 
- 使用切換來啟用不同類型的 Web 伺服器診斷。

![監視應用程式](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> 啟用記錄會影響您的應用程式效能與資源使用率。 如果是生產案例，建議使用錯誤記錄檔，只有在調查問題時，才能啟用更詳細資訊記錄。

### <a name="accessing-logs"></a>存取記錄
使用 Azure 儲存體總管來存取 Blob 儲存體中所儲存的記錄。 在下列路徑中，透過 FTP 存取儲存於 Web 應用程式檔案系統中的記錄：

- **應用程式記錄** - `%HOME%/LogFiles/Application/`。
    - 此資料夾內含有一或多個文字檔案，這些檔案涵蓋應用程式記錄所產生的資訊。
- **失敗的要求追蹤** - `%HOME%/LogFiles/W3SVC#########/`。 
    - 此資料夾內含有一個 XSL 檔案和一或多個 XML 檔案。 
- **詳細的錯誤記錄** - `%HOME%/LogFiles/DetailedErrors/`。 
    - 此資料夾包含一或多個 .htm 檔案，以及關於您應用程式所產生之 HTTP 錯誤的詳細資訊。
- **Web 伺服器記錄** - `%HOME%/LogFiles/http/RawLogs`。 
    - 此資料夾包含一或多個運用 W3C 擴充記錄檔格式來格式化的文字檔。

## <a name="streaming"></a> 步驟 6 - 記錄資料流
資料流記錄在偵錯應用程式時非常便利，因為相較於透過 FTP 來[存取記錄](#Accessing-Logs)，這種方法比較節省時間。

App Service 可以在產生**應用程式記錄**和 **Web 伺服器記錄**時對它們進行資料流處理。 

> [!TIP]
> 在嘗試資料流處理記錄時，請確定您已啟用收集記錄，如[記錄](#logging)一節中所述。

若要串流記錄，移至 [監視]> [記錄資料流]。 根據您要尋找的是何種資訊來選取 [應用程式記錄] 或 [Web 伺服器記錄]。 從這裡開始，您也可以暫停、重新啟動，然後清除緩衝區。

![串流記錄](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> 只有應用程式上有流量時，才會產生記錄，您也可以增加記錄的詳細資訊，以取得更多的事件或資訊。

## <a name="remote"></a> 步驟 7 - 遠端偵錯
一旦您確定應用程式問題的來源後，請使用**遠端偵錯**來逐步執行程式碼。

遠端偵錯可讓您將偵錯工具附加至在雲端中執行的 Web 應用程式。 您可以設定中斷點、直接操作記憶體、逐步執行程式碼，甚至變更程式碼路徑，就像您針對在本機執行的應用程式所做的。

將偵錯工具附加至您在雲端中執行的應用程式：

- 使用 Visual Studio 2017，開啟您要偵錯之應用程式的方案 
- 設定一些中斷點，就像您針對本機開發所做的。
- 開啟 [Cloud Explorer](ctr + /，ctrl + x)。
- 視需要利用您的 Azure 認證登入。
- 尋找您要偵錯的應用程式
- 從 [動作] 窗格選取 [附加偵錯工具]。

![遠端偵錯](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

Visual Studio 會設定您的應用程式以進行遠端偵錯，並啟動瀏覽器視窗，瀏覽至您的應用程式。 完整瀏覽您的應用程式，以觸發中斷點並逐步執行程式碼。

> [!WARNING]
> 不建議在生產環境中執行偵錯模式。 如果您的生產應用程式並未調升規模到多個伺服器執行個體，偵錯就會防止 Web 伺服器回應其他要求。 如需進行生產環境問題的疑難排解，您的最佳資源是[設定記錄](#logging)和 [Application Insights](#insights)。



## <a name="explorer"></a> 步驟 8 - 處理序總管
當您的應用程式相應放大至多個執行個體時，**處理序總管**可協助您識別執行個體特定的問題。

使用**處理序總管**來執行下列動作：

- 列舉 App Service 方案的不同執行個體上的所有處理序。
- 向下切入並檢視與每個處理序相關聯的控制代碼和模組。 
- 檢視處理序層級上的 CPU、工作集和執行緒計數，以協助您識別失控的處理序
- 尋找開啟檔案控制代碼，甚至終止特定的處理序執行個體。

可在 [監視] > [處理序總管] 下方找到處理序總管。

![處理序總管](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)


## <a name="insights"></a> 步驟 9 - Application Insights
**Application Insights** 可為您的應用程式提供應用程式分析和進階監視功能。 

請使用 Application Insights 來偵測並診斷例外狀況，以及 Web 應用程式中的效能問題。

您可以在 [監視] > [Application Insights] 下方，針對 Web 應用程式啟用 Application Insights 

> [!NOTE]
> Application Insights 可能會提示您安裝 Application Insights 網站延伸模組，以開始收集資料。 安裝網站延伸模組會導致應用程式重新啟動。

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

Application Insights 具有豐富的功能集，若要深入了解，請遵循[後續步驟](#next)一節中所含的連結。

## <a name="next"></a> 後續步驟

 - [什麼是 Application Insights](..\application-insights\app-insights-overview.md)
 - [使用 Application Insights 監視 Azure Web 應用程式效能](..\application-insights\app-insights-azure-web-apps.md)
 - [使用 Application Insights 監視任何網站的可用性和回應性](..\application-insights\app-insights-monitor-web-app-availability.md)
