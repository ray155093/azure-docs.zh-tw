---
title: "診斷並解決問題 | Microsoft Docs"
description: "本教學課程說明如何在 Time Series Insights 環境中診斷並解決問題"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 46d58e94f3fede3f7f19de8d97e23ac6910bc12a
ms.contentlocale: zh-tw
ms.lasthandoff: 05/01/2017

---
# <a name="diagnose-and-solve-problems"></a>診斷並解決問題

## <a name="i-do-not-see-my-data"></a>我看不見我的資料
以下是您無法在 [Time Series Insights 入口網站](https://insights.timeseries.azure.com)環境中看到資料的可能原因。

### <a name="does-your-event-source-have-data-in-json-format"></a>您的事件來源是 JSON 格式的資料嗎？
Azure Time Series Insights 現在只支援 JSON 資料。 如需 JSON 範例，請在[這裡](time-series-insights-send-events.md#supported-json-shapes)參閱支援的 JSON 圖形一節。

### <a name="when-registering-your-event-source-did-you-provide-the-key-with-required-permissions"></a>註冊事件來源時，您提供的機碼有所需權限嗎？
1. 針對 IoTHub，您必須提供有「服務連線」權限的機碼。

   ![IotHub 的服務連線權限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   如先前影像所示，iothubowner 或 service 原則都會運作，因為兩者都有「服務連線」權限。
2. 針對事件中樞，您需要提供具有「接聽」權限的機碼。

   ![事件中樞接聽權限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   如先前影像所示，read 或 manage 原則都會運作，因為兩者都有「讀取」權限。

### <a name="are-you-sure-that-the-consumer-group-provided-is-exclusive-to-time-series-insights"></a>您確定提供的取用者群組是 Time Series Insights 專用嗎？
針對 IoTHub 或事件中樞，註冊期間我們會要求您指定讀取資料時應使用的取用者群組。 不可共用此取用者群組。 如果共用，基礎事件中樞會自動且隨機地與其中一個讀取器中斷連線。

## <a name="i-see-my-data-but-there-is-a-lag"></a>我可以看到我的資料，但有延遲情形
以下是您在 [Time Series Insights 入口網站](https://insights.timeseries.azure.com)環境中看到部分資料的可能原因。

### <a name="your-environment-might-be-getting-throttled"></a>您的環境可能正在進行節流
節流限制會根據環境 SKU 種類和容量來強制執行。 在環境內的所有事件來源都會共用這個容量。 如果事件中樞 / IoT 中樞事件來源正在推播超過強制限制的資料，您會看到節流和延遲情形。

下圖顯示具有 SKU S1 和容量 3 的 Time Series Insights 環境。 該環境可以每日輸入 3 百萬個事件。

![環境 SKU 目前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

假設此環境已使用下圖所示的嵌入速率從事件中心嵌入訊息：

![環境 SKU 目前容量](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

如圖所示，每日輸入速率為 ~67,000 則訊息。 此速率可轉譯為大約每分鐘 46 則訊息。 如果每個事件中樞訊息被壓平合併為單一 Time Series Insights 事件，則此環境看不見節流。 如果每個事件中樞訊息被壓平合併為 100 個 Time Series Insights 事件，則每分鐘應內嵌 4,600 個事件。 容量為 3 的 S1 SKU 環境每分鐘只可內嵌 2,100 個事件。 (一天 1 百萬個事件 => 一分鐘 700 個事件，3 個單位 => 一分鐘 2100 個事件)。 因此您會看見延遲情形是因為節流。 若要更深入地了解壓平合併的邏輯運作，請參閱[這裡](time-series-insights-send-events.md#supported-json-shapes)的*支援的 JSON 圖形*。

#### <a name="recommended-steps"></a>建議的步驟
若要修正延遲情形，請增加您環境的 SKU 容量。 [如何調整您的 Time Series Insights 環境規模](time-series-insights-how-to-scale-your-environment.md)

### <a name="you-might-be-pushing-historical-data-and-hence-the-slow-ingress"></a>您可能正在推播歷程記錄資料，且輸入因此而緩慢
如果您正與現有事件來源連線，則您的事件中樞 / IoT 中樞裡可能已有資料。 因此環境會從事件來源訊息保留期相當前端的位置開始提取資料。 此行為是預設行為且無法覆寫。 可能會進行節流，並且可能需要一段時間才能趕上正在內嵌的歷程紀錄資料。

#### <a name="recommended-steps"></a>建議的步驟
若要修正延遲情形，請執行下列步驟：
1. 將 SKU 容量增加到最大允許值 (在此案例中是 10)。 一旦增加容量，輸入程序會開始加快速度來趕上。 您可以從 [Time Series Insights 入口網站](https://insights.timeseries.azure.com)的可用性圖表中看到我們趕上的速度有多快。 增加容量需支付費用。
2. 一旦趕上延遲時間，即可將 SKU 容量降回到正常的輸入速率。

## <a name="my-event-source-timestamp-property-name-setting-does-not-work"></a>我的事件來源*時間戳記屬性名稱*設定沒有作用
請確定名稱和值符合下列規則︰
1. 時間戳記屬性名稱有__區分大小寫__。
2. 來自事件來源的時間戳記屬性值 (JSON 字串) 格式應是 __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__。 字串的範例如 “2008-04-12T12:53Z”。
