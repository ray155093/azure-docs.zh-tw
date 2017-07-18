---
title: "在 Operations Management Suite 入口網站 - Azure 中追蹤 B2B 訊息 | Microsoft Docs"
description: "如何在 Operations Management Suite 入口網站中追蹤 B2B 訊息"
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
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: d1c61ba220b4334f053428a23e620e8004fc60f7
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 入口網站中追蹤 B2B 訊息

![記錄應用程式 B2B 符號](./media/logic-apps-track-b2b-messages-omsportal/logic-apps-b2b-symbol.png)

B2B 通訊牽涉到兩個執行中商務程序或應用程式之間的訊息交換。 在 Operations Management Suite 入口網站中使用下列網頁型追蹤功能，以確認是否正確地處理訊息︰

* 訊息計數和狀態
* 通知狀態
* 訊息與通知的相互關聯
* 失敗的詳細錯誤描述
* 搜尋功能

## <a name="prerequisites"></a>必要條件
* 一個 Azure 帳戶。 您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* 整合帳戶。 您可以建立[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)並設定記錄。 若要設定記錄，請參閱[監視 B2B 訊息](logic-apps-monitor-b2b-message.md)。
* 邏輯應用程式。 您可以建立[邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)及設定記錄。 若要設定記錄，請參閱 [Azure 診斷和警示](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)。

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>將邏輯應用程式 B2B 方案新增至 Operations Management Suite 入口網站

1. 在 Azure 入口網站中，選取 [更多服務]、搜尋 Log Analytics，然後選取 **Log Analytics**。   
![搜尋 log analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. 選取您的 **Log Analytics**。  
![選取 [Log Analytics]](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. 選取 **OMS 入口網站**。 隨即出現 Operations Management Suite 入口網站首頁。   
![瀏覽 Operations Management Suite 入口網站](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. 選取**方案庫**。    
![選取方案庫](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. 選取 [Logic Apps B2B]。     
![選取 [Logic Apps B2B]](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. 選取 [新增]，將 **Logic Apps B2B** 訊息新增至首頁。  
![選取 [新增]](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. **Logic Apps B2B 訊息**會出現在首頁上。   
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 入口網站中追蹤資料

1. 處理訊息之後，就會出現更新的訊息計數。   
![更新的訊息](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. 在首頁選取「Logic Apps B2B 訊息」以檢視 AS2 和 X12 訊息狀態。  資料是根據一天。
![選取 Logic Apps B2B 訊息](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. 依狀態選取 AS2 或 X12 或 EDIFACT 訊息，以移至訊息清單。 下一個營幕擷取畫面會顯示 AS2 訊息狀態。 AS2 和 X12 訊息狀態屬性描述稍後會出現在「訊息清單屬性描述」。  
![選取 AS2 訊息狀態](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

4. 在 AS2 或 X12 或 EDIFACT 訊息清單中選取一列，以前往記錄搜尋。  記錄搜尋會列出所有具有相同執行識別碼的動作。
![選取訊息狀態](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>訊息清單屬性描述

#### <a name="as2-message-list-property-descriptions"></a>AS2 訊息清單屬性描述

| 屬性 | 說明 |
| --- | --- |
| 傳送者 | 針對 AS2 協議的接收設定中所設定的來賓合作夥伴，或傳送設定中設定的主機合作夥伴。 |
| 接收者 | 針對 AS2 協議的接收設定中所設定的主機合作夥伴，或傳送設定中設定的來賓合作夥伴。 |
| 邏輯應用程式 | 已設定 AS2 動作的邏輯應用程式。 |
| 狀態 | AS2 訊息狀態 <br>成功 = 已接收或傳送良好的 AS2 訊息，未設定任何 MDN <br>成功 = 已接收或傳送良好的 AS2 訊息，MDN 已設定且接收或 MDN 已傳送 <br>失敗 = 接收不正確的 AS2 訊息，未設定任何 MDN <br>暫止 = 已接收或傳送良好的 AS2 訊息，MDN 已設定且預期為 MDN |
| Ack | MDN 訊息狀態 <br>接受 = 已接收或傳送正值的 MDN <br>暫止 = 等候接收或傳送 MDN <br>拒絕 = 已接收或傳送負值的 MDN <br>不需要 = MDN 在協議中未設定 |
| 方向 | AS2 訊息方向。 |
| 相互關連識別碼 | 與所有邏輯應用程式內的觸發程序和動作相關聯的識別碼。 |
| 訊息識別碼 |  AS2 訊息識別碼，來自 AS2 訊息的標頭。 |
| Timestamp | AS2 動作處理訊息的時間。 |

#### <a name="x12-message-list-property-descriptions"></a>X12 訊息清單屬性描述

| 屬性 | 說明 |
| --- | --- |
| 傳送者 | 針對 X12 合約之接收設定中所設定的來賓夥伴，或傳送設定中所設定的主機夥伴。 |
| 接收者 | 針對 X12 合約之接收設定中所設定的主機夥伴，或傳送設定中所設定的來賓夥伴。 |
| 邏輯應用程式 | 已設定 AS2 動作的邏輯應用程式。 |
| 狀態 | X12 訊息狀態 <br>成功 = 已接收或傳送良好的 X12 訊息，未設定任何函式認可 <br>成功 = 已接收或傳送良好的 X12 訊息，函式認可已設定且接收，或已傳送函式認可 <br>失敗 = 已接收或傳送不正確的 X12 訊息 <br>暫止 = 已接收或傳送良好的 X12 訊息，函式認可已設定且應該函式認可。 |
| Ack | 功能認可 (997) 狀態 <br>接受 = 已接收或傳送正值的函式認可 <br>拒絕 = 已接收或傳送負值的函式認可 <br>暫止 = 預期函式認可但未收到 <br>暫止 = 已產生函式認可，但無法傳送給夥伴 <br>不需要 = 未設定函式認可 |
| 方向 | X12 訊息方向。 |
| 相互關連識別碼 | 與所有邏輯應用程式內的觸發程序和動作相關聯的識別碼。 |
| 訊息類型 |  EDI X12 訊息類型。 |
| ICN | X12 訊息的交換控制編號。 |
| TSCN | X12 訊息的交易集控制編號。 |
| Timestamp | X12 動作處理訊息的時間。 |


#### <a name="edifact-message-list-property-descriptions"></a>EDIFACT 訊息清單屬性描述

| 屬性 | 說明 |
| --- | --- |
| 傳送者 | 針對 EDIFACT 合約之接收設定中所設定的來賓夥伴，或傳送設定中所設定的主機夥伴。 |
| 接收者 | 針對 EDIFACT 合約之接收設定中所設定的主機夥伴，或傳送設定中所設定的來賓夥伴。 |
| 邏輯應用程式 | 已設定 AS2 動作的邏輯應用程式。 |
| 狀態 | EDIFACT 訊息狀態 <br>成功 = 已接收或傳送良好的 X12 訊息，未設定任何函式認可 <br>成功 = 已接收或傳送良好的 X12 訊息，函式認可已設定且接收，或已傳送函式認可 <br>失敗 = 已接收或傳送不正確的 X12 訊息 <br>暫止 = 已接收或傳送良好的 X12 訊息，函式認可已設定且應該函式認可。 |
| Ack | 功能認可 (997) 狀態 <br>接受 = 已接收或傳送正值的函式認可 <br>拒絕 = 已接收或傳送負值的函式認可 <br>暫止 = 預期函式認可但未收到 <br>暫止 = 已產生函式認可，但無法傳送給夥伴 <br>不需要 = 未設定函式認可 |
| 方向 | EDIFACT 訊息方向。 |
| 相互關連識別碼 | 與所有邏輯應用程式內的觸發程序和動作相關聯的識別碼。 |
| 訊息類型 |  EDIFACT 訊息類型。 |
| ICN | EDIFACT 訊息的交換控制編號。 |
| TSCN | EDIFACT 訊息的交易集控制編號。 |
| Timestamp | EDIFACT 動作處理訊息的時間。 |


## <a name="queries-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 入口網站中查詢

在 [搜尋] 頁面上，您可以建立查詢。 在搜尋時，您可以使用 facet 控制篩選結果。

### <a name="create-a-query"></a>建立查詢

1. 在記錄搜尋中，撰寫查詢並選取 [儲存]。 [儲存搜尋] 隨即出現。 若要撰寫查詢，請參閱[使用查詢在 Operations Management Suite 入口網站中追蹤 B2B 訊息](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)。
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. 在 [儲存搜尋] 中新增**名稱**和**類別**，然後選取 [儲存]。   
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. 若要檢視查詢，請選取 [我的最愛]。    
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>使用已儲存的查詢

* 在記錄搜尋中，選取 [我的最愛] 來檢視已儲存的查詢。  若要檢視查詢結果，請選取查詢。
![選取首頁](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>後續步驟
[自訂追蹤結構描述](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[AS2 追蹤結構描述](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[X12 追蹤結構描述](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

