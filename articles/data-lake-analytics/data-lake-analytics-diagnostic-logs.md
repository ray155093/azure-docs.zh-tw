<properties 
   pageTitle="檢視 Azure Data Lake Analytics 的診斷記錄 | Microsoft Azure" 
   description="了解如何設定及存取 Azure Data Lake Analytics 的診斷記錄 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/10/2016"
   ms.author="larryfr"/>

# 存取 Azure Data Lake Analytics 的診斷記錄

了解如何啟用 Data Lake Analytics 帳戶的診斷記錄，以及如何檢視針對帳戶收集的記錄。

組織可以啟用其 Azure Data Lake Analytics 帳戶的診斷記錄以收集資料存取稽核線索。這些記錄檔可提供如下資訊︰

* 資料的存取使用者清單。
* 資料的存取頻率。
* 帳戶中儲存的資料量。

## 必要條件

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以使用 Data Lake Analytics 公開預覽版。請參閱[指示](data-lake-analytics-get-started-portal.md#signup)。
- **Azure Data Lake Analytics 帳戶**。遵循[使用 Azure 入口網站開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) 的指示。

## 啟用 Data Lake Analytics 帳戶的診斷記錄

1. 登入新的 [Azure 入口網站](https://portal.azure.com)。

2. 開啟 Data Lake Analytics 帳戶，接著在 Data Lake Analytics 帳戶刀鋒視窗中依序按一下 [設定] 和 [診斷設定]。

3. 在 [診斷] 刀鋒視窗中，變更下列項目以設定診斷記錄。

	![啟用診斷記錄](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "啟用診斷記錄")

	* 將 [狀態] 設定為 [開啟] 以啟用診斷記錄。
	* 您可以選擇兩種不同的資料儲存/處理方法。
		* 選取 [匯出到事件中樞] 可將記錄資料串流到 Azure 事件中樞。如果您有即時分析內送記錄的下游處理管線，請使用此選項。如果您選取此選項，必須提供要使用的 Azure 事件中樞詳細資料。
		* 選取 [匯出到儲存體帳戶] 可將記錄儲存到 Azure 儲存體帳戶。如果您想要封存資料，請使用此選項。如果您選取此選項，必須提供用來儲存記錄的 Azure 儲存體帳戶。
	* 指定要取得稽核記錄、要求記錄或兩者。
	* 指定的資料的保留天數。
	* 按一下 [儲存]。

一旦您啟用了診斷設定，即可在 [診斷記錄] 索引標籤中查看記錄。

## 檢視 Data Lake Analytics 帳戶的診斷記錄

檢視 Data Lake Analytics 帳戶的記錄資料有兩種方式。

* 從 Data Lake Analytics 帳戶設定
* 從儲存資料的 Azure 儲存體帳戶

### 使用 Data Lake Analytics 設定檢視

1. 在 Data Lake Analytics 帳戶的 [設定] 刀鋒視窗中，按一下 [診斷記錄]。

	![檢視診斷記錄](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "檢視診斷記錄")

2. 在 [診斷記錄] 刀鋒視窗中，您應該會看到依照 [稽核記錄] 和 [要求記錄] 分類的記錄。
	* 要求記錄能擷取所有以 Data Lake Analytics 帳戶提出的 API 要求。
	* 稽核記錄與要求記錄相似，不過能針對以 Data Lake Analytics 帳戶執行之作業提供更詳細的明細。例如，要求記錄中的一個上傳 API 呼叫可能會致使稽核記錄出現多個「附加」作業。

3. 針對記錄項目按一下 [下載] 連結來下載記錄。

### 從包含記錄資料的 Azure 儲存體帳戶

1. 開啟與與用於記錄的 Data Lake Analytics 關聯的Azure 儲存體帳戶刀鋒視窗，然後按一下 [Blob]。[Blob 服務] 刀鋒視窗會列出兩個容器。

	![檢視診斷記錄](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "檢視診斷記錄")

	* 容器 **insights-logs-audit** 包含稽核記錄檔。
	* 容器 **insights-logs-requests** 包含要求記錄檔。

2. 在這些容器中，紀錄會儲存在下列結構底下。

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] 路徑中的 `##` 項目包含記錄檔的建立年、月、日和小時。Data Lake Analytics 每小時會建立一個檔案，因此 `m=` 一律會包含 `00` 值。

	例如，稽核記錄檔的完整路徑可能是：
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

	同樣的，要求記錄檔的完整路徑可能是：
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## 了解記錄資料的結構

稽核和要求記錄採用 JSON 格式。在本節中，我們要探討要求和稽核記錄的 JSON 結構。

### 要求記錄

以下是採用 JSON 格式之要求記錄中的範例項目。每個 Blob 會一個名為**記錄**的根物件，其中包含記錄檔物件的陣列。

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-07T21:02:53.456Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
			 "category": "Requests",
			 "operationName": "GetAggregatedJobHistory",
			 "resultType": "200",
			 "callerIpAddress": "::ffff:1.1.1.1",
			 "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
			 "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
			 "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
		}
		,
		. . . .
	  ]
	}

#### 要求記錄的結構描述

| Name | 類型 | 說明 |
|-----------------|--------|--------------------------------------------------------------------------------|
| 分析 | String | 記錄的時間戳記 (UTC 時間) |
| resourceId | String | 作業發生之資源的識別碼 |
| category | String | 記錄類別。例如，**要求**。 |
| operationName | String | 記錄的作業名稱。例如，GetAggregatedJobHistory。 |
| resultType | String | 作業的狀態。例如，200。 |
| callerIpAddress | String | 提出要求之用戶端的 IP 位址 |
| correlationId | String | 記錄檔的識別碼。此值可用來群組一組相關的記錄檔項目 |
| 身分識別 | Object | 產生記錄的身分識別 |
| properties | JSON | 請參閱下一節 (要求記錄檔屬性結構描述) 以取得詳細資訊 |

#### 要求記錄屬性結構描述

| Name | 類型 | 說明 |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod | String | 作業使用的 HTTP 方法。例如，GET。 |
| Path | String | 執行作業的所在路徑 |
| RequestContentLength | int | HTTP 要求的內容長度 |
| ClientRequestId | String | 可唯一識別要求的識別碼 |
| StartTime | String | 伺服器接收到要求的時間 |
| EndTime | String | 伺服器傳送回應的時間 |

### 稽核記錄檔

以下是採用 JSON 格式之稽核記錄中的範例項目。每個 Blob 會一個名為**記錄**的根物件，其中包含記錄檔物件的陣列

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-28T19:15:16.245Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
			 "category": "Audit",
			 "operationName": "JobSubmitted",
			 "identity": "user@somewhere.com",
			 "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
		}
		,
		. . . .
	  ]
	}

#### 稽核記錄的結構描述

| Name | 類型 | 說明 |
|-----------------|--------|--------------------------------------------------------------------------------|
| 分析 | String | 記錄的時間戳記 (UTC 時間) |
| resourceId | String | 作業發生之資源的識別碼 |
| category | String | 記錄類別。例如，**稽核**。 |
| operationName | String | 記錄的作業名稱。例如，JobSubmitted。 |
| resultType | String | 作業狀態 (operationName) 的子狀態。 |
| resultSignature | String | 作業狀態 (operationName) 的其他詳細資料。 |
| 身分識別 | String | 要求作業的使用者。例如，susan@contoso.com。 |
| properties | JSON | 請參閱下一節 (稽核記錄檔屬性結構描述) 以取得詳細資訊 |

> [AZURE.NOTE] __resultType__ 和 __resultSignature__ 會提供作業結果的相關資訊，並且只會在作業完成時才包含值。例如，當 __operationName__ 包含 __JobStarted__ 或 __JobEnded__ 的值時，它們便會包含值。

#### 稽核記錄屬性結構描述

| Name | 類型 | 說明 |
|------------|--------|------------------------------------------|
| JobId | String | 指派給作業的識別碼 |
| JobName | String | 為作業提供的名稱 |
| JobRunTime | String | 用來處理作業的執行階段 |
| SubmitTime | String | 作業提交時間 (UTC 格式) |
| StartTime | String | 作業在提交後開始執行的時間 (UTC 格式)。 |
| EndTime | String | 作業結束時間。 |
| 平行處理原則 | String | 在提交期間為此作業要求的 Data Lake Analytics 單位數目。 |

> [AZURE.NOTE] __SubmitTime__、__StartTime__、__EndTime__ 和__平行處理原則__會提供作業的相關資訊，並且只會在作業啟動或完成時才包含值。例如，__SubmitTime__ 會在 __operationName__ 指出 __JobSubmitted__ 之後包含值。

## 處理記錄資料的範例

Azure Data Lake Analytics 會提供有關如何處理和分析記錄資料的範例。您可以在 [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) 找到範例。


## 另請參閱

- [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)

<!---HONumber=AcomDC_0810_2016---->