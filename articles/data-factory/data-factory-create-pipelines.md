---
title: "在 Data Factory 中建立/排程管線和鏈結活動 | Microsoft Docs"
description: "了解如何在 Azure Data Factory 中建立用來移動和轉換資料的資料管線。 建立資料導向工作流程以產生可供使用的資訊。"
keywords: "資料管線, 資料導向工作流程"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d841c57dc736f7d690a6dc97863b7568365fd01a


---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure Data Factory 中的管線及活動
本文協助您了解 Azure Data Factory 中的管線和活動，並使用這些項目來為您的資料移動和資料處理案例建構端對端的資料導向工作流程。  

> [!NOTE]
> 本文假設您已經看過 [Azure Data Factory 簡介](data-factory-introduction.md)。 如果您沒有建立 Data Factory 的實作經驗，請參閱 [建立您的第一個 Data Factory](data-factory-build-your-first-pipeline.md) 此教學課程，以進一步了解這篇文章。  
> 
> 

## <a name="what-is-a-data-pipeline"></a>什麼是資料管線？
**管線**是邏輯相關**活動**的群組。 用來將活動群組成一個單位，共同執行任務。 若要進一步了解管線，您需要先了解活動。 

## <a name="what-is-an-activity"></a>什麼是活動？
活動會定義在您資料上執行的動作。 每個活動會取得零或多個 [資料集](data-factory-create-datasets.md) 做為輸入，並產生一或多個資料集做為輸出。 

例如，您可能會使用複製活動來協調從一個資料存放區複製資料到另一個資料存放區的流程。 同樣地，您可能會使用在 Azure HDInsight 叢集上執行 Hive 查詢的 HDInsight Hive 活動，來轉換您的資料。 Azure Data Factory 提供各種[資料轉換](data-factory-data-transformation-activities.md)和[資料移動](data-factory-data-movement-activities.md)活動。 您也可以選擇建立自訂 .NET 活動來執行自己的程式碼。 

## <a name="sample-copy-pipeline"></a>範例複製管線
在以下的範例管線中， **Copy** in the **活動** 類型的活動。 在此範例中， [Copy 活動](data-factory-data-movement-activities.md) 會將資料從 Azure Blob 儲存體複製到 Azure SQL 資料庫。 

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
} 
```

請注意下列幾點：

* 在活動區段中，只會有一個 **type** 設為 **Copy** 的活動。
* 活動的輸入設定為 **InputDataset**，活動的輸出則設定為 **OutputDataset**。
* 在 **typeProperties** 區段中，來源類型指定為 **BlobSource**，接收類型指定為 **SqlSink**。

如需建立此管線的完整逐步解說，請參閱 [教學課程：將資料從 Blob 儲存體複製到 SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

## <a name="sample-transformation-pipeline"></a>範例轉換管線
在以下的範例管線中， **CopyActivity** in the **活動** 類型的活動。 在此範例中， [HDInsight Hive 活動](data-factory-hive-activity.md) 會執行 Azure HDInsight Hadoop 叢集上的 Hive 指令碼檔案，來轉換 Azure Blob 儲存體的資料。 

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

請注意下列幾點： 

* 在活動區段中，只會有一個 **type** 設為 **HDInsightHive** 的活動。
* Hive 指令碼檔案 **partitionweblogs.hql** 儲存於 Azure 儲存體帳戶 (透過名為 **AzureStorageLinkedService** 的 scriptLinkedService 指定)，且位於 **adfgetstarted** 容器的 **script** 資料夾中。
* **defines** 區段可用來指定執行階段設定，該設定將傳遞到 Hive 指令碼做為 Hive 設定值 (例如 ${hiveconf:inputtable}、${hiveconf:partitionedtable})。

如需建立此管線的完整逐步解說，請參閱 [教學課程：使用 Hadoop 叢集建置您的第一個管線來處理資料](data-factory-build-your-first-pipeline.md)。 

## <a name="chaining-activities"></a>鏈結活動
如果您在管線中有多個活動，而且活動的輸出不是另一個活動的輸入，則當活動的輸入資料分割已備妥時，活動可能會平行執行。 

您可以將一個活動的輸出資料集設為另一個活動的輸入資料集，藉此鏈結兩個活動。 活動可以在相同的管線中或在不同的管線中。 只有當第一個活動執行成功完成時，第二個活動才會執行。 

例如，請考慮下列情況：

1. 管線 P1 具有需要外部輸入資料集 D1 的活動 A1，並且會產生**輸出**資料集 **D2**。
2. 管線 P2 具有需要來自資料集 **D2** 之**輸入**的活動 A2，並且會產生輸出資料集 D3。

在此案例中，活動 A1 會在外部資料提供使用時執行，且達到排程的可用性頻率。  活動 A2 會在 D2 的排定的分割可供使用時執行，且達到排程的可用性頻率。 如果資料集 D2 中的其中一個分割發生錯誤，則不會針對該分割執行 A2，直到該分割可供使用為止。

圖表檢視：

![兩個管線中的鏈結活動](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

兩個活動同時在相同管線中的 [圖表檢視]： 

![相同管線中的鏈結活動](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

如需詳細資訊，請參閱 [排程和執行](#chaining-activities)。 

## <a name="scheduling-and-execution"></a>排程和執行
到目前為止，您已了解什麼是管線和活動。 您也已大致了解其定義方式以及 Azure Data Factory 中的高階活動觀點。 現在讓我們了解一下其執行方式。 

管線僅在其開始時間與結束時間之間有作用。 在開始時間之前或結束時間之後就不會執行。 如果管線已暫停，不論其開始和結束時間為何，都不會執行。 若要執行管線，則不該將它暫停。 事實上，並不是執行管線。 而是執行管線中的活動。 不過，活動會在管線的整體內容中執行。 

請參閱 [排程和執行](data-factory-scheduling-and-execution.md) ，以了解如何在 Azure Data Factory 中排程和執行。

## <a name="create-pipelines"></a>建立管線
Azure Data Factory 提供各種機制來製作及部署管線 (其中包含一或多個活動)。 

### <a name="using-azure-portal"></a>使用 Azure 入口網站
您可以在 Azure 入口網站中使用 Data Factory 編輯器建立管線。 如需逐步解說，請參閱 [開始使用 Azure Data Factory (Data Factory 編輯器)](data-factory-build-your-first-pipeline-using-editor.md) 。 

### <a name="using-visual-studio"></a>使用 Visual Studio
您可以使用 Visual Studio 來製作管線及部署至 Azure Data Factory。 如需端對端逐步解說，請參閱 [開始使用 Azure Data Factory (Data Factory 編輯器)](data-factory-build-your-first-pipeline-using-vs.md) 。 

### <a name="using-azure-powershell"></a>使用 Azure PowerShell
您可以使用 Azure PowerShell 在 Azure Data Factory 中建立管線。 假設您已在檔案 (位於 c:\DPWikisample.json) 中定義管線 JSON。 您可以將它上傳到 Azure Data Factory 執行個體，如下列範例所示：

```PowerShell
New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json
```

請參閱 [開始使用 Azure Data Factory (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md) ，以取得使用管線建立資料處理站的端對端逐步解說。 

### <a name="using-net-sdk"></a>使用 .NET SDK
您也可以透過 .NET SDK 建立和部署管線。 這項機制可用來以程式設計的方式建立管線。 如需詳細資訊，請參閱 [以程式設計方式建立、管理和監視 Data Factory](data-factory-create-data-factories-programmatically.md)。 

### <a name="using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
您可以使用 Azure Resource Manager 範本建立與部署管線。 如需詳細資訊，請參閱 [開始使用 Azure Data Factory (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md)。 

### <a name="using-rest-api"></a>使用 REST API
您也可以使用 REST API 建立和部署管線。 這項機制可用來以程式設計的方式建立管線。 如需詳細資訊，請參閱 [建立或更新管線](https://msdn.microsoft.com/library/azure/dn906741.aspx)。 

## <a name="monitor-and-manage-pipelines"></a>監視與管理管線
一旦部署管線，您即可管理和監視管線、配量和執行。 如需詳細資訊，請參閱 [監視和管理管線](data-factory-monitor-manage-pipelines.md)。

## <a name="pipeline-json"></a>管線 JSON
讓我們來深入探討如何定義 JSON 格式的管線。 管線的一般結構如下所示：

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>"
    }
}
```

[ **活動** ] 區段內可以有一或多個已定義的活動。 每個活動都有下列最上層結構：

```json
{
    "name": "ActivityName",
    "description": "description", 
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    }
    "scheduler":
    {
    }
}
```

下表說明活動和管線 JSON 定義的屬性：

| Tag | 說明 | 必要 |
| --- | --- | --- |
| 名稱 |活動或管線的名稱。 指定一個名稱，以表示活動或管線設定要進行的動作<br/><ul><li>字元數目上限︰260</li><li>開頭必須為字母、數字或底線 (_)</li><li>不允許使用下列字元：“.”、“+”、“?”、“/”、“<”、”>”、”*”、”%”、”&”、”:”、”\\”</li></ul> |是 |
| 說明 |說明活動或管線用途的文字 |是 |
| 類型 |指定活動的類型。 如需不同類型的活動，請參閱[資料移動活動](data-factory-data-movement-activities.md)和[資料轉換活動](data-factory-data-transformation-activities.md)文章。 |是 |
| 輸入 |活動所使用的輸入資料表<br/><br/>// 一個輸入資料表<br/>"inputs":  [ { "name": "inputtable1"  } ],<br/><br/>// 兩個輸入資料表 <br/>"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ], |是 |
| 輸出 |活動所使用的輸出資料表// 一個輸出資料表<br/>"outputs":  [ { "name": “outputtable1” } ],<br/><br/>// 兩個輸出資料表<br/>"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ], |是 |
| linkedServiceName |活動所使用的連結服務名稱。 <br/><br/>活動可能會要求您指定可連結至所需計算環境的連結服務。 |是：適用於 HDInsight 活動和 Azure Machine Learning Batch 評分活動  <br/><br/>否：所有其他 |
| typeProperties |typeProperties 區段中的屬性會視活動的類型而定。 |否 |
| 原則 |會影響活動之執行階段行為的原則。 如果未指定，則會使用預設原則。 |否 |
| start |管線的開始日期時間。 必須使用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 <br/><br/>您可以指定本地時間，如 EST 時間。 範例如下："2016-02-27T06:00:00**-05:00**" 代表 6 AM EST.<br/><br/>管線的 start 和 end 屬性共同指定管線的作用中期間。 輸出配量只會在作用中期間內產生。 |否<br/><br/>如果您指定 end 屬性的值，也必須指定 start 屬性的值。<br/><br/>開始和結束時間都可以是空白來建立管線。 必須指定兩個值，才能設定執行管線的作用中時間。 如果您建立管線時未指定開始和結束時間，您可以在稍後使用 Set-AzureRmDataFactoryPipelineActivePeriod Cmdlet 進行設定。 |
| end |管線的結束日期時間。 如果已指定，則必須使用 ISO 格式。 例如︰2014年-10-14T17:32:41Z <br/><br/>您可以指定本地時間，如 EST 時間。 範例如下："2016-02-27T06:00:00**-05:00**" 代表 6 AM EST.<br/><br/>若要無限期地執行管線，請指定 9999-09-09 做為 end 屬性的值。 |否 <br/><br/>如果您指定 start 屬性的值，也必須指定 end 屬性的值。<br/><br/>請參閱 **start** 屬性的註釋。 |
| isPaused |如果設為 true，管線不會執行。 預設值 = false。 您可以使用此屬性來啟用或停用。 |否 |
| scheduler |“scheduler” 屬性用來定義所要的活動排程。 其子屬性與 [資料集中的可用性屬性](data-factory-create-datasets.md#Availability)中的屬性相同。 |否 |
| pipelineMode |排程管線執行的方法。 允許的值包括：scheduled (預設值)、onetime。<br/><br/>‘Scheduled’ 表示管線會根據其作用中期間 (開始和結束時間) 依指定的時間間隔執行。 ‘Onetime’ 表示管線只會執行一次。 目前，Onetime 管線在建立之後即無法進行修改/更新。 如需 onetime 設定的詳細資料，請參閱 [Onetime 管線](data-factory-scheduling-and-execution.md#onetime-pipeline)。 |否 |
| expirationTime |建立之後，管線有效且應該保持佈建的期間。 如果管線沒有任何作用中、失敗或擱置執行，則會在到達到期時間之後自動予以刪除。 |否 |
| 資料集 |要供管線中已定義活動所使用的資料集清單。 此屬性可用來定義此管線所特有但未在 Data Factory 內定義的資料集。 此管線內定義的資料集只有此管線才能使用，並無法共用。 如需詳細資訊，請參閱 [範圍資料集](data-factory-create-datasets.md#scoped-datasets) 。 |否 |

### <a name="policies"></a>原則
原則會影響活動的執行階段行為，特別是在處理資料表配量的時候。 下表提供詳細資料。

| 屬性 | 允許的值 | 預設值 | 說明 |
| --- | --- | --- | --- |
| 並行 |整數  <br/><br/>最大值：10 |1 |活動的並行執行數目。<br/><br/>它可決定不同配量上可以發生的平行活動執行數目。 例如，如果活動需要處理大量可用的資料，具有較大的並行值會加快資料處理。 |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |決定正在處理之資料配量的順序。<br/><br/>例如，如果您有 2 個配量 (一個發生在下午 4 點，另一個發生在下午 5 點)，而兩者都暫停執行。 如果您將 executionPriorityOrder 設為 NewestFirst，則會先處理下午 5 點的配量。 同樣地，如果您將 executionPriorityOrder 設為 OldestFIrst，則會處理下午 4 點的配量。 |
| retry |整數 <br/><br/>最大值可以是 10 |0 |在配量的資料處理標示為 [失敗] 前的重試次數。 資料配量的活動執行會一直重試，直到指定的重試計數為止。 在失敗後會儘速完成重試。 |
| timeout |TimeSpan |00:00:00 |活動的逾時。 範例︰00:10:00 (意指逾時 10 分鐘)<br/><br/>如果您未指定值 (或值為 0)，代表無限逾時。<br/><br/>如果配量的資料處理時間超過逾時值，該活動會遭到取消，且系統會嘗試重試處理。 重試次數取決於 retry 屬性。 若發生逾時，狀態會設為 TimedOut。 |
| delay |TimeSpan |00:00:00 |指定配量之資料處理開始之前的延遲。<br/><br/>資料配量的活動執行會在 Delay 超出預期執行時間後開始。<br/><br/>範例︰00:10:00 (意指延遲 10 分鐘) |
| longRetry |整數 <br/><br/>最大值：10 |1 |配量執行失敗之前的長時間重試嘗試次數。<br/><br/>多個 longRetry 嘗試之間以 longRetryInterval 隔開。 所以如果您需要指定重試嘗試之間的時間，請使用 longRetry。 如果您指定 Retry 和 longRetry 兩者，每個 longRetry 嘗試都包含 Retry 嘗試，且最大嘗試次數是 Retry * longRetry。<br/><br/>例如，如果活動原則的設定如下︰<br/>Retry：3<br/>longRetry：2<br/>longRetryInterval：01:00:00<br/><br/>假設只有一個要執行的配量 (狀態是 Waiting)，且活動執行每次都失敗。 一開始會有 3 次連續執行嘗試。 在每次嘗試之後，配量狀態會是 Retry。 在前 3 次嘗試結束之後，配量狀態會是 LongRetry。<br/><br/>一個小時 (也就是 longRetryInteval 的值) 之後，會有另一組 3 次連續執行嘗試。 在那之後，配量狀態會是 Failed，不會再嘗試重試。 因此全部已進行 6 次嘗試。<br/><br/>如果任何執行成功，配量狀態會是 Ready 且不會再嘗試重試。<br/><br/>longRetry 可能用於下列情況：相依資料達到不具決定性的次數，或進行資料處理的整體環境很脆弱。 在這類情況下逐一進行重試並沒有幫助，而在一段時間後進行重試則會導致所要的結果。<br/><br/>提醒：請勿設定較大的 longRetry 或 longRetryInterval 值。 較大的值通常表示其他系統問題。 |
| longRetryInterval |TimeSpan |00:00:00 |長時間重試嘗試之間的延遲 |

## <a name="next-steps"></a>後續步驟
* 了解 [在 Azure Data Factory 中排程和執行](data-factory-scheduling-and-execution.md)。  
* 深入了解 Azure Data Factory 中的[資料移動](data-factory-data-movement-activities.md)和[資料轉換功能](data-factory-data-transformation-activities.md)
* 了解 [在 Azure Data Factory 中管理和監視](data-factory-monitor-manage-pipelines.md)。
* [建置和部署第一個管線](data-factory-build-your-first-pipeline.md)。 




<!--HONumber=Nov16_HO3-->


