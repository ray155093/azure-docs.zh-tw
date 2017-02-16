---
title: "串流分析上的 Power BI 儀表板 | Microsoft Docs"
description: "使用即時串流 Power BI 儀表板來收集商業智慧及分析來自串流分析工作的大量資料。"
keywords: "分析儀表板, 即時儀表板"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>串流分析及 Power BI：適用於串流資料的即時分析儀表板
Azure 串流分析可讓您使用其中一種業界頂尖智慧型工具 Microsoft Power BI。 了解如何使用 Azure 串流分析來分析大量串流資料，以及取得即時 Power BI 分析儀表板中的深度資訊。

使用 [Microsoft Power BI](https://powerbi.com/) 來快速建置即時儀表板。 [觀賞案例說明影片](https://www.youtube.com/watch?v=SGUpT-a99MA)。

在這篇文章中，您將了解如何使用 Power BI 做為 Azure 串流分析工作的輸出，來建立您的自訂商業智慧型工具，以及如何利用即時儀表板。

## <a name="prerequisites"></a>必要條件
* Microsoft Azure 帳戶
* Power BI 的公司帳戶或學校帳戶
* 完成[即時詐欺偵測](stream-analytics-real-time-fraud-detection.md)入門案例。 本文以該工作流程為基礎，並新增 Power BI 串流資料集輸出。

## <a name="add-power-bi-output"></a>新增 Power BI 輸出
該作業具有輸入之後，就可以定義對 Power BI 的輸出。 選取位於作業儀表板 [輸出] 中央的方塊。 然後按一下 [+ 新增] 按鈕來建立輸出。

![新增輸出](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

提供 [輸出別名] – 您可以填入任何較容易記住的輸出別名。 如果您決定讓工作擁有多個輸出，填寫輸出別名會非常有幫助。 在這個情況下，您必須參考此查詢中的輸出。 例如，讓我們使用 "StreamAnalyticsRealTimeFraudPBI" 當作輸出別名的值。

然後按一下 [授權] 按鈕。

![新增授權](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

會出現視窗提示您輸入 Azure 認證 (公司或學校帳戶)，然後它會為 Azure 作業提供 Power BI 區域的存取權。

![授權欄位](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

符合認證之後授權視窗會消失，[新輸出] 區域會有 [資料集名稱] 和 [資料表名稱] 欄位。

![pbi 工作區](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

將它們定義如下：
* **群組工作區** – 在將用來建立資料集的 Power BI 租用戶中選取工作區。
* **資料集名稱** - 提供一個 Power BI 輸出應該要有的資料集名稱。 例如，讓我們使用 "StreamAnalyticsRealTimeFraudPBI"。
* **資料表名稱** - 提供 Power BI 輸出資料集的資料表名稱。 假設我們稱它為 "StreamAnalyticsRealTimeFraudPBI"。 目前，串流分析工作的 Power BI 輸出中，一個資料集只能有一個資料表。

按一下 [建立] 即完成輸出組態。

> [!WARNING]
> 請注意 Power BI 是否已經具有與您在串流分析工作中提供的名稱相同的資料集和資料表名稱；若是如此，可能會覆寫現有的資料。
> 此外，您不應在 Power BI 帳戶中明確建立此資料集和資料表。 當您啟動串流分析工作，工作開始將輸出提取至 Power BI 時，就會自動建立資料集和資料表。 如果您的工作查詢沒有傳回任何結果，將不會建立資料集和資料表。
> 
> 

資料集是使用下列設定集建立：
* defaultRetentionPolicy: BasicFIFO - 資料是 FIFO，最大資料列數 20 萬
* defaultMode: pushStreaming：支援串流磚和傳統報表型視覺效果 (又稱推送)
* 此階段不支援使用其他旗標建立資料集

如需 Power BI 資料集的詳細資訊，請參閱 [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) 參考。


## <a name="write-query"></a>撰寫查詢
移至工作的 [查詢]  索引標籤。 撰寫查詢，就是 Power BI 中要有的輸出。 例如，它可以是如下的 SQL 查詢，以在電信業中攔截 SIM 詐騙：


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>在 Power BI 中建立儀表板

移至 [Powerbi.com](https://powerbi.com) ，然後使用公司帳戶或學校帳戶登入。 如果串流分析工作查詢輸出任何結果，就會看到您的資料集已經建立完成：

![串流資料集](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

按一下 [新增] 磚並選取自訂的串流資料。

![自訂串流資料集](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

然後從清單中選取資料集：

![您的串流資料集](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

再建立視覺效果卡並選取 [fraudulentcalls] 欄位。

![新增詐騙](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

我們已經完成詐騙計數器！

![詐騙計數器](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

再次執行新增磚的步驟，但這次改成選取折線圖。 新增 fraudulentcalls 做為值，並新增軸做為 windowend。 下圖為選取過去 10 分鐘：

![詐騙電話](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


請注意，本教學課程示範如何為一個資料集建立一種圖表類型。 Power BI 可協助您為組織建立其他客戶商業智慧型工具。 如需其他 Power BI 儀表板範例，請觀賞 [開始使用 Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) 視訊。

如需有關設定 Power BI 輸出及利用 Power BI 群組的進一步資訊，請檢閱[了解串流分析輸出](stream-analytics-define-outputs.md "了解串流分析輸出")的 [Power BI 小節](stream-analytics-define-outputs.md#power-bi)。 另一個深入了解如何使用 Power BI 建立儀表板的實用資源，可參考 [Power BI 中的儀表板](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)。

## <a name="limitations-and-best-practices"></a>限制和最佳作法
Power BI 同時採用了並行處理和輸送量條件約束，如下所述： [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Power BI 價格")

目前來說，每秒可以呼叫 Power BI 約一次。 串流視覺效果支援的封包大小為 15kb。 若超出大小，串流視覺效果會失敗 (但推送會繼續運作)。

由於這些 Power BI 本身以最自然的方式符合案例需求，其中 Azure 串流分析會大量降低資料載入的作業。
我們建議使用 TumblingWindow 或 HoppingWindow 來確保資料推送最多為每秒推送 1 次，並且您的查詢會符合輸送量的要求範圍 – 您可以使用下列方程式，以秒為單位計算要提供給視窗的值：

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

舉例來說，如果您有 1,000 個每秒傳送資料的裝置，並且在支援每小時 1,000,000 個資料列的 Power BI Pro SKU 上，而您想要取得 Power BI 上每個裝置的平均資料，則您可以讓每一裝置最多每 4 秒推送一次 (如下所示)：  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

這表示我們會將原始查詢變更為：

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>更新授權
如果您在建立作業之後或上次驗證過後變更了密碼，則需要重新驗證您的 Power BI 帳戶。 如果您在 Azure Active Directory (AAD) 租用戶上設定 Multi-Factor Authentication (MFA)，則也需要每 2 週更新一次 Power BI 授權。 此問題發生時的徵兆就是沒有作業輸出，且作業記錄檔中出現「驗證使用者錯誤」。

同樣地，如果作業在權杖已過期時嘗試啟動，將會發生錯誤，而作業啟動將會失敗。 若要解決這個問題，請停止執行工作並移至 Power BI 輸出。  按一下 [更新授權] 連結，並從 [上次停止時間] 重新啟動您的工作以避免資料遺失。 透過 Power BI 重新整理授權後，您會看見授權區域中出現綠色警示，表示問題已解決。

## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


