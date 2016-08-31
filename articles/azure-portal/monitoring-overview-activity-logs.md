<properties
	pageTitle="Azure 活動記錄檔概觀 | Microsoft Azure"
	description="認識 Azure 活動記錄檔，並了解如何使用它來了解您的 Azure 訂用帳戶內發生的事件。"
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Azure 活動記錄檔概觀
**Azure 活動記錄檔** (Activity Log) 能為您提供訂用帳戶中的資源所執行之作業的深入解析。活動記錄檔之前叫做「稽核記錄」或「作業記錄」，因為它會報告訂用帳戶中控制層面的事件。您可以使用活動記錄檔來判斷訂用帳戶中的資源上執行的所有寫入作業 (PUT、POST、DELETE) 的人、事、時，了解作業狀態和其他相關屬性。活動記錄檔不包含讀取作業 (GET)。

活動記錄檔不同於[診斷記錄](./monitoring-overview-of-diagnostic-logs.md)，是由資源發出的所有記錄檔。這些記錄檔提供有關該資源的作業資料，而不是該資源上的作業。

您可以使用 Azure 入口網站、CLI、PowerShell Cmdlet 、Insights REST API 從活動記錄檔擷取事件。

## Azure 活動記錄檔的用途
以下是您可以利用活動記錄檔進行的事：

- 在 **Azure 入口網站**中查詢和檢視活動記錄檔。
- 透過 REST API、PowerShell Cmdlet 或 CLI 查詢活動記錄檔。
- [建立電子郵件或可觸發關閉活動記錄檔事件的 webhook 警示。](./insights-auditlog-to-webhook-email.md)
- 將活動記錄檔儲存到**儲存體帳戶**以利封存或手動檢查。您可以使用**記錄檔設定檔**指定保留時間 (以天為單位)。
- 使用 [**PowerBI 內容套件**](https://powerbi.microsoft.com/zh-TW/documentation/powerbi-content-pack-azure-audit-logs/)在 PowerBI 中分析活動記錄檔。
- [將活動記錄檔串流至**事件中樞**](./monitoring-stream-activity-logs-event-hubs.md)，以利第三方服務或自訂的分析解決方案 (如 PowerBI) 擷取。

## 匯出活動記錄檔與記錄檔設定檔
**記錄檔設定檔**控制活動記錄檔的匯出方式。使用記錄檔設定檔，您可以設定︰

- 活動記錄檔應該要傳送至何處 (儲存體帳戶或事件中樞)
- 應該要傳送何種事件分類 (例如 Write、Delete、Action)
- 應該要匯出哪一個區域 (Locations)
- 活動記錄檔應該在儲存體帳戶中保留多久 – 保留期零天表示要永遠保留記錄檔。如果有設定保留原則，但將儲存體帳戶的記錄檔儲存停用 (例如，如果只選取事件中樞或 OMS 選項)，保留原則不會有任何作用。

這些設定可透過入口網站中 [活動記錄檔] 刀鋒視窗中的 [匯出] 選項來設定，或[使用 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell Cmdlet 或 CLI 以程式設計方式設定。一個訂用帳戶只能有一個記錄檔的設定檔。

### 使用 Azure 入口網站設定記錄檔設定檔
您可以將活動記錄檔串流至事件中樞，或在 Azure 入口網站中使用 [匯出] 選項將它們儲存在儲存體帳戶。

1. 使用入口網站左側的功能表，瀏覽至 [活動記錄檔] 刀鋒視窗。

    ![在入口網站中瀏覽至活動記錄檔](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 按一下刀鋒視窗頂端的 [匯出] 按鈕。

    ![入口網站中的匯出按鈕](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 在出現的刀鋒視窗中，您可以選擇您要匯出事件的區域、您想要儲存事件的儲存體帳戶 (以及您想要在儲存體中保留這些事件的天數)、您想要在哪一個服務匯流排命名空間中建立事件中樞以便進行這些事件的串流。

    ![匯出活動記錄檔刀鋒視窗](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. 按一下 [儲存] 來儲存這些設定。您的訂用帳戶時會立即套用設定。

### 使用 Azure PowerShell Cmdlet 設定記錄檔設定檔
#### 取得現有的記錄檔設定檔
```
Get-AzureRmLogProfile
```

#### 新增記錄檔設定檔
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| 屬性 | 必要 | 說明 |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name | 是 | 記錄檔設定檔的名稱。 |
| StorageAccountId | 否 | 資源識別碼，活動記錄檔應該要儲存至此儲存體帳戶。 |
| serviceBusRuleId | 否 | 服務匯流排規則識別碼，您想要在其中建立事件中樞的服務匯流排命名空間。將會是此格式的字串︰`{service bus resource ID}/authorizationrules/{key name}`。 |
| 位置 | 是 | 以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 |
| RetentionInDays | 是 | 事件應保留的天數。值為 0 會無限期地儲存記錄檔。 |
| 類別 | 否 | 以逗號分隔的類別清單，其中列出應該收集的事件類別。可能的值有 Write、Delete、Action。 |

#### 移除記錄檔設定檔
```
Remove-AzureRmLogProfile -name my_log_profile
```

### 使用 Azure 跨平台 CLI 設定記錄檔設定檔
#### 取得現有的記錄檔設定檔
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
`name` 屬性應該是您的記錄檔設定檔的名稱。

#### 新增記錄檔設定檔
``` 
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| 屬性 | 必要 | 說明 |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 名稱 | 是 | 記錄檔設定檔的名稱。 |
| storageId | 否 | 資源識別碼，活動記錄檔應該要儲存至此儲存體帳戶。 |
| serviceBusRuleId | 否 | 服務匯流排規則識別碼，您想要在其中建立事件中樞的服務匯流排命名空間。將會是此格式的字串︰`{service bus resource ID}/authorizationrules/{key name}`。 |
| 位置 | 是 | 以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 |
| retentionInDays | 是 | 事件應保留的天數。值為 0 會無限期地儲存記錄檔。 |
| categories | 否 | 以逗號分隔的類別清單，其中列出應該收集的事件類別。可能的值有 Write、Delete、Action。 |

#### 移除記錄檔設定檔
```
azure insights logprofile delete --name my_log_profile
```

## 結構描述
活動記錄檔中的每個事件都有像這樣的 JSON blob︰

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| 元素名稱 | 說明 |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 授權 | 事件的 RBAC 屬性的 blob。通常包括 action、role 和 scope 屬性。 |
| 呼叫者 | 已執行作業的使用者的電子郵件地址，根據可用性的 UPN 宣告或 SPN 宣告。 |
| 通道 | 為下列其中一個值：Admin、Operation |
| correlationId | 通常是字串格式的 GUID。具有相同 correlationId、屬於同一 uber 動作的事件。 |
| 說明 | 事件的靜態文字描述。 |
| eventDataId | 事件的唯一識別碼。 |
| eventSource | 產生此事件的 Azure 服務或基礎結構的名稱。 |
| httpRequest | 描述 HTTP 要求的 blob。通常包括 “clientRequestId”、“clientIpAddress” 和 “method” (HTTP 方法，例如 PUT)。 |
| 層級 | 事件的層級。下列其中一個值：重大、錯誤、警告、資訊和詳細資訊 |
| resourceGroupName | 受影響資源的資源群組的名稱。 |
| resourceProviderName | 受影響資源的資源提供者的名稱。 |
| resourceUri | 受影響資源的資源識別碼。 |
| operationId | 對應至單一作業的事件共用的 GUID。 |
| operationName | 作業名稱。 |
| properties | 描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |
| status | 字串，描述作業的狀態。常見的值包括︰Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| 子狀態 | 通常包含對應 REST 呼叫的 HTTP 狀態碼，但也可以包含其他描述子狀態的字串，常見的值包括：確定 (HTTP 狀態碼︰200)，已建立 (HTTP 狀態碼︰201)、接受 (HTTP 狀態碼︰202)、沒有內容 (HTTP 狀態碼︰204)、不正確的要求 (HTTP 狀態碼︰400)、找不到 (HTTP 狀態碼︰404)，衝突 (HTTP 狀態碼︰409)、內部伺服器錯誤 (HTTP 狀態碼︰500)、服務無法使用 (HTTP 狀態碼︰503)、閘道逾時 (HTTP 狀態碼︰504)。 |
| eventTimestamp | 處理與事件對應之要求的Azure 服務產生事件時的時間戳記。 |
| submissionTimestamp | 當事件變成可供查詢時的時間戳記。 |
| subscriptionId | Azure 訂用帳戶識別碼。 |
| nextLink | 結果被分成多個回應時，用於提取下一組結果的接續權杖。通常是超過 200 個記錄的情況。 |

## 後續步驟
- [深入了解活動記錄檔 (之前的稽核記錄檔)](../resource-group-audit.md)
- [將 Azure 活動記錄檔串流至事件中樞](./monitoring-stream-activity-logs-event-hubs.md)

<!---HONumber=AcomDC_0817_2016-->