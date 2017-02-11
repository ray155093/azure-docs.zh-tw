---
title: "將 Azure 診斷記錄檔串流至事件中樞 | Microsoft Docs"
description: "了解如何將 Azure 診斷記錄檔串流至事件中樞。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4ff5fb57cba6dea1bee9d2e2d25f6fcf8354ce79


---
# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>將 Azure 診斷記錄檔串流至事件中樞
您可以使用入口網站中內建的「匯出至事件中樞」選項，或透過 Azure PowerShell Cmdlet 或 Azure CLI 來啟用診斷設定中服務匯流排規則識別碼的方式，以近乎即時的速度將 **[Azure 診斷記錄檔](monitoring-overview-of-diagnostic-logs.md)**串流至任何應用程式。

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>您可以使用診斷記錄和事件中樞執行的項目
這裡有一些您可以使用診斷日誌串流功能的方法：

* **串流至協力廠商記錄與遙測系統** – 經過一段時間，事件中樞串流會成為將活動記錄檔輸送到協力廠商的 SIEM 與記錄分析解決方案的機制。
* **透過將「最忙碌路徑」串流至 PowerBI 以檢視服務健全狀況** – 您可以使用事件中樞、串流分析和 PowerBI，輕鬆快速地將診斷資料轉換為 Azure 服務上的深入解析。 [此文件文章提供絕佳概觀，說明如何設定事件中樞、使用串流分析處理資料，以及使用 PowerBI 作為輸出](../stream-analytics/stream-analytics-power-bi-dashboard.md)。 以下是設定診斷記錄的一些秘訣︰
  
  * 當您勾選入口網站中的選項，或透過 PowerShell 進行啟用時，即會自動建立診斷記錄類別的事件中樞，因此此您需要選取服務匯流排命名空間中名稱開頭為 “insights-” 的事件中樞
  * 此處是您可以使用的範例串流分析查詢，以輕鬆將記錄資料剖析至 PowerBI 表格：

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

* **建置自訂遙測及記錄平台** – 如果您已有自建遙測平台或正好在考慮建置一個，事件中樞所具備的高度可調整的發佈訂閱特質可讓您靈活擷取診斷記錄檔。 [請參閱此處的 Dan Rosanova 指南，以在全球級別的遙測平台中使用事件中樞](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="enable-streaming-of-diagnostic-logs"></a>啟用診斷記錄串流功能
您可以透過入口網站，或使用 [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)，來啟用以程式控制的方式對診斷記錄進行串流的功能。 在這兩種方式的情況下，您會選取服務匯流排命名空間，且針對您所啟用的每個記錄檔分類，在命名空間中建立事件中樞。 診斷 **記錄檔分類** 是一種資源可以收集的記錄類型。 您可以在 Azure 入口網站的 [診斷] 刀鋒視窗中選取您想要收集的特定資源的記錄檔分類。

![入口網站中的記錄檔類別](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [!WARNING]
> 啟用和串流來自計算資源 (例如，VM 或 Service Fabric) 的診斷記錄 [需要一組不同的步驟](../event-hubs/event-hubs-streaming-azure-diags-data.md)。
> 
> 

### <a name="via-powershell-cmdlets"></a>透過 PowerShell Cmdlet
若要透過 [Azure PowerShell Cmdlet](insights-powershell-samples.md) 啟用串流功能，您可以使用 `Set-AzureRmDiagnosticSetting` Cmdlet 搭配下列參數︰

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

服務匯流排規則識別碼是此格式的字串︰`{service bus resource ID}/authorizationrules/{key name}`，例如，`/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`。

### <a name="via-azure-cli"></a>透過 Azure CLI
若要透過 [Azure CLI](insights-cli-samples.md) 啟用串流功能，您可以使用如下的 `insights diagnostic set` 命令︰

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

如 PowerShell Cmdlet 所述，對服務匯流排規則識別碼使用相同的格式。

### <a name="via-azure-portal"></a>透過 Azure 入口網站
若要透過 Azure 入口網站啟用串流功能，瀏覽至資源的診斷設定並選取 [匯出至事件中樞]。

![在入口網站中匯出至事件中樞](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

若要進行設定，請選取現有的服務匯流排命名空間。 選取的命名空間將會是事件中樞的建立所在 (如果這是您第一次的串流診斷記錄) 或串流處理的目的地 (如果已存在將該記錄檔分類串流至此命名空間的資源)，而原則會定義串流機制擁有的權限。 目前，串流到事件中樞需要 管理、閱讀和傳送權限。 您可以在傳統入口網站 [設定] 索引標籤下，為您的服務匯流排命名空間建立或修改服務匯流排命名空間共用存取原則。 若要更新其中一個診斷設定，用戶端必須擁有服務匯流排授權規則的 ListKey 權限。

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>我要如何透過事件中樞取用記錄檔資料？
此處是來自事件中樞的範例輸出資料：

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| 元素名稱 | 說明 |
| --- | --- |
| 記錄數 |此承載中的所有記錄事件的陣列。 |
| 分析 |事件發生的時間。 |
| category |此事件的記錄檔分類。 |
| resourceId |產生此事件之資源的資源識別碼。 |
| operationName |作業名稱。 |
| 層級 |選用。 表示記錄事件層級。 |
| properties |事件的屬性。 |

您可以在 [這裡](monitoring-overview-of-diagnostic-logs.md)檢視支援串流至事件中樞的所有資源提供者清單。

## <a name="next-steps"></a>後續步驟
* [深入了解 Azure 診斷記錄檔](monitoring-overview-of-diagnostic-logs.md)
* [開始使用事件中心](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)




<!--HONumber=Nov16_HO3-->


