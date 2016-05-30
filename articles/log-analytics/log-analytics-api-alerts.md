<properties 
   pageTitle="Log Analytics 警示 REST API"
   description="Log Analytics 警示 REST API 可讓您在 Operations Management Suite (OMS) 中建立及管理警示。本文提供此 API 的詳細資料和幾個執行不同作業的範例。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/03/2016"
   ms.author="bwren" />

# Log Analytics 警示 REST API

Log Analytics 警示 REST API 可讓您在 Operations Management Suite (OMS) 中建立及管理警示。本文提供此 API 的詳細資料和幾個執行不同作業的範例。

Log Analytics 搜尋 API 是 RESTful，可透過 Azure Resource Manager REST API 來存取。在這份文件中，您可以找到透過 [ARMClient](https://github.com/projectkudu/ARMClient) 從 PowerShell 命令列存取 API 的範例，這是一個開放原始碼命令列工具，可簡化叫用 Azure Resource Manager API。使用 ARMClient 和 PowerShell 是存取 Log Analytics 搜尋 API 的許多選項之一。透過這些工具，您可以利用 RESTful Azure Resource Manager API 呼叫 OMS 工作區並在其中執行搜尋命令。API 會以 JSON 格式向您輸出搜尋結果，讓您以程式設計方式透過許多不同的方法使用搜尋結果。

## 必要條件
目前，在 Log Analytics 中只能使用已儲存的搜尋來建立警示。如需詳細資訊，請參閱[記錄檔搜尋 REST API](log-analytics-log-search-api.md)。

## 排程
一個已儲存的搜尋可以有一或多個排程。排程中定義搜尋的執行頻率及識別準則的時間間隔。排程具有下表中的屬性。

| 屬性 | 說明 |
|:--|:--|
| 間隔 | 執行搜尋的頻率。以分鐘為單位。 |
| QueryTimeSpan | 準則評估的時間間隔。必須等於或大於 Interval。以分鐘為單位。 |
| 版本 | 所使用的 API 版本。目前，這應該一律設為 1。 |

例如，假設事件查詢的 Interval 是 15 分鐘，而 Timespan 是 30 分鐘。在此例子中，每隔 15 分鐘會執行查詢，如果準則在 30 分鐘內連續評估為 true，則會觸發警示。

### 擷取排程
使用 Get 方法來擷取已儲存的搜尋的所有排程。

	armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

使用 Get 方法來擷取已儲存的搜尋的特定排程。

	armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

以下是排程的回應範例。

	{
		"id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
		"etag": "W/"datetime'2016-02-25T20%3A54%3A49.8074679Z'"",
		"properties": {
		"Interval": 15,
		"QueryTimeSpan": 15
	}

### 建立或編輯排程
使用 Put 方法並指定已儲存的搜尋特有的排程識別碼，建立新的排程。如果您使用現有的排程識別碼，將會修改此排程。當您在 OMS 主控台中建立排程時，將會建立 GUID 做為排程識別碼。

	$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### 刪除排程
使用 Delete 方法並指定排程識別碼來刪除排程。

	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## 動作
一個排程可以有多個動作。一個動作可能定義一或多個處理序來執行，例如傳送郵件或啟動 Runbook，或也可能定義臨界值來判斷搜尋結果是否符合某些準則。某些動作會同時定義這兩者，以便符合臨界值時執行處理序。
 
所有動作具有下表中的屬性。不同類型的警示有不同的其他屬性，如下所述。

| 屬性 | 說明 |
|:--|:--|
| 類型 | 動作的類型。目前可能的值為 Alert 和 Webhook。 |
| 名稱 | 警示的顯示名稱。 |
| 版本 | 所使用的 API 版本。目前，這應該一律設為 1。 |

### 擷取動作
使用 Get 方法來擷取排程的所有動作。

	armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

使用 Put 方法並指定動作識別碼，擷取排程的特定動作。

	armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### 建立或編輯動作
使用 Put 方法並指定排程特有的活動識別碼，建立新的活動。如果您使用現有的活動識別碼，將會修改此活動。當您在 OMS 主控台中建立活動時，活動識別碼會使用 GUID。

建立新活動的要求格式依活動類型而不同，下列各節提供這些範例。

### 刪除動作
使用 Delete 方法並指定動作識別碼來刪除動作。
	
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### 警示動作
一個排程應該只有一個警示動作。警示動作具有下表中的一或多個區段。以下進一步詳細說明每一個區段。

| 區段 | 說明 |
|:--|:--|
| 閾值 | 執行動作的準則。 |  
| EmailNotification | 將郵件傳送給多位收件者。 |
| 補救 | 在 Azure 自動化中啟動 Runbook，以嘗試更正識別的問題。 |

#### 臨界值
一個警示動作應該只有一個臨界值。當已儲存的搜尋結果符合與該搜尋相關聯動作中的臨界值時，將會執行該動作中的任何其他處理序。動作也可以只包含臨界值，以便與不包含臨界值的其他類型動作一起搭配使用。

臨界值具有下表中的屬性。

| 屬性 | 說明 |
|:--|:--|
| 運算子 | 用於比較臨界值的運算子。<br> gt = 大於 <br> lt = 小於 |
| 值 | 臨界值。 |

例如，假設事件查詢的間隔是 15 分鐘、時間範圍是 30 分鐘，而臨界值大於 10。在此例子中，每隔 15 分鐘會執行查詢，如果傳回在 30 分鐘內建立的 10 個事件，則會觸發警示。

以下是一個只包含臨界值的動作的回應範例。

	"etag": "W/"datetime'2016-02-25T20%3A54%3A20.1302566Z'"",
	"properties": {
		"Type": "Alert",
		"Name": "My threshold action",
		"Threshold": {
			"Operator": "gt",
			"Value": 10
		},
    	"Version": 1
	}

使用 Put 方法並指定動作識別碼，建立排程的新臨界值動作。如果對動作使用新的識別碼，將會建立新的動作。如果您使用排程已有的識別碼，將會修改此動作。

	$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### 電子郵件通知
「電子郵件通知」會將郵件傳送給一或多位收件者。它們包含下表中的屬性。

| 屬性 | 說明 |
|:--|:--|
| 收件者 | 郵件地址清單。 |
| 主旨 | 郵件的主旨。 |
| 附件 | 目前不支援附件，這個值永遠為 “None”。 |

以下是一個包含臨界值的電子郵件通知動作的回應範例。

	"etag": "W/"datetime'2016-02-25T20%3A54%3A20.1302566Z'"",
	"properties": {
		"Type": "Alert",
		"Name": "My email action",
		"Threshold": {
			"Operator": "gt",
			"Value": 10
		},
		"EmailNotification": {
			"Recipients": [
    			"recipient1@contoso.com",
		    	"recipient2@contoso.com"
			],
			"Subject": "This is the subject",
			"Attachment": "None"
		},
    	"Version": 1
	}

使用 Put 方法並指定動作識別碼，建立排程的新電子郵件動作。如果對動作使用新的識別碼，將會建立新的動作。如果您使用排程已有的識別碼，將會修改此動作。

下列範例建立一個包含臨界值的電子郵件通知，當已儲存的搜尋結果超過臨界值時，就會傳送郵件。

	$emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### 補救動作
「補救」會在 Azure 自動化中啟動 Runbook，嘗試更正警示所識別的問題。您必須為補救動作中使用的 Runbook 建立 webhook，然後在 WebhookUri 屬性中指定 URI。當您使用 OMS 主控台建立此動作時，將會自動為 Runbook 建立新的 webhook。

「補救」包含下表中的屬性。

| 屬性 | 說明 |
|:--|:--|
| RunbookName | Runbook 的名稱。這必須符合自動化帳戶 (在 OMS 工作區的自動化方案中設定) 中發佈的 Runbook。 |
| WebhookUri | Webhook 的 URI。
| Expiry | webhook 的到期日期和時間。如果 webhook 沒有到期日，這可以是任何有效的未來日期。 |

以下是一個包含臨界值的補救動作的回應範例。

	"etag": "W/"datetime'2016-02-25T20%3A54%3A20.1302566Z'"",
	"properties": {
		"Type": "Alert",
		"Name": "My remediation action",
		"Threshold": {
			"Operator": "gt",
			"Value": 10
		},
		"Remediation": {
			"RunbookName": "My-Runbook",
			"WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
			"Expiry": "2018-02-25T18:27:20"
			},
		"Version": 1
	}

使用 Put 方法並指定動作識別碼，建立排程的新補救動作。如果對動作使用新的識別碼，將會建立新的動作。如果您使用排程已有的識別碼，將會修改此動作。

下列範例建立一個包含臨界值的補救，當已儲存的搜尋結果超過臨界值時，就會啟動 Runbook。

	$remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### 範例
以下是建立新電子郵件警示的完整範例。這會建立新的排程及一個包含臨界值和電子郵件的動作。

	$subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
	$workspaceId    = "MyWorkspace"
	$searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

	$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
	armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

	$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
	armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

	$emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
	armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### Webhook 動作
Webhook 動作會呼叫 URL 並選擇性地提供要傳送的承載，以啟動處理序。這些動作類似於「補救」動作，不同之處在於它們用於可能叫用 Azure 自動化 Runbook 以外之處理序的 webhook。它們還提供另一個選項，可指定要傳遞到遠端處理序的承載。

Webhook 動作沒有臨界值，應該加入至具有警示動作和臨界值的排程。您可以加入多個 Webhook 動作，在符合臨界值時全部執行。

Webhook 動作包含下表中的屬性。

| 屬性 | 說明 |
|:--|:--|
| WebhookUri | 郵件的主旨。 |
| CustomPayload | 要傳送至 webhook 的自訂內容。格式取決於 webhook 需要的內容。 |

以下是 webhook 動作及一個包含臨界值的相關聯警示動作的回應範例。

	{
		"__metadata": {},
		"value": [
			{
				"id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
				"etag": "W/"datetime'2016-02-26T20%3A25%3A00.6862124Z'"",
				"properties": {
					"Type": "Webhook",
					"Name": "My Webhook Action",
					"WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
					"CustomPayload": "{"fielld1":"value1","field2":"value2"}",
					"Version": 1
				}
			},
			{
				"id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
				"etag": "W/"datetime'2016-02-26T20%3A25%3A00.565204Z'"",
				"properties": {
					"Type": "Alert",
					"Name": "Threshold for my webhook action",
					"Threshold": {
						"Operator": "gt",
						"Value": 10
					},
					"Version": 1
				}
			}
		]
	}

#### 建立或編輯 webhook 動作
使用 Put 方法並指定動作識別碼，建立排程的新 webhook 動作。如果對動作使用新的識別碼，將會建立新的動作。如果您使用排程已有的識別碼，將會修改此動作。下列範例建立 Webhook 動作及一個包含臨界值的警示動作，當已儲存的搜尋結果超過臨界值時，就會啟動 webhook。

	$thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction
	
	$webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{"field1":"value1","field2":"value2"}', 'Version': 1 }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## 後續步驟

- 在 Log Analytics 中使用 [REST API 執行記錄檔搜尋](log-analytics-log-search-api.md)。
 

<!---HONumber=AcomDC_0518_2016-->