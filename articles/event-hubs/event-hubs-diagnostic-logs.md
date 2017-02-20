---
title: "Azure 事件中樞診斷記錄 | Microsoft Docs"
description: "了解如何在 Microsoft Azure 事件中樞中分析診斷記錄。"
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 4d7d0e1f5ffb395bf91bbdac1ab4b9ec3f9dee1c
ms.openlocfilehash: cb8c7930ee423543c91366de64220ee55609a4cf


---
# <a name="event-hub-diagnostic-logs"></a>事件中樞診斷記錄

## <a name="introduction"></a>簡介
事件中樞公開兩種類型的記錄： 
* [活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)，一律會啟用，並提供作業上所執行之作業的深入資訊；
* [診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)，可供使用者設定，並提供豐富的作業深入資訊，包含從建立、更新、執行，直到刪除作業之間所發生任何事件；

## <a name="how-to-enable-diagnostic-logs"></a>如何啟用診斷記錄
診斷記錄預設為 [關閉]。 若要啟用，請依照下列步驟執行：

登入 Azure 入口網站並瀏覽到 [串流作業] 刀鋒視窗，然後使用 [監視] 底下的 [診斷記錄] 刀鋒視窗。

![瀏覽到診斷記錄的刀鋒視窗](./media/event-hubs-diagnostic-logs/image1.png)  

然後按一下 [開啟診斷] 連結

![開啟診斷記錄](./media/event-hubs-diagnostic-logs/image2.png)

在開啟的診斷中，將狀態變更為 [開啟]。

![變更診斷記錄狀態](./media/event-hubs-diagnostic-logs/image3.png)

設定所需的封存目標 (儲存體帳戶、事件中樞、Log Analytics)，並選取要收集的記錄類別 (執行、製作)。 然後儲存新的診斷組態。

儲存組態之後，需要約 10 分鐘才會生效，然後記錄會開始顯示在設定的封存目標中。您可以在 [診斷記錄] 刀鋒視窗中查看：

設定診斷的詳細資訊可在 [診斷記錄][](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 頁面取得。

## <a name="diagnostic-logs-categories"></a>診斷記錄類別
目前可擷取的診斷記錄類別有兩種：

* **ArchivalLogs：**擷取與事件中樞封存相關的記錄 - 特別是與封存錯誤相關的記錄。
* **OperationalLogs：**擷取事件中樞作業期間發生的動作 - 特別是作業類型 (例如事件中樞建立)、使用的資源，以及作業狀態。

## <a name="diagnostic-logs-schema"></a>診斷記錄結構描述
所有記錄檔都以 JSON 格式儲存，且每個項目都有遵循下列格式的字串欄位：


### <a name="archive-error-schema"></a>封存錯誤結構描述
名稱 | 說明
------- | -------
TaskName | 失敗的作業描述
ActivityId | 用於追蹤目的的內部識別碼
trackingId | 用於追蹤目的的內部識別碼
resourceId | ARM 資源識別碼
eventHub | 事件中樞完整名稱 (包括命名空間名稱)
partitionId | 要寫入至事件中樞內的資料分割
archiveStep | ArchiveFlushWriter
startTime | 失敗開始時間
failures | 發生失敗的次數
durationInSeconds | 失敗持續時間
Message | 錯誤訊息
category | ArchiveLogs

#### <a name="example-archive-log"></a>封存記錄檔範例

```json
{
     "TaskName": "EventHubArchiveUserError",
     "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
     "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
     "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
     "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
     "partitionId": "1",
     "archiveStep": "ArchiveFlushWriter",
     "startTime": "9/22/2016 5:11:21 AM",
     "failures": 3,
     "durationInSeconds": 360,
     "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
     "category": "ArchiveLogs"
}
```

### <a name="operation-logs-schema"></a>作業記錄檔結構描述
名稱 | 說明
------- | -------
ActivityId | 用於追蹤目的的內部識別碼
EventName | 作業名稱           
resourceId | ARM 資源識別碼
SubscriptionId | 訂用帳戶識別碼
EventTimeString | 作業時間
EventProperties | 作業屬性
狀態 | 作業狀態
呼叫者 | 作業呼叫端 (入口網站或管理用戶端)
category | OperationalLogs

#### <a name="example-operation-log"></a>作業記錄檔範例

```json
Example: 
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create EventHub",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>後續步驟
* [事件中樞簡介](event-hubs-what-is-event-hubs.md)
* [事件中樞 API 概觀](event-hubs-api-overview.md)
* [開始使用事件中樞](event-hubs-csharp-ephcs-getstarted.md)


<!--HONumber=Feb17_HO1-->


