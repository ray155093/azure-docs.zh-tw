---
title: "使用 Azure 邏輯應用程式自動化 Azure Application Insights 程序。"
description: "了解如何透過將 Application Insights Connector 加入到 Azure 邏輯應用程式，快速自動化重複程序。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 571a76253fa62a89f325e0c295e9a7e98e201079
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-an-azure-logic-app"></a>使用 Azure 邏輯應用程式自動化 Application Insights 程序

您是否發現自己在遙測資料上不斷重複地執行相同查詢，以檢查服務是否正常運作？ 想要將此類查詢自動化以尋找趨勢和異常，並針對它們建立您自己的工作流程嗎？ 那麼，Azure Logic Apps 的 Application Insights Connector (預覽版) 就是你所需要的！
有了此整合，就能立即自動化許多流程而不需要撰寫任何一行程式碼。 您可以使用 Application Insights Connector 建立邏輯應用程式，以快速自動化任何 Application Insights 程序。 您也可以新增額外的動作。 Logic Apps 提供數百個動作。 例如，您可以自動傳送電子郵件通知，或在 Visual Studio Team Services 中建立 Bug，作為邏輯應用程式的一部分。 您也可以使用任何一種適用於 Logic Apps 的[範本](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates)。 這些範本會加快建立邏輯應用程式的程序。 

## <a name="tutorial-for-creating-an-azure-logic-app-for-application-insights"></a>建立適用於 Application Insights 的 Azure 邏輯應用程式教學課程

在本教學課程中，您會學習如何建立一個 Azure 邏輯應用程式，該程式會使用 Analytics 自動叢集演算法將 Web 應用程式資料中的屬性分類。 流程會自動將結果以電子郵件傳送。 這只是您可以如何一起使用 Application Insights Analytics 和 Azure Logic Apps 的一個範例。 

### <a name="step-1-create-a-logic-app"></a>步驟 1：建立邏輯應用程式
1. 登入 https://portal.azure.com。
2. 從 [新增/Web + 行動裝置] 功能表建立新的邏輯應用程式。

![新的邏輯應用程式畫面](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>步驟 2：建立邏輯應用程式的觸發程序
1.  在邏輯應用程式設計工具中，在 [開始] 底下的一般觸發程序，選擇 [循環]。
2.  將 [頻率] 設為 [天]，[間隔] 設為 1。

![邏輯應用程式觸發程序對話方塊](./media/automate-with-logic-apps/logicapp2.png)

![邏輯應用程式頻率對話方塊](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-azure-application-insights-action"></a>步驟 3：新增 Azure Application Insights 動作
1. 按一下 [新增步驟]，然後按一下 [新增動作]。
2. 搜尋 Azure Application Insights。
3. 按一下 [Azure Application Insights – 視覺化 Analytics 查詢預覽]。

![執行 Analytics 查詢畫面](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>步驟 4：連線到 Application Insights 資源

**必要條件**

您需要資源的應用程式識別碼和 API 金鑰才能完成此步驟。 您可以從 Azure 入口網站擷取這些資訊，如下圖所示：

![Azure 入口網站中的應用程式識別碼](./media/automate-with-logic-apps/appid.png) 

- 為您的連線提供名稱以及應用程式識別碼和 API 金鑰。

![Flow 連線畫面](./media/automate-with-logic-apps/flow3.png)

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
![Analytics 查詢設定畫面](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-app-to-send-email"></a>步驟 6：設定應用程式傳送電子郵件

1. 按一下 [新增步驟]，然後選取 [新增動作]。
2. 搜尋 Office 365 Outlook。
3. 按一下 [Office 365 Outlook – 傳送電子郵件]。
![Office 365 Outlook 選項](./media/automate-with-logic-apps/flow2b.png)

4. 在電子郵件動作中加入下列資料：
 - 指定收件者的電子郵件地址
 - 提供電子郵件的主旨
 - 將游標放置於 [內文] 欄位，然後從右方開啟的動態內容功能表中，選取 [內文]。
 - 按一下 [顯示進階選項]。

 ![Office 365 Outlook 設定](./media/automate-with-logic-apps/flow5.png)

5. 從動態內容功能表執行下列動作：
- 選取 [附件名稱]
- 選取 [附件內容]
- 在 [為 HTML] 欄位中選取 [是]

![Office 365 電子郵件設定畫面](./media/automate-with-logic-apps/flow7.png)
### <a name="step-7-save-and-test-your-logic-app"></a>步驟 7： 儲存並測試邏輯應用程式
1. 按一下 [確定] 儲存變更。
1. 您可以等待觸發程式執行邏輯應用程式，或者也可以選擇 [執行] 立即執行。

![邏輯應用程式建立畫面](./media/automate-with-logic-apps/step7.png)

當您的邏輯應用程式執行時，您在電子郵件清單中指定的收件者將收到電子郵件，如下所示：

![邏輯應用程式電子郵件](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>後續步驟

- 深入了解建立 [Analytics 查詢](app-insights-analytics-using.md)。
- 深入了解 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)。



<!--Link references-->






