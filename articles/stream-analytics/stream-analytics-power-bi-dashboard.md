---
title: "Azure 串流分析上的 Power BI 儀表板 | Microsoft Docs"
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
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 278f73d9ee3f64727b34b3674cd38eaf5301e675
ms.lasthandoff: 05/01/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>串流分析及 Power BI：適用於串流資料的即時分析儀表板
Azure 串流分析可讓您使用其中一種頂尖的商業智慧工具 Microsoft Power BI。 了解如何使用 Azure 串流分析來分析大量串流資料，以及取得即時 Power BI 分析儀表板中的深度資訊。

使用 [Microsoft Power BI](https://powerbi.com/) 來快速建置即時儀表板。 [觀看說明此案例的影片](https://www.youtube.com/watch?v=SGUpT-a99MA)。

在本文中，您將了解如何使用 Power BI 做為 Azure 串流分析工作的輸出，來建立您自己的自訂商業智慧型工具。 您也將了解如何利用即時儀表板。

## <a name="prerequisites"></a>必要條件
* Microsoft Azure 帳戶。
* Power BI 的公司帳戶或學校帳戶。
* 完成[即時詐騙偵測](stream-analytics-real-time-fraud-detection.md)案例。 您目前正在閱讀的這篇文章是以[即時詐騙偵測](stream-analytics-real-time-fraud-detection.md)中所述的工作流程為基礎，並且會新增 Power BI 串流資料集輸出。

## <a name="add-power-bi-output"></a>新增 Power BI 輸出
該作業具有輸入之後，就可以定義對 Power BI 的輸出。

1. 選取位於作業儀表板中央的 [輸出] 方塊。 然後選取 [+ 新增] 來建立輸出。

    ![新增輸出](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. 提供 [輸出別名]。 您可以使用任何容易記住的輸出別名。 如果您決定讓作業擁有多個輸出，使用輸出別名會有幫助。 在此案例中，您會在查詢中參考此輸出。 例如，讓我們使用 "StreamAnalyticsRealTimeFraudPBI" 當作輸出別名的值。

3. 選取 [授權]。

    ![新增授權](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. 隨即會開啟視窗供您提供 Azure 認證 (工作帳戶或學校帳戶的認證)。 它也可讓您的 Azure 作業存取 Power BI 區域。

    ![授權欄位](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. 提供必要資訊之後，授權就會消失。 [新增輸出] 區域中有 [資料集名稱] 和 [資料表名稱] 欄位。

    ![PBI 工作區](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. 將它們定義如下：
    * **群組工作區**：在 Power BI 租用戶中選取要用來建立資料集的工作區。
    * **資料集名稱**：提供一個 Power BI 輸出應該要有的資料集名稱。 例如，讓我們使用 "StreamAnalyticsRealTimeFraudPBI"。
    * **資料表名稱**：提供 Power BI 輸出資料集的資料表名稱。 例如，讓我們使用 "StreamAnalyticsRealTimeFraudPBI"。 目前，串流分析工作的 Power BI 輸出中，一個資料集只能有一個資料表。

7. 選取 [ **建立**]。 現在，您已完成輸出組態。

> [!WARNING]
> 如果 Power BI 所擁有之資料集和資料表的名稱與串流分析作業中的相同，現有資料會遭到覆寫。
> 此外，我們不建議您在 Power BI 帳戶中明確建立此資料集和資料表。 當您啟動串流分析作業，而該作業開始將輸出提取至 Power BI 時，系統會自動建立資料集和資料表。 如果您的作業查詢沒有傳回任何結果，系統不會建立資料集和資料表。
>
>

系統會使用下列設定來建立資料集：
* **defaultRetentionPolicy: BasicFIFO**：資料是 FIFO，最大資料列數 20 萬。
* **defaultMode: pushStreaming**：支援串流磚和傳統報表型視覺效果 (又稱推送)。

您目前無法建立具有其他旗標的資料集。

如需 Power BI 資料集的詳細資訊，請參閱 [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) 參考。


## <a name="write-query"></a>撰寫查詢
移至工作的 [查詢]  索引標籤。 撰寫查詢 (也就是 Power BI 中要有的輸出)。 例如，它可能類似下列可在電信業中攔截 SIM 詐騙的 SQL 查詢：


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>在 Power BI 中建立儀表板

1. 移至 [Powerbi.com](https://powerbi.com)，然後使用公司帳戶或學校帳戶登入。 如果串流分析作業查詢輸出了結果，您就會看到資料集已建立完成，如下圖所示：

    ![串流資料集](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. 選取 [新增圖格]，然後選取自訂串流資料。

    ![自訂串流資料集](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. 從清單中選取資料集。

    ![您的串流資料集](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. 建立視覺效果卡。 然後選取 [fraudulentcalls] 欄位。

    ![新增詐騙](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    我們已經完成詐騙計數器！

    ![詐騙計數器](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. 再次執行新增圖格的步驟。 但這次選取折線圖。 新增 [fraudulentcalls] 作為值，並新增 [windowend] 作為軸。 我們選取了過去 10 分鐘，如下列螢幕擷取畫面所示︰

![詐騙電話](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


本教學課程示範如何為一個資料集只建立一種圖表。 Power BI 可協助您為組織建立其他客戶商業智慧型工具。 如需其他 Power BI 儀表板範例，請觀賞 [開始使用 Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) 視訊。

如需有關設定 Power BI 輸出及利用 Power BI 群組的詳細資訊，請檢閱[了解串流分析輸出](stream-analytics-define-outputs.md "了解串流分析輸出")的 [Power BI 小節](stream-analytics-define-outputs.md#power-bi)。 [Power BI 中的儀表板](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)是另一項實用資源。

## <a name="learn-about-limitations-and-best-practices"></a>了解限制和最佳作法
Power BI 同時採用了並行處理和輸送量條件約束，如[本頁面上有關 Power BI](https://powerbi.microsoft.com/pricing "Power BI 價格") 的說明。

目前來說，系統大約每秒可呼叫 Power BI 一次。 串流視覺效果支援 15 KB 的封包。 超過 15 KB，串流視覺效果就會失敗 (但仍會繼續推送)。

由於有這些限制，Power BI 最適用的自然是 Azure 串流分析會大量降低資料載入的案例。
我們建議使用輪轉時間範圍或跳動時間範圍來確保資料推送最多為每秒推送 1 次，並且您的查詢會符合輸送量的要求範圍。

您可以使用下列方程式，以秒為單位計算要提供給視窗的值：

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

例如：
- 您有 1 千個以一秒間隔傳送資料的裝置。
- 您使用的 Power BI Pro SKU 支援每小時 100 萬個資料列。
- 您想要讓每個裝置將平均資料量發佈至 Power BI。

因此，方程式會變成︰

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

這表示我們可以將原始查詢變更為下面這樣︰

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
如果您在建立作業之後或上次驗證過後變更了密碼，則需要重新驗證您的 Power BI 帳戶。 如果您在 Azure Active Directory (Azure AD) 租用戶上設定 Azure Multi-Factor Authentication，則也需要每 2 週更新一次 Power BI 授權。 如果您未更新，就會在作業記錄中看到缺乏作業輸出或「驗證使用者錯誤」之類的徵兆。

同樣地，如果作業在權杖過期後嘗試啟動，就會發生錯誤，而且作業啟動會失敗。 若要解決這個問題，請停止執行作業並移至 Power BI 輸出。 若要避免資料遺失，請選取 [更新授權] 連結，然後從 [上次停止時間] 重新啟動作業。

Power BI 在重新整理過授權後，授權區域就會出現綠色警示，表示問題已獲得解決。

## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

