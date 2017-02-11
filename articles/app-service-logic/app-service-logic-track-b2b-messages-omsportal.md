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
ms.sourcegitcommit: b3f564e32105708ddbd9027240c897fdd8ae2ac6
ms.openlocfilehash: 673b128c628a13c4af6c73c1a2d82953aadfd45a


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>在 OMS 入口網站中追蹤 B2B 訊息
B2B 通訊牽涉到兩個執行中商務程序或應用程式之間的訊息交換。 在 OMS 入口網站中追蹤 B2B 訊息可利用豐富的 Web 型追蹤功能，讓您看到是否正確地處理訊息。  您可以追蹤

* 訊息的計數和狀態
* 通知狀態
* 訊息與通知的相互關聯
* 失敗的詳細錯誤描述
* 搜尋功能

## <a name="prerequisites"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* 整合帳戶；您可以建立[整合帳戶](app-service-logic-enterprise-integration-create-integration-account.md)並啟用記錄；您可以在[這裡](app-service-logic-monitor-b2b-message.md)找到步驟
* 邏輯應用程式；您可以建立[邏輯應用程式](app-service-logic-create-a-logic-app.md)並啟用記錄；您可以在[這裡](app-service-logic-monitor-your-logic-apps.md)找到步驟

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

8. 張貼訊息程序；首頁上會更新訊息計數 ![選取首頁](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

9. 在首頁選取 **Logic Apps B2B 訊息**會移至 AS2 和 X12 訊息狀態。  資料是根據最後一天。
![選取 Logic Apps B2B 訊息](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

10. 依狀態選取 AS2 或 X12 訊息會帶您前往郵件清單 ![選取 AS2 訊息狀態](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![選取 X12 訊息狀態](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

11. 在 AS2 或 X12 訊息清單中選取一列會帶您前往記錄搜尋。  記錄搜尋會列出所有具有相同**執行識別碼**的動作
![選取訊息狀態](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)


## <a name="next-steps"></a>後續步驟
[自訂追蹤結構描述](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[AS2 追蹤結構描述](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[X12 追蹤結構描述](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


