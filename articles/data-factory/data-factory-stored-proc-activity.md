---
title: "SQL Server 預存程序活動"
description: "深入了解如何使用 SQL Server 預存程序活動，以從 Data Factory 管線叫用 Azure SQL Database 或 Azure SQL 資料倉儲中的預存程序。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 7faa27220bcc07fff0bb2a77e7b90c386cc5ae1e
ms.lasthandoff: 03/14/2017


---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server 預存程序活動
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

您在 Data Factory [管線](data-factory-create-pipelines.md)中使用資料轉換活動，以轉換和處理您的原始資料以進行預測和深入了解。 預存程序活動是 Data Factory 支援的其中一個轉換活動。 本文是根據 [資料轉換活動](data-factory-data-transformation-activities.md) 一文，它呈現資料轉換和支援的轉換活動的一般概觀。

您可以使用預存程序活動，以叫用下列其中一個資料存放區中的預存程序：您的企業或 Azure 虛擬機器 (VM) 中的 Azure SQL Database、Azure SQL 資料倉儲、SQL Server 資料庫。  如果您使用 SQL Server，請在裝載資料庫的同一部電腦上或可存取資料庫的個別電腦上安裝資料管理閘道。 資料管理閘道是一套透過安全且可管理的方式，將內部部署/Azure VM 上的資料來源連結至雲端服務的元件。 如需詳細資訊，請參閱[資料管理閘道](data-factory-data-management-gateway.md)文章。

下列逐步解說會在管線中使用預存程序活動，來叫用 Azure SQL Database 中的預存程序。 

## <a name="walkthrough"></a>逐步介紹
### <a name="sample-table-and-stored-procedure"></a>範例資料表與預存程序
1. 在您的 Azure SQL Database 中，使用 SQL Server Management Studio 或任何其他您很熟悉的工具，來建立下列 **資料表** 。 Datetimestamp 資料行是產生對應識別碼的日期和時間。

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    Id 是可唯一識別的，而 datetimestamp 資料行是產生對應識別碼的日期和時間。
    
    ![範例資料](./media/data-factory-stored-proc-activity/sample-data.png)

    在此範例中，預存程序在 Azure SQL Database 中。 如果預存程序是在 Azure SQL 資料倉儲和 SQL Server Database 中，此方式是類似的。 對於 SQL Server 資料庫中，您必須安裝[資料管理閘道](data-factory-data-management-gateway.md)。
2. 建立下列**預存程序**，將資料插入 **sampletable**。

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > 參數 (在此範例中是 DateTime) 的**名稱**和**大小寫**必須與下列管線/活動 JSON 中指定的參數相符。 在預存程序定義中，請務必使用 **@** 做為參數前置詞。

### <a name="create-a-data-factory"></a>建立 Data Factory
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下左側功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。

    ![新增 Data Factory](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. 在 [新增 Data Factory] 刀鋒視窗中，輸入 **SProcDF** 做為 [名稱]。 Azure Data Factory 名稱必須是**全域唯一的**。 您必須在 Data Factory 的名稱前面加上您的名稱，才能成功建立 Factory。

   ![新增 Data Factory](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. 選取您的 **Azure 訂用帳戶**。
5. 針對**資源群組**，請執行下列其中一個步驟︰
   1. 按一下 [新建] 並輸入資源群組的名稱。
   2. 按一下 [使用現有的] 並選取現有的資源群組。  
6. 選取 Data Factory 的 [位置]  。
7. 選取 [釘選到儀表板]，讓您下一次登入時可以在儀表板上看到 Data Factory。
8. 按一下 [新增 Data Factory] 刀鋒視窗上的 [建立]。
9. 您會看到 Data Factory 建立在 Azure 入口網站的 [儀表板] 中。 在 Data Factory 成功建立後，您會看到 Data Factory 頁面，顯示 Data Factory 的內容。

   ![Data Factory 首頁](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>建立 Azure SQL 連結服務
建立 Data Factory 之後，您可以建立 Azure SQL 連結的服務，將 Azure SQL Database 連結到 Data Factory。 此資料庫包含 sampletable 資料表和 sp_sample 預存程序。

1. 在 **SProcDF** 的 [Data Factory] 刀鋒視窗上，按一下 [製作和部署] 來啟動 Data Factory 編輯器。
2. 在命令列上按一下 [新增資料儲存區]，然後選擇 [Azure SQL Database]。 您應該會在編輯器中看到用來建立 Azure SQL 連結服務的 JSON 指令碼。

   ![新增資料存放區](media/data-factory-stored-proc-activity/new-data-store.png)
3. 在 JSON 指令碼中，進行下列變更：

   1. 以 Azure SQL Database 伺服器的名稱取代 `<servername>`。
   2. 以建立資料表和預存程序的資料庫取代 `<databasename>`。
   3. 以具有資料庫存取權的使用者帳戶取代 `<username@servername>`。
   4. 以使用者帳戶的密碼取代 `<password>`。

      ![新增資料存放區](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. 若要部署連結服務，請按一下命令列的 [部署]。 在左側的樹狀檢視中確認您已看到 AzureSqlLinkedService。

    ![含連結服務的樹狀檢視](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>建立輸出資料表
1. 按一下 **...更多** (工具列上)，按一下 新增資料集，然後按一下 Azure SQL。 命令列的 [新資料集]，然後選取 [Azure SQL]。

    ![含連結服務的樹狀檢視](media/data-factory-stored-proc-activity/new-dataset.png)
2. 將下列 JSON 指令碼複製/貼到 JSON 編輯器。

    ```JSON
    {                
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. 若要部署資料集，請按一下命令列上的 [部署]。 確認您已在樹狀檢視中看到該資料集。

    ![含連結服務的樹狀檢視](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>使用 SqlServerStoredProcedure 活動建立管線
現在，讓我們使用 SqlServerStoredProcedure 活動來建立管線。

1. 如果沒看到此按鈕，請按一下工具列 **...命令列上的 [更多]**，然後按一下 [新增管線]。
2. 複製/貼上下列 JSON 程式碼片段：   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
             "start": "2016-08-02T00:00:00Z",
             "end": "2016-08-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```

    將 **storedProcedureName** 設定為 **sp_sample**。 **DateTime** 參數的名稱和大小寫必須符合預存程序定義中參數的名稱和大小寫。

    如果您要為參數傳遞 null，請使用語法："param1": null (全部小寫)。
3. 若要部署管線，請按一下工具列上的 [部署]。  

### <a name="monitor-the-pipeline"></a>監視管線
1. 按一下 **X** 以關閉 [Data Factory 編輯器] 刀鋒視窗、瀏覽回 [Data Factory] 刀鋒視窗，然後按一下 [圖表]。

    ![圖表圖格](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. 在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。

    ![圖表圖格](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. 在 [圖表檢視] 中，按兩下 `sprocsampleout` 資料集。 您會看到就緒狀態的配量。 由於配量是針對 JSON 的開始時間和結束時間之間的每一個小時所產生，因此，應該會有&5; 個配量。

    ![圖表圖格](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. 當配量處於**就緒**狀態時，請對 Azure SQL Database 執行 `select * from sampletable` 查詢，以驗證預存程序已將資料插入資料表。

   ![輸出資料](./media/data-factory-stored-proc-activity/output.png)

   如需監視 Azure Data Factory 管線的詳細資訊，請參閱 [監視管線](data-factory-monitor-manage-pipelines.md) 。  

> [!NOTE]
> 在此範例中，SprocActivitySample 沒有輸入。 如果您想要鏈結此活動與活動上游 (亦即預先處理)，可以使用上游活動的輸出做為此活動的輸入。 在此情況下，上游活動完成且能使用上游活動的輸出 (處於就緒狀態) 之前，此活動不會執行。 輸入無法直接做為預存程序活動的參數使用。
>
>

## <a name="json-format"></a>JSON 格式
以下是 JSON 格式來定義預存程序活動︰

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":  
        {
            "param1": "param1Value"
            …
        }
    }
}
```

## <a name="json-properties"></a>JSON 屬性
| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| 名稱 | 活動的名稱 |是 |
| 說明 |說明活動用途的文字 |否 |
| 類型 | 必須設定為：**SqlServerStoredProcedure** | 是 |
| 輸入 | 選用。 如果您有指定輸入資料集，它必須可供使用 (「就緒」狀態)，預存程序活動才能執行。 在預存程序中輸入資料集無法做為參數取用。 它只會用來在啟動預存程序活動之前檢查相依性。 |否 |
| 輸出 | 您必須指定預存程序活動的輸出資料集。 輸出資料集會指定預存程序活動的 **排程** (每小時、每週、每月等)。 <br/><br/>輸出資料集必須使用參考了想在其中執行預存程序之 Azure SQL Database、Azure SQL 資料倉儲或 SQL Server Database 的 **連結服務** 。 <br/><br/>輸出資料集可以做為傳遞預存程序結果，以供管線中的另一個活動 ([鏈結活動](data-factory-scheduling-and-execution.md#run-activities-in-a-sequence)) 進行後續處理的方式。 不過，Data Factory 不會自動將預存程序的輸出寫入至此資料集。 它是會寫入至輸出資料集所指向之 SQL 資料表的預存程序。 <br/><br/>在某些情況下，輸出資料集可以是 **虛擬資料集**，只會用來指定用於執行預存程序活動的排程。 |是 |
| storedProcedureName |指定 Azure SQL Database 或 Azure SQL 資料倉儲中預存程序的名稱，由輸出資料表使用的連結的服務代表。 |是 |
| storedProcedureParameters |指定預存程序參數的值。 如果您要為參數傳遞 null，請使用語法："param1": null (全部小寫)。 請參閱下列範例以了解如何使用這個屬性。 |否 |

## <a name="passing-a-static-value"></a>傳遞靜態值
現在我們來考量在包含稱為「文件範例」的靜態值的資料表中，新增另一個名為「案例」的資料行。

![範例資料 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**資料表：**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**預存程序：**

```SQL
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

現在，從預存程序活動傳遞**案例**參數和值。 在前述範例中，**typeProperties** 區段看起來像下列程式碼片段：

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory 資料集：**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory 管線**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
