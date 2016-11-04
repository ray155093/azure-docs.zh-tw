---
title: Microsoft Azure 中的警示概觀 | Microsoft Docs
description: 警示可讓您監視 Azure 資源度量、事件或記錄檔，並在您所指定條件符合時收到通知。
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb

---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警示概觀
本文章說明何謂警示、其優點，以及如何開始使用它們。  

## <a name="what-are-alerts?"></a>何謂警示？
警示是監視 Azure 資源度量、事件或記錄檔，並在您所指定條件符合時的通知方法。

您可能會根據下列條件收到警示︰

* **度量值**：當指定的度量值超出您在任一方向指派的閾值時會觸發這個警示。 也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。
* **活動記錄檔事件**：這個警示可觸發每一個事件，或是僅在發生特定事件數目時才觸發。

## <a name="alerts-in-different-azure-services"></a>不同 Azure 服務中的警示
警示可跨不同的服務使用，包括：

* **Application Insights**：允許 web 測試及度量警示。 請參閱 [ Application Insights 中設定警示](../application-insights/app-insights-alerts.md)和[監視任何網站的可用性和回應性](../application-insights/app-insights-monitor-web-app-availability.md)。
* **Log Analytics (Operations Management Suite)**：啟用對 Log Analytics 的診斷記錄路由。 Operations Management Suite 可允許度量、記錄和其他警示類型。 如需詳細資訊，請參閱 [Log Analytics 中的警示](../log-analytics/log-analytics-alerts.md)。   
* **Azure 監視器**：允許根據度量值和活動記錄檔事件的警示。 Azure 監視器包含 [Azure Insights REST API](https://msdn.microsoft.com/library/dn931943.aspx)。  如需詳細資訊，請參閱 [使用 Azure 入口網站、PowerShell 或命令列介面來建立警示](insights-alerts-portal.md)。

## <a name="alert-actions"></a>警示動作
您可以設定警示執行下列動作︰

* 將電子郵件通知傳送至服務管理員、共同管理員或您指定的其他電子郵件。
* 呼叫 webhook，可讓您啟動其他自動化動作。
  
  ![警示的說明](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## <a name="next-steps"></a>後續步驟
使用下列項目取得有關警示規則和設定這些規則的資訊：

* [Azure 入口網站](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [命令列介面 (CLI)](../azure-portal/insights-alerts-command-line-interface.md)
* [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!--HONumber=Oct16_HO2-->


