<properties
	pageTitle="監視 Azure Web 應用程式效能 |Microsoft Azure"
	description="圖表載入和回應時間、相依性資訊以及設定效能警示。"
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="awills"/>

# 監視 Azure Web 應用程式效能

在 [Azure 入口網站](https://portal.azure.com) 中，您可以設定監視功能，使其收集 [Azure Web 應用程式](../app-service-web/app-service-web-overview.md)或[虛擬機器](../virtual-machines/virtual-machines-linux-about.md)中應用程式相依性的統計資料和詳細資料。

Azure 運用擴充功能支援應用程式效能監視功能 (APM)。這些擴充功能已安裝在應用程式中，可收集資料並回報給監視服務。

**Application Insights** 和 **New Relic** 是兩個可用的效能監視延伸模組。若要使用這些模組，您需要在執行階段安裝代理程式。藉由 Application Insights，還有可用 SDK 建置程式碼的選項。SDK 可讓您撰寫程式碼來監視應用程式的詳細使用狀況和效能。

## Application Insights

### (選擇性) 使用 SDK 重建應用程式...

Application Insights 可以提供更詳細的遙測，方法是將 SDK 安裝至您的 App。

在 Visual Studio (2013 Update 2 或更新版本) 中，將 Application Insights SDK 加入專案。

![以滑鼠右鍵按一下 Web 專案，然後選擇 [加入 Application Insights]。](./media/insights-perf-analytics/03-add.png)

系統要求您登入時，請使用 Azure 帳戶的認證。

此作業有兩種效果︰

1. 在 Azure 中建立 Application Insights 資源，這是存放、分析和顯示遙測資料的位置。
2. 將 Application Insights NuGet 封裝加入至您的程式碼，並加以設定以將遙測傳送至 Azure 資源。

您可以在開發電腦 (F5) 中執行應用程式來測試遙測，或是直接繼續執行並重新發佈應用程式。

SDK 會提供 API 供您[撰寫自訂遙測](../application-insights/app-insights-api-custom-events-metrics.md)以追蹤使用情況。

### ...或手動設定資源

如果您未在 Visual Studio 中新增 SDK，則必須在 Azure 中設定 Application Insights 資源，這是存放、分析和顯示遙測資料的位置。

![按一下 [加入]、[開發人員服務]、[Application Insights]。選擇 ASP.NET 應用程式類型。](./media/insights-perf-analytics/01-new.png)


## 啟用擴充功能

1. 瀏覽至您要檢測的 Web 應用程式或虛擬機器的控制刀鋒視窗。

2. 加入 Application Insights 或 New Relic 延伸模組。

    如果您要檢測 Web 應用程式：

![設定、延伸模組、加入、Application Insights](./media/insights-perf-analytics/05-extend.png)

如果您使用的是虛擬機器：

![按一下 [分析] 磚。](./media/insights-perf-analytics/10-vm1.png)



## 探索資料

1. 開啟 Application Insights 資源 (無論是直接從 [瀏覽]，或從 Web 應用程式的 [效能監視] 工具)。

2. 逐一點選任何圖表以取得更詳細的資料：

    ![按一下 Application Insights 概觀刀鋒視窗上的圖表](./media/insights-perf-analytics/07-dependency.png)

    您可以[自訂計量刀鋒視窗](../application-insights/app-insights-metrics-explorer.md)。

3. 進一步逐一點選以查看個別事件與其屬性︰

    ![按一下事件類型以開啟依該類型篩選的搜尋](./media/insights-perf-analytics/08-requests.png)

    請注意 [...] 連結以開啟所有的屬性。

    您可以[自訂搜尋](../application-insights/app-insights-diagnostic-search.md)。

透過您的遙測功能更強大的搜尋，請使用 [Analytics 查詢語言](../application-insights/app-insights-analytics-tour.md)。


## 問答集

如何變更為將資料傳送至不同的 Application Insights 資源？

* 如果您已在 Visual Studio 中將 Application Insights 加入至程式碼：在專案上按一下滑鼠右鍵，選擇 [Application Insights] > [設定] 並選擇您想要的資源。您可取得建立新資源的選項。重建並重新部署。
* 否則︰在 Azure 中，開啟 Web 應用程式控制刀鋒視窗，並開啟 [工具] > [延伸模組]。刪除 Application Insights 延伸模組。然後開啟 [工具] > [效能]、按這裡、選擇 Application Insights，然後選擇您想要的資源。(如果您想要建立新的 Application Insights 資源，請先行建立)。


## 後續步驟

* [監視服務健康狀態計量](insights-how-to-customize-monitoring.md)可確保您的服務可用且回應正常。
* [啟用監視和診斷](insights-how-to-use-diagnostics.md)來在您服務中收集詳細的高頻率計量。
* 每當發生操作事件或計量超過臨界值時，[接收警示通知](insights-receive-alert-notifications.md)。
* 使用 [JavaScript 應用程式和網頁適用的 Application Insights](../application-insights/app-insights-web-track-usage.md)，以從造訪網頁的瀏覽器取得用戶端遙測。
* [設定可用性 Web 測試](../application-insights/app-insights-monitor-web-app-availability.md)，以在您的網站關閉時發出警示。

<!---HONumber=AcomDC_0803_2016-->