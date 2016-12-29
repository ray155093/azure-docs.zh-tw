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
ms.sourcegitcommit: 6e56dae0ab30612a7794bd39e6eba2f84bcecc19
ms.openlocfilehash: eae37a357e3e1c00310c7c638d3164118403ee9f


---
# <a name="create-a-query-in-oms-portal"></a>在 OMS 入口網站中建立查詢 
建立查詢以針對特定交換控制編號篩選資料的步驟

## <a name="prerequisites"></a>必要條件

為您的[整合帳戶](app-service-logic-monitor-b2b-message.md) (具有 X12 連接器的[邏輯應用程式](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)) 啟用診斷，以獲得更豐富的詳細資訊和進行偵錯。  請依照[這裡](app-service-logic-track-b2b-messages-omsportal.md)的步驟將診斷資料發佈至 OMS 入口網站。

## <a name="create-a-query-to-search-data-for-a-specific-interchange-control-number"></a>建立查詢以搜尋特定交換控制編號的資料

1. 選取首頁上的 [記錄檔搜尋]  
![選取記錄檔搜尋](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. 在搜尋視窗中輸入 **Type="AzureDiagnostics"**，提取所有資料。  按一下 [新增] 以篩選資料  
![選取查詢](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. 輸入 **interchange**、選取 **event_record_messageProperties_interchangeControlNumber_s**，然後按一下 [新增  
![選取控制編號](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. 選取您想要篩選資料的控制編號並按一下 [套用]  
![選取控制編號](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. 您可以尋找所建立的查詢以篩選所選控制編號的資料   
![選取控制編號](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. 指定查詢的名稱並加以儲存   
![選取控制編號](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png) 

7. 選取 [我的最愛] 以檢視查詢並使用於未來的搜尋中  
![選取控制編號](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. 您可以更新查詢，以搜尋新的交換控制編號  
![選取控制編號](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png) 


## <a name="next-steps"></a>後續步驟
[自訂追蹤結構描述](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[AS2 追蹤結構描述](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[X12 追蹤結構描述](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


