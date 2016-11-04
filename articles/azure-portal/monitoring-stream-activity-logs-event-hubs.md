---
title: 將 Azure 活動記錄檔串流至事件中樞 | Microsoft Docs
description: 了解如何將 Azure 活動記錄檔串流至事件中樞。
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: johnkem

---
# 將 Azure 活動記錄檔串流至事件中樞
您可以使用入口網站中內建的「匯出」選項，或透過 Azure PowerShell Cmdlet 或 Azure CLI 來啟用記錄設定檔中服務匯流排規則識別碼的方式，迅速將 [**Azure 活動記錄檔**](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) 串流至任何應用程式。

## 您可以使用活動記錄檔與事件中樞執行的項目
此處提供一些您可以使用活動記錄檔串流功能的方法：

* **串流至協力廠商記錄與遙測系統** – 經過一段時間，事件中樞串流會成為將活動記錄檔輸送到協力廠商的 SIEM 與記錄分析解決方案的機制。
* **建置自訂遙測及記錄平台** – 如果您已有自建遙測平台或正在考慮建置一個，事件中樞所具備的高度可調整發佈訂閱特質可讓您靈活擷取活動記錄檔。[請參閱此處的 Dan Rosanova 指南，以在全球級別的遙測平台中使用事件中樞。](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## 啟用活動記錄檔的串流功能
您可以以程式控制的方式，或透過入口網站來啟用活動記錄檔的串流功能。在這兩種方式的情況下，您會選取服務匯流排命名空間 (都沒有的話即建立一個)，所選的命名空間就是事件中樞建立的位置 (如果這是第一次的串流活動記錄檔)，或串流處理的目的地 (如果您先前已將活動記錄檔串流到此命名空間)，而原則會定義串流機制擁有的權限。目前，串流到事件中樞需要**管理**、**讀取**和**傳送**權限。您可以在傳統入口網站 [設定] 索引標籤下，為您的服務匯流排命名空間建立或修改服務匯流排命名空間共用存取原則。若要更新活動記錄檔設定檔以加入串流，用戶端必須擁有服務匯流排授權規則的 ListKey 權限。

### 透過 Azure 入口網站
1. 使用入口網站左側的功能表，瀏覽至 [活動記錄檔] 刀鋒視窗。
   
    ![在入口網站中瀏覽至活動記錄檔](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 按一下刀鋒視窗頂端的 [匯出] 按鈕。
   
    ![入口網站中的匯出按鈕](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 在出現的刀鋒視窗中，您可以選取想要串流事件的區域，以及服務匯流排命名空間，以在其中建立事件中樞建立以對這些事件進行串流處理。
   
    ![匯出活動記錄檔刀鋒視窗](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. 按一下 [儲存] 來儲存這些設定。您的訂用帳戶時會立即套用設定。

### 透過 PowerShell Cmdlet
如果記錄檔設定檔已存在，您必須先移除該設定檔。

1. 使用 `Get-AzureRmLogProfile` 來識別記錄檔設定檔是否存在
2. 如果有，使用 `Remove-AzureRmLogProfile` 來進行移除。
3. 使用 `Set-AzureRmLogProfile` 來建立設定檔：

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

舉例來說，服務匯流排規則識別碼一組字串，格式如下：{service bus resource ID}/authorizationrules/{key name}

### 透過 Azure CLI
如果記錄檔設定檔已存在，您必須先移除該設定檔。

1. 使用 `azure insights logprofile list` 來識別記錄檔設定檔是否存在
2. 如果有，使用 `azure insights logprofile delete` 來進行移除。
3. 使用 `azure insights logprofile add` 來建立設定檔：

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

服務匯流排規則識別碼是此格式的字串︰`{service bus resource ID}/authorizationrules/{key name}`。

## 我要如何透過事件中樞取用記錄檔資料？
[記錄檔的結構描述可在此取得](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。每個事件位於名為「記錄」的 JSON blob 陣列中。

## 後續步驟
* [將活動記錄檔封存至儲存體帳戶](../monitoring-and-diagnostics/monitoring-archive-activity-log.md)
* [閱讀 Azure 活動記錄檔的概觀](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)
* [根據活動記錄檔事件設定警示](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)

<!---HONumber=AcomDC_0824_2016-->