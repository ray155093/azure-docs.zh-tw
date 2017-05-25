---
title: "使用查詢追蹤 AS2、X12、EDIFACT 訊息 - Azure Logic Apps | Microsoft Docs"
description: "使用查詢在 Operations Management Suite 入口網站中追蹤企業對企業訊息"
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
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 67313e4ff780e6c64dfc3d767ea49167c2a390f4
ms.openlocfilehash: 701a3bbadb340c9390b486a5295eced056db70a0
ms.contentlocale: zh-tw
ms.lasthandoff: 01/31/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>使用查詢在 Operations Management Suite 入口網站中追蹤 B2B 訊息
若要在 Operations Management Suite 入口網站中追蹤企業對企業 (B2B) 訊息，您可以建立會篩選特定交換控制編號資料的查詢。

## <a name="prereqs"></a>必要條件

如需偵錯和更詳細的診斷資訊，在您具有 X12 連接器的 [Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) 之[整合帳戶](logic-apps-monitor-b2b-message.md)中開啟診斷程式。 然後，執行步驟以[發佈診斷資料](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)至 Operations Management Suite 入口網站。

## <a name="search-for-an-interchange-control-number"></a>搜尋交換控制編號

1. 在開始頁面上，選取 [記錄搜尋]。  
![選取記錄檔搜尋](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. 在 [搜尋] 方塊中，輸入 **Type="AzureDiagnostics"**。 若要篩選資料，選取 [新增]。  
![選取查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. 輸入 **interchange**、選取 **event_record_messageProperties_interchangeControlNumber_s**，然後選取 [新增]。  
![新增篩選器](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. 選取您想要篩選資料的控制編號並選取 [套用]。  
![搜尋控制編號](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. 尋找您所建立的查詢以篩選所選控制編號的資料。   
![尋找查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. 輸入查詢的名稱，然後加以儲存。   
![儲存查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. 若要檢視查詢並使用於未來的搜尋中，選取 [我的最愛]。  
![檢視查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. 您可以更新查詢，以搜尋新的交換控制編號。  
![更新查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>後續步驟
* 深入了解[自訂追蹤結構描述](logic-apps-track-integration-account-custom-tracking-schema.md)。   
* 深入了解 [AS2 追蹤結構描述](logic-apps-track-integration-account-as2-tracking-schemas.md)。    
* 深入了解 [X12 追蹤結構描述](logic-apps-track-integration-account-x12-tracking-schema.md)。  
* [深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)。

