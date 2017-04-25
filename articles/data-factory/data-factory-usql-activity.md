---
title: "使用 U-SQL 指令碼轉換資料 - Azure | Microsoft Docs"
description: "了解如何在 Azure Data Lake Analytics 計算服務上執行 U-SQL 指令碼來處理或轉換資料。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: b41d906d6948f0f9e3cdb38b4a478b39f55ce219
ms.lasthandoff: 03/14/2017


---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>在 Azure Data Lake Analytics 上執行 U-SQL 指令碼來轉換資料 
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 活動](data-factory-hive-activity.md) 
> * [Pig 活動](data-factory-pig-activity.md)
> * [MapReduce 活動](data-factory-map-reduce.md)
> * [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
> * [Spark 活動](data-factory-spark.md)
> * [Machine Learning Batch 執行活動](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新資源活動](data-factory-azure-ml-update-resource-activity.md)
> * [預存程序活動](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活動](data-factory-usql-activity.md)
> * [.NET 自訂活動](data-factory-use-custom-activities.md)

Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。 它包含一系列活動，其中每個活動都會執行特定的處理作業。 本文將說明 **Data Lake Analytics U-SQL 活動**，它在 **Azure Data Lake Analytics** 計算連結的服務上執行 **U-SQL** 指令碼。 

> [!NOTE]
> 使用 Data Lake Analytics「U-SQL 活動」來建立管線之前，請先建立 Azure Data Lake Analytics 帳戶。 若要了解 Azure Data Lake Analytics，請參閱 [開始使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。
> 
> 請檢閱 [建置您的第一個管線教學課程](data-factory-build-your-first-pipeline.md) ，以了解建立 Data Factory、連結服務、資料集和管線的詳細步驟。 您可以搭配「Data Factory 編輯器」、Visual Studio 或 Azure PowerShell 使用 JSON 程式碼片段來建立 Data Factory 實體。
> 
> 

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 連結服務
您需建立 **Azure Data Lake Analytics** 連結服務，來將 Azure Data Lake Analytics 計算服務連結到 Azure Data Factory。 管線中的 Data Lake Analytics U-SQL 活動會參考此連結服務。 

下列範例提供 Azure Data Lake Analytics 連結服務的 JSON 定義。 

```JSON
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

下表提供 JSON 定義中所使用之屬性的描述： 

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| 類型 |type 屬性應設為： **AzureDataLakeAnalytics**。 |是 |
| accountName |Azure Data Lake Analytics 帳戶名稱。 |是 |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI。 |否 |
| 授權 |按一下 Data Factory 編輯器中的 [授權]  按鈕並完成 OAuth 登入後，即會自動擷取授權碼。 |是 |
| subscriptionId |Azure 訂用帳戶識別碼 |否 (如果未指定，便會使用 Data Factory 的訂用帳戶)。 |
| resourceGroupName |Azure 資源群組名稱 |否 (若未指定，便會使用 Data Factory 的資源群組)。 |
| sessionId |OAuth 授權工作階段的工作階段識別碼。 每個工作階段識別碼都是唯一的，只能使用一次。 此工作階段識別碼是在「Data Factory 編輯器」中自動產生的。 |是 |

您使用 [授權] 按鈕所產生的授權碼在一段時間後會到期。 請參閱下表以了解不同類型的使用者帳戶的到期時間。 當驗證**權杖到期**時，您可能會看到下列錯誤訊息：認證作業發生錯誤：invalid_grant - AADSTS70002：驗證認證時發生錯誤。 AADSTS70008：提供的存取授權已過期或撤銷。 追蹤識別碼：d18629e8-af88-43c5-88e3-d8419eb1fca1 相互關連識別碼：fac30a0c-6be6-4e02-8d69-a776d2ffefd7 時間戳記：2015-12-15 21:09:31Z

| 使用者類型 | 到期時間 |
|:--- |:--- |
| 不受 Azure Active Directory 管理的使用者帳戶 (@hotmail.com、@live.com 等) |12 小時 |
| 受 Azure Active Directory (AAD) 管理的使用者帳戶 |最後一次執行配量後的 14 天。 <br/><br/>如果以 OAuth 式連結服務為基礎的配量至少每 14 天執行一次，則為 90 天。 |

如果要避免/解決此錯誤，請在**權杖到期**時使用 [授權] 按鈕重新授權，然後重新部署連結服務。 您也可以使用下一節中的程式碼以程式設計方式產生 **sessionId** 和 **authorization** 屬性的值。 

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>若要以程式設計方式產生 sessionId 與 authorization 的值

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

請參閱 [AzureDataLakeStoreLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、[AzureDataLakeAnalyticsLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)和 [AuthorizationSessionGetResponse 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主題，以取得在程式碼中使用的 Data Factory 類別的詳細資訊。 請針對 WindowsFormsWebAuthenticationDialog 類別，新增對下列項目的參考：Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll。 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活動
下列 JSON 片段會定義具有 Data Lake Analytics U-SQL 活動的管線。 活動定義具有您稍早建立的 Azure Data Lake Analytics 連結服務的參考。   

```JSON
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

下表描述此活動特有的屬性之名稱和描述。 

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 |類型屬性必須設為 DataLakeAnalyticsU-SQL 。 |是 |
| scriptPath |包含 U-SQL 指令碼的資料夾的路徑。 檔案的名稱有區分大小寫。 |否 (如果您使用指令碼) |
| scriptLinkedService |連結服務會連結包含 Data Factory 的指令碼的儲存體 |否 (如果您使用指令碼) |
| script |指定內嵌指令碼而不是指定 scriptPath 和 scriptLinkedService。 例如："script": "CREATE DATABASE test"。 |否 (如果您使用 scriptPath 和 scriptLinkedService) |
| degreeOfParallelism |同時用來執行作業的節點數目上限。 |否 |
| 優先順序 |判斷應該選取排入佇列的哪些工作首先執行。 編號愈低，優先順序愈高。 |否 |
| 參數 |U-SQL 指令碼的參數 |否 |

指令碼定義請參閱 [SearchLogProcessing.txt 指令碼定義](#script-definition) 。 

## <a name="sample-input-and-output-datasets"></a>建立輸入和輸出資料集
### <a name="input-dataset"></a>輸入資料集
在此範例中，輸入的資料是位於 Azure Data Lake Store (datalake/input 資料夾中的 SearchLog.tsv 檔案)。 

```JSON
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>輸出資料集
在此範例中，U-SQL 指令碼所產生的輸出資料會儲存在 Azure Data Lake Store (datalake/output 資料夾)。 

```JSON
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Data Lake Store 連結服務範例
以下是輸入/輸出資料集所使用的範例 Azure Data Lake Store 連結服務的定義。 

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>"
        }
    }
}
```

如需 JSON 屬性的描述，請參閱 [將資料移入和移除 Azure Data Lake Store](data-factory-azure-datalake-connector.md) 一文。 

## <a name="sample-u-sql-script"></a>U-SQL 指令碼範例

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

ADF 會使用 ‘parameters’ 區段來動態傳遞 U-SQL 指令碼中 **@in** 和 **@out** 參數的值。 請參閱管線定義中的 ‘parameters’ 區段。

您也可以在管線定義中，針對在 Azure Data Lake Analytics 服務上執行的作業，指定其他屬性 (例如 degreeOfParallelism 和 priority)。

## <a name="dynamic-parameters"></a>動態參數
在範例管線定義中，in 和 out 參數都被指派了硬式編碼值。 

```JSON
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

您可改為使用動態參數。 例如： 

```JSON
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

在此例中，系統仍然會從 /datalake/input 資料夾挑選輸入檔，並在 /datalake/output 資料夾中產生輸出檔。 檔案名稱則是根據配量開始時間動態產生。  


