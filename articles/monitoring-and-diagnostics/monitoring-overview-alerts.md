---
title: "Microsoft Azure 和 Azure 監視器中的警示概觀 | Microsoft Docs"
description: "警示可讓您監視 Azure 資源度量、事件或記錄檔，並在您所指定條件符合時收到通知。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 76c8feb077cca27dc96f43e708cdef4fbb0f824c
ms.lasthandoff: 03/31/2017


---
# <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警示是什麼？
本文章說明何謂警示、其優點，以及如何開始使用它們。 此內容特別適用於 Azure 監視器，但會提供其他服務的指標。  

警示是監視 Azure 資源度量、事件或記錄，並在您所指定條件符合時的通知方法。  

## <a name="alerts-in-different-azure-services"></a>不同 Azure 服務中的警示
警示可跨不同的服務使用，包括：

* **Application Insights**：允許 web 測試及度量警示。 請參閱 [ Application Insights 中設定警示](../application-insights/app-insights-alerts.md)和[監視任何網站的可用性和回應性](../application-insights/app-insights-monitor-web-app-availability.md)。
* **Log Analytics (Operations Management Suite)**：啟用對 Log Analytics 之活動和診斷記錄的路由。 Operations Management Suite 可允許度量、記錄和其他警示類型。 如需詳細資訊，請參閱 [Log Analytics 中的警示](../log-analytics/log-analytics-alerts.md)。  
* **Azure 監視器**：允許根據度量值和活動記錄檔事件的警示。 您可以使用 [Azure 監視器 REST API](https://msdn.microsoft.com/library/dn931943.aspx) 來管理警示。  如需詳細資訊，請參閱 [使用 Azure 入口網站、PowerShell 或命令列介面來建立警示](insights-alerts-portal.md)。

## <a name="visual-summary"></a>視覺化摘要
下圖摘要說明警示以及警示可執行的作業 (特別是在「Azure 監視器」中)。 其他動作可能會適用於先前列出的服務。 例如，目前來說，診斷記錄的警示僅適用於 Log Analytics。

![警示的說明](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="what-can-trigger-alerts-in-azure-monitor"></a>可在 Azure 監視器中觸發警示的項目

您可能會根據下列條件收到警示︰

* **度量值**：當指定的度量值超出您在任一方向指派的閾值時會觸發這個警示。 也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。 Azure 監視器所支援的可用度量清單會不斷成長，如需此清單，請參閱 [Azure 監視器上支援的度量清單](monitoring-supported-metrics.md)。
* **活動記錄事件**：此警示可以在特定事件於資源上發生時觸發，或是在服務通知張貼至您的訂用帳戶時觸發。

## <a name="what-can-metric-alerts-do"></a>計量警示可以做什麼？
您可以設定警示來執行下列動作︰

* 將電子郵件通知傳送至服務管理員、共同管理員或您指定的其他電子郵件。
* 呼叫 webhook，可讓您啟動其他自動化動作。 範例包括呼叫：
    - Azure 自動化 Runbook
    - Azure Function
    - Azure 邏輯應用程式
    - 第三方服務

## <a name="what-can-activity-log-alerts-do"></a>活動記錄警示可以做什麼？
您可以設定警示來執行下列動作︰
* 在特定事件於您訂用帳戶下的其中一個資源上發生時觸發
* 在服務通知張貼至您的訂用帳戶時觸發
* 透過下列方式對動作群組的成員發出警示：
    * sms
    * 電子郵件
    * Webhook

## <a name="next-steps"></a>後續步驟
使用下列項目取得有關警示規則和設定這些規則的資訊：

* 深入了解[計量](monitoring-overview-metrics.md)
* [透過 Azure 入口網站設定計量警示](insights-alerts-portal.md)
* [透過 PowerShell 設定計量警示](insights-alerts-powershell.md)
* [透過命令列介面 (CLI) 設定計量警示](insights-alerts-command-line-interface.md)
* [透過 Azure 監視器 REST API 設定計量警示](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 深入了解[活動記錄](monitoring-overview-activity-logs.md)
* [透過 Azure 入口網站設定活動記錄警示](monitoring-activity-log-alerts.md)
* [透過 Resource Manager 設定活動記錄警示](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 檢閱[活動記錄警示 webhook 結構描述](monitoring-activity-log-alerts-webhook.md)
* 深入了解[服務通知](monitoring-service-notifications.md)
* 深入了解[動作群組](monitoring-action-groups.md)

