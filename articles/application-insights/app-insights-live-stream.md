---
title: "Azure Application Insights 中的自訂即時計量和診斷 | Microsoft Docs"
description: "利用自訂計量和失敗、追蹤與事件之即時摘要等診斷問題，即時監視 web 應用程式。"
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 86e01cf6cb14334e85da4102610fa7feb66cb543
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="custom-live-metrics-and-events-monitor--diagnose-with-1-second-latency"></a>自訂即時計量和事件︰以 1 秒的延遲進行監視與診斷 
即時計量串流會以 1 秒接近即時的延遲來顯示您的 [Application Insights](app-insights-overview.md) 計量和事件。 此即時監視有助於減少偵測和診斷的平均時間，可協助您符合 SLA。 您可以：
* 即時監視自訂 KPI︰實驗將您現有的 Application Insights 遙測進行篩選、切割及細分，立即從入口網站取得最相關的 KPI。 不會變更任何程式碼或設定，且不需要部署。 任何您可能會傳送的自訂計量或測量也可供使用。
* 即時偵測及診斷：請參閱隨詳細例外狀況追蹤所發生的要求和相依性失敗。 將任何已知的問題篩選掉，以著重於真實/新增的問題。
* 即時偵錯︰將問題重現，並即時查看所有相關的遙測，利用自訂篩選到可識別重現互動的特定工作階段識別碼 (或利用任何自訂屬性)。 收集來自任何/所有伺服器的資訊，搶先開始修正問題。
* 立即查看資源消耗回應負載的方式︰當您執行負載測試時，即時監視任何 Windows 效能計數器，或在任何項目受到負面影響之前，監視要處理的生產環境。 無需變更任何設定或進行部署。
* 驗證要發行的修正。 請確定您服務的更新發生時順利進行。 請驗證您已修正的失敗不會再發生。
* 輕鬆識別有問題的伺服器，並將所有 KPI/即時摘要篩選到只有該伺服器。

即時計量與事件串流資料是免費的︰它不會新增至您的帳單。 當您開啟入口網站體驗時，資料會從您的伺服器隨需進行串流處理。 只要資料仍在圖表上就會保存，之後便會捨棄該資料。 完整功能適用於 ASP.NET 傳統應用程式，而 .NET Core 應用程式目前只有一組固定的即時計量和範例失敗。 我們會使所有支援的 SDK 達到最新即時串流功能的標準。 

我們在進行任何取樣之前，會從您的應用程式執行個體收集即時串流，或是套用任何自訂的 TelemetryProcessors。 

![即時計量串流影片](./media/app-insights-live-stream/youtube.png) [即時計量串流影片](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

按一下左側的選項或是 [概觀] 刀鋒視窗上的按鈕，可存取即時計量和事件︰

![在 [概觀] 刀鋒視窗中，按一下 [即時資料流]](./media/app-insights-live-stream/live-stream-2.png)

## <a name="custom-live-kpi"></a>自訂即時 KPI
您可以從入口網站將任何篩選條件套用在任何 Application Insights 遙測上，以便即時監視自訂 KPI。 按一下您將滑鼠移過任何圖表時所顯示的篩選條件控制項。 下列圖表是使用 URL 和 Duration 屬性的篩選條件來繪製自訂要求計數 KPI。 利用可顯示符合您在任一時間點所指定準則之遙測即時摘要的「串流預覽」區段來驗證您的篩選條件。 

![自訂要求 KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

您可以監視與計數不同的值。 選項取決於串流的類型，可能是任何的 Application Insights 遙測︰要求、相依性、例外狀況、追蹤、事件或計量。 它可以是您自己的[自訂測量](app-insights-api-custom-events-metrics.md#properties)：

![值選項](./media/app-insights-live-stream/live-stream-valueoptions.png)

除了 Application Insights 遙測之外，您也可以監視任何 Windows 效能計數器，方法是從串流選項中選取，並提供效能計數器的名稱。

即時計量會在兩個地方進行彙總︰在每一部伺服器上本機進行，以及在所有伺服器上進行。 您可以選取個別下拉式清單中的其他選項，在任一位置變更預設。

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>範例遙測︰自訂即時診斷事件
依預設，事件的即時摘要會顯示失敗要求和相依性呼叫、例外狀況、事件以及追蹤的範例。 按一下篩選圖示可查看任一時間點套用的準則。 

![預設的即時摘要](./media/app-insights-live-stream/live-stream-eventsdefault.png)

如同計量，您可以將任意準則指定為任何的 Application Insights 遙測類型。 在此範例中，我們要選取特定的要求失敗、追蹤及事件。 我們還會選取所有的例外狀況和相依性失敗。

![自訂即時摘要](./media/app-insights-live-stream/live-stream-events.png)

請注意︰目前針對以例外狀況訊息為基礎的準則，請使用最外部的例外狀況訊息。 在上述範例中，若要使用內部例外狀況訊息 (後接 "<--" 分隔符號)「用戶端中斷連線。」將良性的例外狀況篩選掉， 請使用不包含「讀取要求內容時發生錯誤」準則的訊息。

按一下即時摘要中的項目，以查看它的詳細資料。 您可以按一下 [暫停] 或只向下捲動，或是按一下項目來將摘要暫停。 在您捲動回到頂端後，或是按一下暫停時所收集到的項目計數器，即時摘要就會繼續進行。

![取樣的即時失敗](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>依伺服器執行個體篩選

如果您想要監視特定伺服器角色執行個體，可以依伺服器篩選。

![取樣的即時失敗](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK 需求
[Application Insights SDK for Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的 2.4.0-beta2 版或更新版本提供「自訂即時計量串流」。 請記得選取 NuGet 套件管理員的 [包括預先發行] 選項。

## <a name="authenticated-channel"></a>驗證的頻道
您指定的自訂篩選條件準則會傳回給 Application Insights SDK 中的即時計量元件。 篩選條件可能會包含機密資訊，例如 customerIDs。 除了檢測金鑰之外，您還可以利用祕密 API 金鑰來保護頻道安全。
### <a name="create-an-api-key"></a>建立 API 金鑰

![建立 API 金鑰](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>將 API 金鑰新增至設定
在 applicationinsights.config 檔案中，將 AuthenticationApiKey 新增至 QuickPulseTelemetryModule：
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
或在程式碼中，在 QuickPulseTelemetryModule 上設定它：

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

不過，如果您認得並信任所有連線的伺服器，則無需透驗證的頻道就可以嘗試自訂篩選器。 這個選項有六個月可供使用。 一旦每個新的工作階段或是新的伺服器上線後，就需要此覆寫。

![即時計量驗證選項](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>我們強烈建議您在篩選條件準則中輸入潛在的機密資訊 (例如 CustomerID) 之前，先設定驗證的頻道。
>

## <a name="troubleshooting"></a>疑難排解

沒有資料？ 如果您的應用程式是在受保護的網路中︰即時計量串流會使用與其他 Application Insights 遙測不同的 IP 位址。 請確定[這些 IP 位址](app-insights-ip-addresses.md)在您的防火牆中為開啟狀態。



## <a name="next-steps"></a>後續步驟
* [使用 Application Insights 監視使用情況](app-insights-web-track-usage.md)
* [使用診斷搜尋](app-insights-diagnostic-search.md)


