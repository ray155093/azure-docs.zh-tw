<properties
	pageTitle="Log Analytics 記錄搜尋 API | Microsoft Azure"
	description="本指南提供基本教學課程，說明可如何使用 Operations Management Suite (OMS) 中的 Log Analytics 搜尋 API，並提供使用命令的範例。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# Log Analytics 記錄搜尋 API

本指南提供基本教學課程，說明如何使用 Operations Management Suite (OMS) 中的 Log Analytics 搜尋 API，並提供使用命令的範例。本文中的部分範例參考 Operational Insights，這是舊版 Log Analytics 的名稱。

## Log Search API 概觀

Log Analytics 搜尋 API 是一種 RESTful API，並可透過 Azure Resource Manager API 存取。在這份文件中，您會發現可在其中透過 [ARMClient](https://github.com/projectkudu/ARMClient) 存取 API 的範例，以及簡化叫用 Azure 資源管理員 API 的開放原始碼命令列工具。使用 ARMClient 和 PowerShell 是存取 Log Analytics 搜尋 API 的許多選項之一。透過這些工具，您可以利用 RESTful Azure Resource Manager API 呼叫 OMS 工作區並在其中執行搜尋命令。API 會以 JSON 格式向您輸出搜尋結果，讓您以程式設計方式透過許多不同的方法使用搜尋結果。

Azure 資源管理員可透過 [Library for.NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) 以及 [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx) 使用。檢閱按讚的網頁以深入了解。

## 基本 Log Analytics 搜尋 API 教學課程

### 使用 ARM 用戶端

1. 安裝 [Chocolatey](https://chocolatey.org/)，也就是 Windows 的開放原始碼機器封裝管理員。以系統管理員身分開啟命令提示字元視窗，然後執行下列命令：

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. 執行下列命令來安裝 ARMClient：

    ```
    choco install armclient
    ```

### 使用 ARMClient 執行簡單搜尋

1. 登入 Microsoft 或 OrgID 帳戶：

    ```
    armclient login
    ```

    成功的登入會列出繫結至指定帳戶的所有訂用帳戶。例如：

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. 取得 Operations Management Suite 工作區。例如：

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    成功的 Get 呼叫會輸出繫結至訂用帳戶的所有工作區。例如：

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. 建立您的搜尋變數。例如：

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. 使用新的搜尋變數搜尋。例如：

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## Log Analytics 搜尋 API 參考範例
下列範例顯示如何使用 Search API。

### 搜尋 - 動作/讀取

**範例 Url：**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**要求：**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
下表描述可用的屬性。

|**屬性**|**說明**|
|---|---|
|top|要傳回的結果數目上限。|
|highlight|包含 pre 和 post 參數，通常用於反白顯示比對欄位|
|pre|以指定字串做為比對欄位的前置詞。|
|post|以指定字串附加至比對欄位之後。|
|query|用來收集並傳回結果的搜尋查詢。|
|start|您想要從其中找到結果的時間範圍開頭。|
|end|您想要從其中找到結果的時間範圍結尾。|


**回應：**

```
	{
	  "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	  "__metadata" : {
	    "resultType" : "raw",
	    "total" : 1455,
	    "top" : 150,
	    "StartTime" : "2015-02-11T21:09:07.0345815Z",
	    "Status" : "Successful",
	    "LastUpdated" : "2015-02-11T21:09:07.331463Z",
	    "CoreResponses" : [],
	    "sort" : [{
	      "name" : "TimeGenerated",
	      "order" : "desc"
	    }],
	    "requestTime" : 450
	  },
	  "value": [{
	    "SourceSystem" : "OpsManager",
	    "TimeGenerated" : "2015-02-07T14:07:33Z",
	    "Source" : "SideBySide",
	    "EventLog" : "Application",
	    "Computer" : "BAMBAM",
	    "EventCategory" : 0,
	    "EventLevel" : 1,
	    "EventLevelName" : "Error",
	    "UserName" : "N/A",
	    "EventID" : 78,
	    "MG" : "00000000-0000-0000-0000-000000000001",
	    "TimeCollected" : "2015-02-07T14:10:04.69Z",
	    "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
	    "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	    "Type" : "Event",
	    "__metadata" : {
	      "Type" : "Event",
	      "TimeGenerated" : "2015-02-07T14:07:33Z",
	      "highlighting" : {
	      "EventLevelName" : ["{[hl]}Error{[/hl]}"]
	    }
	  }]
	],
	        "start" : "2015-02-04T21:03:29.231Z",
	        "end" : "2015-02-11T21:03:29.231Z"
	      }
	    }
	  }]
	}
```

### 搜尋/{ID} - 動作/讀取

**要求已儲存搜尋的內容：**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] 如果搜尋會傳回「擱置中」狀態，則輪詢更新的結果可以透過此 API 完成。6 分鐘後，搜尋的結果將會從快取卸除，並將傳回 Http Gone。如果初始搜尋要求立即傳回「成功」狀態，它就不會加入至快取，使 API在被查詢時傳回 Http Gone。Http 200 結果內容的格式將會和更新值相同，都是初始搜尋要求。

### 已儲存的搜尋 - 僅限於 REST

**已儲存搜尋的要求清單：**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

支援的方法：GET PUT DELETE

支援的收集方法：GET

下表描述可用的屬性。

|屬性|說明|
|---|---|
|識別碼|唯一識別碼。|
|Etag|**修補程式的必要項目**。每次寫入時由伺服器進行更新。值必須等於目前儲存的值或 ‘*’ 才能進行更新。舊值/無效值時會傳回 409。|
|properties.query|**必要**。搜尋查詢。|
|properties.displayName|**必要**。使用者定義的查詢顯示名稱。如果模型化為 Azure 資源，則這會是標記。|
|properties.category|**必要**。使用者定義的查詢類別。如果模型化為 Azure 資源，則這會是標記。|

>[AZURE.NOTE] 目前，當 Log Analytics 搜尋 API 輪詢工作區的儲存搜尋時，其會傳回使用者建立的儲存搜尋。API 不會傳回目前解決方案所提供的儲存搜尋。這項功能將會在之後加入。

### 刪除儲存搜尋

**要求：**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### 更新儲存搜尋

 **要求：**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### 中繼資料 - 僅限 JSON

以下的方法可讓您看到工作區中所收集資料的所有記錄類型欄位。例如，如果您想知道事件類型是否具有名為「電腦」的欄位，這會是一個查詢和確認的方法。

**欄位要求：**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**回應：**

```
	{
	  "__metadata" : {
	    "schema" : {
	      "name" : "Example Name",
	      "version" : 2
	    },
	    "resultType" : "schema",
	    "requestTime" : 35
	  },
	  "value" : [{
	      "name" : "MG",
	      "displayName" : "MG",
	      "type" : "Guid",
	      "facetable" : true,
	      "display" : false,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }, {
	      "name" : "ManagementGroupName",
	      "displayName" : "ManagementGroupName",
	      "type" : "String",
	      "facetable" : true,
	      "display" : true,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }
	  ]
	}
```

下表描述可用的屬性。

|**屬性**|**說明**|
|---|---|
|名稱|欄位名稱。|
|displayName|欄位的顯示名稱。|
|類型|欄位值的類型。|
|可面向化|目前 ‘indexed’、‘stored ‘和 ‘facet’ 屬性的組合。|
|顯示|目前 ‘display’ 屬性。如果欄位顯示在搜尋中，則為 True。|
|ownerType|減少至僅屬於 onboarded IP 的類型。|


## 選擇性參數
下列資訊描述可用的選擇性參數。

### 醒目提示

“Highlight” 參數是一種選擇性參數，您可以用來要求搜尋子系統，包含其回應中的一組標記。

這些標記表示開頭與結尾的醒目提示文字，以符合您在搜尋查詢中所提供的詞彙。您可以指定開頭與結尾的標記，讓搜尋用來包裝醒目提示的詞彙。

**範例搜尋查詢**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**範例結果：**

```
	{
	    "TimeGenerated":
	    "2015-05-18T23:55:59Z", "Source":
	    "EventLog": "Application",
	    "Computer": "smokedturkey.net",
	    "EventCategory": 0,
	    "EventLevel":1,
	    "EventLevelName":
	    "Error"
	    "Manager ", "__metadata":
	    {
	        "Type": "Event",
	        "TimeGenerated": "2015-05-18T23:55:59Z",
	        "highlighting": {
	            "EventLevelName":
	            ["{[hl]}Error{[/hl]}"]
	        }
	    }
	}
```

請注意，上述結果包含已具有前置詞及附加詞的錯誤訊息。

## 電腦群組

電腦群組是一種特殊的已儲存搜尋，其會傳回一組電腦。您可以在其他查詢中使用電腦群組，以將結果限制為該群組中的電腦。電腦群組會實作為已儲存的搜尋，其含有 Group 標籤與 Computer 值。

以下是電腦群組的回應範例。

	"etag": "W/"datetime'2016-04-01T13%3A38%3A04.7763203Z'"",
	"properties": {
		"Category": "My Computer Groups",
		"DisplayName": "My Computer Group",
		"Query": "srv* | Distinct Computer",
		"Tags": [{
        	"Name": "Group",
        	"Value": "Computer"
      	}],
    "Version": 1
	}

### 擷取電腦群組

使用 Get 方法與群組識別碼，擷取電腦群組。

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### 建立或更新電腦群組

使用 Put 方法與單一的已儲存搜尋識別碼，建立新的電腦群組。如果您使用現有的電腦群組識別碼，則將會修改它。當您在 OMS 主控台中建立電腦群組時，即會依據群組與名稱來建立識別碼。

用於群組定義的查詢必須傳回一組電腦，群組才能正確運作。建議您使用 *| Distinct Computer* 結束您的查詢，以確保傳回正確的資料。

已儲存搜尋的定義必須包含 Group 標籤與 Computer 值，才能將搜尋分類為電腦群組。

	$etag=get-date -f yyyy-MM-ddThh:mm:ss.msZ
	$groupName="My Computer Group"
	$groupQuery = "Computer=srv* | Distinct Computer"
	$groupCategory="My Computer Groups"
	$groupID = "My Computer Groups | My Computer Group"

	$groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

	armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### 刪除電腦群組

使用 Delete 方法與群組識別碼，刪除電腦群組。

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## 後續步驟

- 了解[記錄搜尋](log-analytics-log-searches.md)，以使用自訂欄位作為準則來建立查詢。

<!---HONumber=AcomDC_0504_2016-->