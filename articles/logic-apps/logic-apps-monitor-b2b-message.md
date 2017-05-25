---
title: "監視 B2B 交易中的訊息 - Azure Logic Apps | Microsoft Docs"
description: "使用整合帳戶中的 Logic Apps 監視並追蹤程序與應用程式 B2B 通訊期間的訊息。"
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
ms.custom: H1Hack27Feb2017
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: dc760b4c08d0e1afff3bc1276f6ed2367d67629e
ms.contentlocale: zh-tw
ms.lasthandoff: 03/01/2017

---

# <a name="start-or-enable-logging-of-as2-x12-and-edifact-messages-to-monitor-success-errors-and-message-properties"></a>啟動或啟用 AS2、X12 和行政、商業、貿易的電子資料交換 (EDIFACT) 訊息的記錄，以監視成功、錯誤和訊息屬性

B2B 通訊牽涉到兩個執行中商務程序或應用程式之間的訊息交換。 關聯性定義商務程序之間的合約。 通訊建立之後，您可以設定訊息監視來檢查通訊是否如預期般運作。 如需更豐富的詳細資料和偵錯，您可以為整合帳戶設定診斷。

訊息追蹤適用於下列 B2B 通訊協定：AS2、X12 和 EDIFACT。 

## <a name="prerequisites"></a>必要條件

* Azure 帳戶；您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* 整合帳戶；您可以建立[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)。
* 邏輯應用程式；您可以建立[邏輯應用程式](logic-apps-create-a-logic-app.md)並[啟用記錄](logic-apps-monitor-your-logic-apps.md)。

## <a name="enable-logging-for-an-integration-account"></a>為整合帳戶啟用記錄

您可以使用 **Azure 入口網站**或**監視**來為整合帳戶啟用記錄。

### <a name="enable-logging-with-azure-portal"></a>使用 Azure 入口網站啟用記錄

1. 選取您的整合帳戶，然後選取 [診斷記錄]。

    ![選取您的整合帳戶](media/logic-apps-monitor-b2b-message/pic5.png)

2. 選取 [訂用帳戶] 和 [資源群組]。 從 [資源類型] 中選取 [整合帳戶]。 從 [資源] 中選取您的整合帳戶。 針對您選取的整合帳戶，按一下 [開啟診斷] 來啟用診斷。

    ![針對整合帳戶設定診斷](media/logic-apps-monitor-b2b-message/pic2.png)

3. 選取狀態 [開啟]。

    ![開啟診斷狀態](media/logic-apps-monitor-b2b-message/pic3.png)

4. 選取 [傳送至 Log Analytics] 並設定 Log Analytics 來發出資料。

    ![傳送診斷資料到記錄](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>使用監視來啟用記錄

0. 依序選取 [監視]、[診斷記錄]。

    ![依序選取 [監視]、[診斷記錄]](media/logic-apps-monitor-b2b-message/pic1.png)

0. 選取 [訂用帳戶] 和 [資源群組]。 從 [資源類型] 中選取 [整合帳戶]。 從 [資源] 中選取您的整合帳戶。 針對您選取的整合帳戶，按一下 [開啟診斷] 來啟用診斷。

    ![針對整合帳戶設定診斷](media/logic-apps-monitor-b2b-message/pic2.png)

0. 選取狀態 [開啟]。

    ![開啟診斷狀態](media/logic-apps-monitor-b2b-message/pic3.png) 

0. 選取 [傳送至 Log Analytics] 並設定 **Log Analytics** 來發出資料。

    ![傳送診斷資料到記錄](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>延伸您的解決方案

除了 **Log Analytics**，您可以將整合帳戶和 [Logic Apps](./logic-apps-monitor-your-logic-apps.md) 設定到事件中樞或儲存體帳戶。

![Azure 診斷設定](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

您可以使用此遙測從事件中樞或儲存體進入其他服務，例如 [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)和 [Power BI](https://powerbi.com)，以便即時監視您的整合工作流程。

## <a name="supported-tracking-schema"></a>支援的追蹤結構描述

我們支援下列追蹤結構描述類型，其中除了自訂類型外都有固定的結構描述。

* [自訂追蹤結構描述](logic-apps-track-integration-account-custom-tracking-schema.md)
* [AS2 追蹤結構描述](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 追蹤結構描述](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

[在 OMS 入口網站中追蹤 B2B 訊息](logic-apps-track-b2b-messages-omsportal.md "追蹤 B2B 訊息")

[深入了解企業整合套件](logic-apps-enterprise-integration-overview.md "了解企業整合套件")


