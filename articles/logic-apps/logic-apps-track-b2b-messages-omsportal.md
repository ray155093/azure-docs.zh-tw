---
title: "在 OMS 入口網站中追蹤 B2B 訊息 |Microsoft Docs"
description: "如何在 OMS 入口網站中追蹤 B2B 訊息"
author: padmavc
manager: anneta
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
ms.sourcegitcommit: 53195091ac4b93ed94f432990c84c407615fc03e
ms.openlocfilehash: 9c3855c7fce5a9f38424f0bb6cd03f7a2c8d36be


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
* 整合帳戶；您可以建立[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)並啟用記錄；您可以在[這裡](logic-apps-monitor-b2b-message.md)找到步驟
* 邏輯應用程式；您可以建立[邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)並啟用記錄；您可以在[這裡](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)找到步驟

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>將 Logic Apps B2B 方案新增至 OMS 入口網站

1. 在入口網站中選取 [更多服務]，搜尋 **log analytics** 並選取 [Log Analytics]   
![搜尋 log analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. 選取您的 **Log Analytics**  
![選取 [Log Analytics]](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. 選取 [OMS 入口網站]，開啟 OMS 入口網站首頁   
![瀏覽 OMS 入口網站](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. 選取**方案庫**    
![選取方案庫](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. 選取 [Logic Apps B2B]     
![選取 [Logic Apps B2B]](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. 按一下 [新增]，將「Logic Apps B2B 訊息」新增至首頁  
![選取 [新增]](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. 瀏覽首頁以檢視「Logic Apps B2B 訊息」   
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>在 OMS 入口網站中追蹤資料

1. 張貼訊息程序；首頁會更新訊息計數   
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. 在首頁選取「Logic Apps B2B 訊息」會移至 AS2 和 X12 訊息狀態。  資料是根據最後一天。
![選取 Logic Apps B2B 訊息](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)



3. 根據狀態選取 AS2 或 X12 訊息會帶您前往訊息清單   
![選取 AS2 訊息狀態](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

| 屬性 | 說明 |
| --- | --- |
| 傳送者 | 針對 AS2 協議的接收設定中所設定的來賓合作夥伴，或傳送設定中設定的主機合作夥伴 |
| 接收者 | 針對 AS2 協議的接收設定中所設定的主機合作夥伴，或傳送設定中設定的來賓合作夥伴 |
| 邏輯應用程式 | 已設定 AS2 動作的邏輯應用程式 |
| 狀態 | AS2 訊息狀態。 成功 = 已接收或傳送良好的 AS2 訊息，沒有設定 MDN；成功 = 已接收或傳送良好的 AS2 訊息，已設定且已接收或已傳送 MDN；失敗 = 收到錯誤的 AS2 訊息，沒有設定 MDN；暫止 = 已接收或傳送良好的 AS2 訊息，已設定 MDN 並預期功能認可； |
| Ack | MDN 訊息狀態 |
| 方向 | AS2 訊息方向 |
| 相互關連識別碼 | 與所有邏輯應用程式內的觸發程序和動作相關聯的識別碼 |
| 訊息識別碼 |  AS2 訊息識別碼，來自 AS2 訊息的標頭 |
| Timestamp | AS2 動作處理訊息的時間 |
|  |  |


![選取 X12 訊息狀態](media/logic-apps-track-b2b-messages-omsportal/x12messagelist.png)

| 屬性 | 說明 |
| --- | --- |
| 傳送者 | 針對 AS2 協議的接收設定中所設定的來賓合作夥伴，或傳送設定中設定的主機合作夥伴 |
| 接收者 | 針對 AS2 協議的接收設定中所設定的主機合作夥伴，或傳送設定中設定的來賓合作夥伴 |
| 邏輯應用程式 | 已設定 AS2 動作的邏輯應用程式 |
| 狀態 | X12 訊息狀態。 成功 = 已接收或傳送良好的 X12 訊息，沒有設定功能通知；成功 = 已接收或已傳送良好的 X12 訊息，已設定且已接收或已傳送功能通知；失敗 = 已收到或已傳送錯誤的 X12 訊息；暫止 = 已接收或已傳送良好的 X12 訊息，已設定功能通知並預期功能認可 |
| Ack | 功能認可 (997) 狀態。  接受 = 已接收或已傳送正值的功能通知；拒絕 = 已接收或已傳送負值的功能通知；暫止 = 預期功能認可，但未收到；暫止 = 產生功能認可，但無法傳送給合作夥伴 |
| 方向 | X12 訊息方向 |
| 相互關連識別碼 | 與所有邏輯應用程式內的觸發程序和動作相關聯的識別碼 |
| 訊息類型 |  EDI X12 訊息類型 |
| ICN | X12 訊息的交換控制編號 |
| TSCN | X12 訊息的交易集控制編號 |
| Timestamp | X12 動作處理訊息的時間 |
| | |

4. 在 AS2 或 X12 訊息清單中選取一列會帶您前往記錄搜尋。  記錄搜尋會列出所有具有相同**執行識別碼**的動作
![選取訊息狀態](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>在 OMS 入口網站中查詢

您可以在搜尋頁面上建立查詢，然後在搜尋時，您可以使用 Facet 控制篩選結果。

### <a name="how-to-create-a-query"></a>如何建立查詢

1. 在記錄搜尋中，撰寫查詢並選取 [儲存]。  [這裡](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)提供撰寫查詢的步驟 ![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. [儲存搜尋] 隨即開啟。  提供「名稱」、「類別」，然後按一下 [儲存]   
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. 若要檢視查詢，請選取 [我的最愛]    
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>如何使用已儲存的查詢

* 在記錄搜尋中，選取 [我的最愛] 來檢視已儲存的查詢。  選取其中一個提供結果的查詢 ![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>後續步驟
[自訂追蹤結構描述](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[AS2 追蹤結構描述](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[X12 追蹤結構描述](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Jan17_HO4-->


