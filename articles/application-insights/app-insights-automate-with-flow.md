---
title: "使用 Microsoft Flow 自動化 Azure Application Insights 流程"
description: "了解如何利用適用於 Flow 的 Application Insights Connector，使用 Microsoft Flow 來快速自動化可重複的流程。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 7e17fde55097a8e852be33bac66940a05fdd9df6
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-the-connector-for-microsoft-flow"></a>使用適用於 Microsoft Flow 的連接器自動化 Application Insights 流程

您是否發現自己在遙測資料上不斷重複地執行相同查詢，以檢查服務是否正常運作？ 想要將此類查詢自動化以尋找趨勢和異常，並針對它們建立您自己的工作流程嗎？ 那麼，適用於 Microsoft Flow 的 Application Insights Connector (預覽) 正是您需要的！
有了此整合，就能立即自動化許多流程而不需要撰寫任何一行程式碼。 在您使用 Application Insights 動作建立流程後，它會自動執行 Application Insights Analytics 查詢。 您也可以新增額外的動作。 Flow 提供數百個動作。 例如，您可以使用 Flow 來自動傳送電子郵件通知，或是在 Visual Studio Team Services 中建立錯誤 (Bug)。 您也可以使用提供給適用於 Microsoft Flow 之連接器的任何一種[範本](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights)。 這些範本可加快建立流程的程序。 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="tutorial-for-creating-a-flow-for-application-insights"></a>建立 Application Insights 流程的教學課程

在此教學課程中，您將學習如何建立流程，該流程會使用 Analytics 自動叢集演算法將 Web 應用程式資料中的屬性分組。 流程會自動將結果以電子郵件傳送。 這只是如何將 Microsoft Flow 和 Application Insights Analytics 一起使用的其中一個範例。 

### <a name="step-1-create-a-flow"></a>步驟 1：建立流程
1. 登入：http://flow.microsoft.com 然後選取 [我的流程]。
2. 按一下 [從空白建立流程]。

### <a name="step-2-create-a-trigger-for-your-flow"></a>步驟 2：建立流程的觸發程序
1. 依序選擇 [排程]、[排程 - 重複]。
2. 將 [頻率] 設定為 [天]，將 [間隔] 設定為 1。

![[流程觸發程序] 對話方塊](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-azure-application-insights-action"></a>步驟 3：新增 Azure Application Insights 動作
1. 按一下 [新增步驟]，然後按一下 [新增動作]。
2. 搜尋 Azure Application Insights。
3. 按一下 [Azure Application Insights – 視覺化 Analytics 查詢預覽]。

![執行 Analytics 查詢畫面](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>步驟 4：連線到 Application Insights 資源

**先決條件**

您需要資源的應用程式識別碼和 API 金鑰才能完成此步驟。 您可以從 Azure 入口網站擷取這些資訊，如下圖所示：

![Azure 入口網站中的應用程式識別碼](./media/app-insights-automate-with-flow/appid.png) 

- 為您的連線提供名稱以及應用程式識別碼和 API 金鑰。

![Flow 連線畫面](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>步驟 5：指定 Analytics 查詢和圖表類型
此範例會選取最後一天內的失敗要求，並將它們與作業發生的例外狀況相互關聯。 Analytics 會根據 operation_Id 識別碼來相互關聯。 查詢接著會使用自動叢集演算法將結果分段。 建立您自己的查詢時，務必確認它們可在 Analytics 中正常運作，然後再將其新增到流程中。

- 加入下列 Analytics 查詢，然後選取 HTML 表格圖表類型。 

```
requests
| where timestamp > ago(1d)
| where success == "False"
| project name, operation_Id
| join ( exceptions
    | project problemId, outerMessage, operation_Id
) on operation_Id
| evaluate autocluster()
```
![Analytics 查詢設定畫面](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>步驟 6：設定傳送電子郵件的流程

1. 按一下 [新增步驟]，然後選取 [新增動作]。
2. 搜尋 Office 365 Outlook。
3. 按一下 [Office 365 Outlook – 傳送電子郵件]。
![Office 365 Outlook 選項](./media/app-insights-automate-with-flow/flow2b.png)

4. 在電子郵件動作中加入下列資料：
 - 指定收件者的電子郵件地址
 - 提供電子郵件的主旨
 - 將游標放置於 [內文] 欄位，然後從右方開啟的動態內容功能表中，選取 [內文]。
 - 按一下 [顯示進階選項]。

 ![Office 365 Outlook 設定](./media/app-insights-automate-with-flow/flow5.png)

5. 從動態內容功能表執行下列動作：
- 選取 [附件名稱]
- 選取 [附件內容]
- 在 [為 HTML] 欄位中選取 [是]

![Office 365 電子郵件設定畫面](./media/app-insights-automate-with-flow/flow7.png)
### <a name="step-7-save-and-test-your-flow"></a>步驟 7：儲存並測試流程
- 新增您的流程名稱，然後按一下 [建立流程]。

![Microsoft Flow 建立畫面](./media/app-insights-automate-with-flow/flow8.png)

您可以等候觸發程序執行此動作，或[視需要執行觸發程序](https://flow.microsoft.com/blog/run-now-and-six-more-services/)來立即執行流程。

流程執行時，您在電子郵件清單中所指定的收件者會收到看起來如下的電子郵件：

![範例電子郵件](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>後續步驟

- 深入了解建立 [Analytics 查詢](app-insights-analytics-using.md)。
- 深入了解 [Microsoft Flow](https://ms.flow.microsoft.com)。



<!--Link references-->






