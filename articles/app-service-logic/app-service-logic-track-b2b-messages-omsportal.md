---
title: "在 OMS 入口網站中追蹤 B2B 訊息 |Microsoft Docs"
description: "如何在 OMS 入口網站中追蹤 B2B 訊息"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f64fe625b7c3b7e58f86154b9d601eff03e31d3
ms.openlocfilehash: 854db453a3af459c0398c4b88a6789c87747e920


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>在 OMS 入口網站中追蹤 B2B 訊息
B2B 通訊牽涉到兩個執行中商務程序或應用程式之間的訊息交換。 在 OMS 入口網站中追蹤 B2B 訊息可利用豐富的 Web 型追蹤功能，讓您看到訊息是否已正確處理。  您可以追蹤

* 訊息的計數和狀態
* 通知狀態
* 訊息與通知的相互關聯
* 失敗的詳細錯誤描述
* 搜尋功能

## <a name="prerequisites"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* 整合帳戶；您可以建立[整合帳戶](app-service-logic-enterprise-integration-create-integration-account.md)並啟用記錄；您可以在[這裡](app-service-logic-monitor-b2b-message.md)找到步驟
* 邏輯應用程式；您可以建立[邏輯應用程式](app-service-logic-create-a-logic-app.md)並啟用記錄；您可以在[這裡](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)找到步驟

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>將 Logic Apps B2B 方案新增至 OMS 入口網站

1. 在入口網站中選取 [更多服務]****，搜尋 **Log Analytics**，然後選取 **Log Analytics**
![搜尋 Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png)  

2. 選取您的 **Log Analytics**
![選取 Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png)

3. 選取 **OMS 入口網站**，開啟 OMS 入口網站首頁![瀏覽 OMS 入口網站](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png)

4. 選取**方案庫**    
![選取方案庫](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png)

5. 選取 **Logic Apps B2B**
![選取 Logic Apps B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png)

6. 按一下 [新增]，將 **Logic Apps B2B 訊息**新增至首頁 ![選取新增](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png)

7. 瀏覽首頁來檢視 **Logic Apps B2B 訊息**
![選取首頁](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>在 OMS 入口網站中追蹤資料

1. 張貼訊息程序；首頁上會更新訊息計數 ![選取首頁](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

2. 在首頁選取 **Logic Apps B2B 訊息**會移至 AS2 和 X12 訊息狀態。  資料是根據最後一天。
![選取 Logic Apps B2B 訊息](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

3. 依狀態選取 AS2 或 X12 訊息會帶您前往郵件清單 ![選取 AS2 訊息狀態](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![選取 X12 訊息狀態](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

4. 在 AS2 或 X12 訊息清單中選取一列會帶您前往記錄搜尋。  記錄搜尋會列出所有具有相同**執行識別碼**的動作
![選取訊息狀態](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>在 OMS 入口網站中查詢

您可以在搜尋頁面上建立查詢，然後在搜尋時，您可以使用 Facet 控制篩選結果。

### <a name="how-to-create-a-query"></a>如何建立查詢

1. 在記錄搜尋中，撰寫查詢並選取 [儲存]。  [這裡](app-service-logic-track-b2b-messages-omsportal-query-filter-control-number.md)提供撰寫查詢的步驟 ![選取首頁](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery.png)

2. [儲存搜尋] 隨即開啟。  提供「名稱」、「類別」，然後按一下 [儲存]   
![選取首頁](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. 若要檢視查詢，請選取 [我的最愛]    
![選取首頁](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![選取首頁](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>如何使用已儲存的查詢

1. 在記錄搜尋中，選取 [我的最愛] 來檢視已儲存的查詢。  選取其中一個提供結果的查詢 ![選取首頁](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>後續步驟
[自訂追蹤結構描述](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[AS2 追蹤結構描述](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[X12 追蹤結構描述](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


