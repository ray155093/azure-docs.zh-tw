---
title: "監視 B2B 訊息 | Microsoft Docs"
description: "如何監視整合帳戶"
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
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>監視 B2B 訊息
B2B 通訊牽涉到兩個執行中商務程序或應用程式之間的訊息交換。 關聯性定義商務程序之間的合約。 建立通訊後，必須設法監視通訊是否正常運作。  已實作 B2B 通訊協定的訊息追蹤︰AS2、X12 和 EDIFACT。  您可以設定整合帳戶來使用診斷，以取得更豐富的詳細資訊和進行偵錯

## <a name="prerequisites"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* 整合帳戶；您可以建立[整合帳戶](app-service-logic-enterprise-integration-create-integration-account.md)
* 邏輯應用程式；您可以建立[邏輯應用程式](app-service-logic-create-a-logic-app.md)並啟用記錄；您可以在[這裡](app-service-logic-monitor-your-logic-apps.md)找到步驟

## <a name="enable-logging-for-an-integration-account"></a>為整合帳戶啟用記錄
您可以使用 **Azure 入口網站**或**監視**來為整合帳戶啟用記錄

### <a name="enable-logging-with-azure-portal"></a>使用 Azure 入口網站啟用記錄

1. 選取 [整合帳戶]****，然後選取 [診斷記錄檔]**** 
![選取整合帳戶](./media/app-service-logic-monitor-integration-account/Pic5.png)  

2. 選取您的 [訂用帳戶] 和 [資源群組]，從 [資源類型] 選取 [整合帳戶]，然後從 [資源] 下拉式清單選取您的 [整合帳戶]，以便啟用診斷。  針對選取的整合帳戶，按一下 [開啟診斷]來啟用診斷               
![選取整合帳戶](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. 選取狀態**開啟**       
![開啟診斷](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. 選取 [傳送至 Log Analytics] 並設定 Log Analytics 來發出資料               
![開啟診斷](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>使用監視來啟用記錄

1. 選取 [監視]，按一下 [診斷記錄檔]   
![選取 [監視]](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. 選取您的 [訂用帳戶] 和 [資源群組]，從 [資源類型] 選取 [整合帳戶]，然後從 [資源] 下拉式清單選取您的 [整合帳戶]，以便啟用診斷。  針對選取的整合帳戶，按一下 [開啟診斷]來啟用診斷               
![選取整合帳戶](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. 選取狀態**開啟**       
![開啟診斷](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. 選取 [傳送至 Log Analytics] 並設定 Log Analytics 來發出資料 ![開啟診斷](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>延伸您的方案
除了 **Log Analytics**，您可以將整合帳戶和 [Logic Apps](./app-service-logic-monitor-your-logic-apps.md) 設定到事件中樞或儲存體帳戶         
![Azure 診斷設定](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

您可以使用此遙測從事件中樞或儲存體進入其他服務，例如 [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)和 [Power BI](https://powerbi.com)，以便即時監視您的整合工作流程。

## <a name="supported-tracking-schema"></a>支援的追蹤結構描述
我們支援下列追蹤結構描述類型。  除了 [自訂] 類型以外，全部都有固定的結構描述。

* [自訂追蹤結構描述](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [AS2 追蹤結構描述](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 追蹤結構描述](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>後續步驟
[在 OMS 入口網站中追蹤 B2B 訊息](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


