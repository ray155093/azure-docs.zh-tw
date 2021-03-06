---
title: "接收 Azure 服務通知的活動記錄警示 | Microsoft Docs"
description: "在 Azure 服務發生問題時透過 SMS、電子郵件或 Webhook 獲得通知。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: c8339c08333aaa471b42f6468ca2a59ee4efacf9
ms.contentlocale: zh-tw
ms.lasthandoff: 07/18/2017

---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure 活動記錄警示的 Webhook
在定義動作群組的過程中，您可以設定 Webhook 端點以接收活動記錄警示通知。 Webhook 可讓您將這些通知路由到其他系統，以進行後置處理或自訂動作。 本文會說明 HTTP POST 至 Webhook 的承載資料樣貌。

如需建立 Azure 活動記錄警示的詳細資訊，[請改為參閱本頁](monitoring-activity-log-alerts.md)。

如需建立動作群組的詳細資訊，[請改為參閱本頁](monitoring-action-groups.md)。

## <a name="authenticating-the-webhook"></a>驗證 Webhook
Webhook 會使用權杖型授權進行驗證 - Webhook URI 會連同權杖識別碼一起儲存，例如 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>承載結構描述
POST 作業中所包含的 JSON 承載，會根據承載的 data.context.activityLog.eventSource 欄位而有所不同。

###<a name="common"></a>一般
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###<a name="administrative"></a>管理
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###<a name="servicehealth"></a>服務健康狀況
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

如需服務健康狀況通知活動記錄警示的特定結構描述詳細資料，請[按一下這裡](monitoring-service-notifications.md) 如需所有其他活動記錄警示的特定結構描述詳細資料，請[按一下這裡](monitoring-overview-activity-logs.md)

| 元素名稱 | 說明 |
| --- | --- |
| status |用於度量警示。 針對活動記錄警示，一律設為「啟動」。 |
| context |事件的內容。 |
| resourceProviderName |受影響資源的資源提供者。 |
| conditionType |一律為「事件」。 |
| 名稱 |警示規則的名稱。 |
| id |警示的資源識別碼。 |
| 說明 |警示建立期間所設定的警示說明。 |
| subscriptionId |Azure 訂用帳戶識別碼。 |
| timestamp |處理要求的 Azure 服務產生事件的時間。 |
| resourceId |受影響資源的資源識別碼。 |
| resourceGroupName |受影響資源的資源群組的名稱 |
| properties |一組包含事件相關詳細資料的`<Key, Value>`配對 (也就是 `Dictionary<String, String>`)。 |
| 事件 |包含事件相關中繼資料的元素。 |
| 授權 |事件的 RBAC 屬性。 這些屬性通常包括「動作」、「角色」和「範圍」。 |
| category |事件的類別。 支援值包括︰管理、警示、安全性、ServiceHealth、建議。 |
| 呼叫者 |已執行作業的使用者的電子郵件地址，根據可用性的 UPN 宣告或 SPN 宣告。 特定系統呼叫可為 Null。 |
| correlationId |通常是字串格式的 GUID。 具有屬於同一個較大動作的 correlationId 的事件，且通常會共用 correlationId。 |
| eventDescription |事件的靜態文字描述。 |
| eventDataId |事件的唯一識別碼。 |
| eventSource |產生事件的 Azure 服務或基礎結構的名稱。 |
| httpRequest |要求通常包括 “clientRequestId”、“clientIpAddress” 和 HTTP “method” (例如 PUT)。 |
| 層級 |下列其中一個值：「重大」、「錯誤」、「警告」、「資訊」和「詳細資訊」。 |
| operationId |通常是對應至單一作業的事件之間共用的 GUID。 |
| operationName |作業名稱。 |
| 屬性 |事件的屬性。 |
| status |字串。 作業的狀態。 常見的值包括︰「已啟動」、「進行中」、「成功」、「失敗」、「使用中」、「已解決」。 |
| 子狀態 |通常包含對應 REST 呼叫的 HTTP 狀態碼。 它也可以包含其他描述子狀態的字串。 常見子狀態的值包括：確定 (HTTP 狀態碼︰200)，已建立 (HTTP 狀態碼︰201)、接受 (HTTP 狀態碼︰202)、沒有內容 (HTTP 狀態碼︰204)、不正確的要求 (HTTP 狀態碼︰400)、找不到 (HTTP 狀態碼︰404)，衝突 (HTTP 狀態碼︰409)、內部伺服器錯誤 (HTTP 狀態碼︰500)、服務無法使用 (HTTP 狀態碼︰503)、閘道逾時 (HTTP 狀態碼︰504) |

## <a name="next-steps"></a>後續步驟
* [深入了解活動記錄](monitoring-overview-activity-logs.md)
* [對 Azure 警示執行 Azure 自動化指令碼 (Runbook)](http://go.microsoft.com/fwlink/?LinkId=627081)
* [使用邏輯應用程式透過 Twilio 從 Azure 警示傳送簡訊](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 此範例適用於度量警示，但經過修改後即可用於活動記錄警示。
* [使用邏輯應用程式從 Azure 警示傳送 Slack 訊息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 此範例適用於度量警示，但經過修改後即可用於活動記錄警示。
* [使用邏輯應用程式從 Azure 警示傳送訊息到 Azure 佇列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 此範例適用於度量警示，但經過修改後即可用於活動記錄警示。

