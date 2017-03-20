---
title: "Azure 服務匯流排診斷記錄 | Microsoft Docs"
description: "了解如何為 Azure 中的「服務匯流排」設定診斷記錄。"
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: abcb0eee979853948cf6d981ff8f3a457eeeeef0
ms.openlocfilehash: 65fe81dc90f2dc7a251860adfdd8374912cb8d73
ms.lasthandoff: 03/01/2017


---
# <a name="service-bus-diagnostic-logs"></a>服務匯流排診斷記錄

您可以檢視「Azure 服務匯流排」的兩種記錄：
* **[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**。 這些記錄包含對工作執行之操作的相關資訊。 系統一律會開啟這些記錄。
* **[診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**。 您可以設定診斷記錄，以更深入了解在工作發生的所有事件。 診斷記錄涵蓋從建立工作到刪除工作期間的活動，包括工作執行時發生的更新與活動。

## <a name="turn-on-diagnostic-logs"></a>開啟診斷記錄
診斷記錄預設為 [關閉]。 開啟診斷記錄：

1.    在 Azure 入口網站中，移至 [資料流處理工作] 刀鋒視窗。

2.    在 [監視] 底下，移至 [診斷記錄] 刀鋒視窗。

    ![瀏覽到診斷記錄的刀鋒視窗](./media/service-bus-diagnostic-logs/image1.png)  

3.    選取 [開啟診斷]。

    ![開啟診斷記錄](./media/service-bus-diagnostic-logs/image2.png)

4.    針對 [狀態]，選取 [開啟]。

    ![變更診斷記錄狀態](./media/service-bus-diagnostic-logs/image3.png)

5.    設定您要使用的封存目標，例如儲存體帳戶、事件中樞或 Azure Log Analytics。

6.    選取您要收集的記錄類別，例如 [執行] 或 [製作]。

7.    儲存新的診斷設定。

新的設定大約會在 10 分鐘內生效。 之後，記錄就會顯示在 [診斷記錄] 刀鋒視窗上已設定的封存目標中。

如需有關設定診斷的詳細資訊，請參閱 [Azure 診斷記錄檔概觀](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)。

## <a name="diagnostic-logs-schema"></a>診斷記錄結構描述

所有記錄都會以「JavaScript 物件標記法」(JSON) 格式儲存。 每個項目都具有字串欄位，這些欄位會使用下列範例所述的格式。

## <a name="operation-logs-example"></a>作業記錄範例

**OperationalLogs** 類別中的記錄會擷取「服務匯流排」作業期間發生的事項。 具體而言，這些記錄會擷取作業類型，包括建立佇列、使用的資源，以及作業狀態。

作業記錄 JSON 字串包括下表所列的元素：

名稱 | 說明
------- | -------
ActivityId | 用於追蹤的內部識別碼
EventName | 作業名稱             
resourceId | Azure Resource Manager 資源識別碼
SubscriptionId | 訂用帳戶識別碼
EventTimeString | 作業時間
EventProperties | 作業屬性
狀態 | 作業狀態
呼叫者 | 作業呼叫者 (Azure 入口網站或管理用戶端)
category | OperationalLogs

以下是作業記錄 JSON 字串的範例：

```json
Example:
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create Queue",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>後續步驟
* [服務匯流排簡介](service-bus-messaging-overview.md)
* [開始使用服務匯流排](service-bus-create-namespace-portal.md)

