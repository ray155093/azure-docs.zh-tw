---
title: "OMS Log Analytics 中的警示概觀 | Microsoft Docs"
description: "Log Analytics 中的警示會識別您的 OMS 儲存機制中的重要資訊，並可主動通知您相關問題或叫用動作以嘗試更正問題。  本文描述如何建立警示規則及詳細說明可以採取的各種動作。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: ec7167fb36e54219957629699f33dfce950f86d5
ms.openlocfilehash: 48d921650dbbf3f9cc2a8a0e265e0c7deaebdafd
ms.lasthandoff: 03/01/2017


---
# <a name="respond-to-issues-in-log-analytics-using-alerts"></a>回應在 Log Analytics 中使用警示的問題

Log Analytics 中的警示會識別您的 Log Analytics 儲存機制中的重要資訊。  您可以使用它們以主動通知您發生問題，或採用自動化動作以回應問題。  本文提供如何建立及使用警示的概觀。  


>[!NOTE]
> 如需計量測量警示規則的相關資訊 (目前為公開預覽狀態)，請參閱[公用預覽版本中的新計量測量警示規則類型！(英文)](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/)。

## <a name="alert-rules"></a>警示規則

警示會由自動定期執行記錄搜尋的警示規則所建立。  如果記錄搜尋的結果符合特定準則，則會建立警示的記錄。  此規則還可以自動執行一或多個動作，以主動通知警示或叫用另一個處理序。  

![Log Analytics 警示](media/log-analytics-alerts/overview.png)


警示規則會由下列詳細資料定義。

- **記錄搜尋**。  這是每次引發警示規則都會執行的查詢。  此查詢所傳回的記錄將會用來判斷是否要建立警示。
- **時間範圍**。  指定查詢的時間範圍。  查詢只會傳回在此目前時間範圍內建立的記錄。  可以是介於 5 分鐘與 24 小時之間的任何值。 例如，如果時間範圍設定為 60 分鐘，則查詢會在下午 1:15 執行，只會傳回在下午 12:15 與下午 1:15 之間建立的記錄。
- **頻率**。  指定應執行查詢的頻率。 可以是介於 5 分鐘與 24 小時之間的任何值。 應等於或小於此時間範圍。  如果值大於時間範圍，則您可能有遺漏記錄的風險。<br>例如，請考慮 30 分鐘的時間範圍，以及 60 分鐘的頻率。  如果在 1:00 執行查詢，它會傳回 12:30 到下午 1:00 之間的記錄。  下一次執行查詢就是 2:00 時，它會傳回 1:30 至 2:00 之間的記錄。  1:00 和 1:30 之間建立的任何記錄一律不會評估。
- **閾值**。  如果記錄搜尋所傳回的記錄數目超過閾值，則會建立警示。

## <a name="creating-alert-rules"></a>建立警示規則
您可以建立及修改多個方法的警示規則。  請參閱以下文章中的詳細指引。  

- 使用 [OMS 入口網站](log-analytics-alerts-creating.md)建立警示規則。
- 使用 [Resource Manager 範本](log-analytics-template-workspace-configuration.md)建立叢集規則。
- 使用 [REST API](log-analytics-api-alerts.md) 建立警示規則。

## <a name="alert-actions"></a>警示動作

除了建立警示記錄，它建立警示時，警示規則可採用一或多個動作。  您可以使用動作來傳送一郵件封以回應警示，或啟動嘗試採取修正動作的處理序。  

您也可以利用動作強化 Log Analytics 功能與其他服務。  例如，它目前不提供使用 SMS 或電話通知您的功能。  不過您可以使用警示規則中的 webhook 動作來呼叫服務，例如提供這些功能的 [PagerDuty](https://www.pagerduty.com/)。  您可以逐步解說在 [OMS Log Analytics 中建立警示 webhook 動作以將訊息傳送至 Slack](log-analytics-alerts-webhooks.md) 中使用 [Slack](https://slack.com/) 建立 webhook 來傳送訊息的範例。

下表列出您可以採取的動作。  您可以在[將動作新增至 Log Analytics 中的警示規則](log-analytics-alerts-actions.md)了解每一種方式。 

| 動作 | 說明 |
|:--|:--|
| 電子郵件  |     傳送內含警示詳細資料的電子郵件給一或多位收件者。 |
| Webhook | 透過單一 HTTP POST 要求叫用外部處理序。 |
| Runbook | 在 Azure 自動化中啟動 Runbook。 |


## <a name="alerting-scenarios"></a>警示狀況

### <a name="events"></a>事件
若要對單一事件發出警示，請建立結果數目為**大於 0** 的警示規則，並將頻率與時間範圍設為 **5 分鐘**。  如此一來，將會每隔 5 分鐘執行一次查詢，並檢查在上次執行查詢後是否發生所建立的單一事件。  較長的頻率可能會延遲收集事件和建立警示的時間。  您會使用類似下列的查詢來指定您感興趣的事件。

    Type=Event Source=MyApplication EventID=7019 

有些應用程式可能會記錄不一定會產生警示的偶發錯誤。  例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。  在此情況下，除非在特定時間範圍內建立多個事件，否則您不會想建立警示。  若要這樣做，您會使用相同的查詢，但將閾值設定為較高的數目。  例如，若要在 30 分鐘內警示 5 個事件，您將頻率設為 **5 分鐘**，時間視窗設為 **30 分鐘內**，和結果數目設為**大於 4**。    

在某些情況下，您可能想在某個事件不存在時建立警示。  例如，處理序可能會記錄一般事件，表示運作正常。  如果它不在特定的時間範圍內記錄一個事件，則應建立警示。  在此情況下，您會將結果數目設定為**小於 1**。

### <a name="performance-alerts"></a>效能警示
[效能資料](log-analytics-data-sources-performance-counters.md) 會儲存為 Log Analytics 儲存機制中類似事件的記錄。  如果您要在效能計數器超過特定臨界值時發出警示，則該臨界值應包含在查詢中。

例如，如果您想要在處理器執行超過 90% 時發出警示，您會使用如下的查詢，且警示規則的結果數目**大於 0**。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

如果您想要針對特定的時間範圍在處理器平均超過 90% 時發出警示，您會利用如下的[測量命令](log-analytics-search-reference.md#commands)來使用查詢，且警示規則的臨界值**大於 0**。 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90


## <a name="alert-records"></a>警示記錄
在 Log Analytics 中由警示規則建立的警示記錄，**Type** 為 **Alert**，**SourceSystem** 為 **OMS**。  其屬性如下表所示。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*警示* |
| SourceSystem |*OMS* |
| AlertName |警示的名稱。 |
| AlertSeverity |警示的嚴重性層級。 |
| LinkToSearchResults |連結至 Log Analytics 記錄檔搜尋，而該搜尋會從建立警示的查詢傳回記錄。 |
| 查詢 |已執行的查詢文字。 |
| QueryExecutionEndTime |查詢的時間範圍結尾。 |
| QueryExecutionStartTime |查詢的時間範圍開頭。 |
| ThresholdOperator | 警示規則所使用的運算子。 |
| ThresholdValue | 警示規則所使用的值。 |
| TimeGenerated |建立警示的日期和時間。 |

[警示管理解決方案](log-analytics-solution-alert-management.md)和 [Power BI 匯出](log-analytics-powerbi.md)會建立其他種類的警示記錄。  這些記錄的 **Type** 均為 **Alert**，但依其 **SourceSystem** 區分。


## <a name="next-steps"></a>後續步驟
* 使用 [OMS 入口網站](log-analytics-alerts-creating.md)建立警示規則。
* 安裝 [警示管理解決方案](log-analytics-solution-alert-management.md) ，以分析在 Log Analytics 中建立的警示以及從 System Center Operations Manager (SCOM) 收集的警示。
* 深入了解可產生警示的 [記錄檔搜尋](log-analytics-log-searches.md) 。
* 完成 [設定 Webook](log-analytics-alerts-webhooks.md) 和警示規則的逐步解說。  
* 了解如何在 [Azure 自動化中撰寫 Runbook](https://azure.microsoft.com/documentation/services/automation) 以補救警示所識別的問題。


