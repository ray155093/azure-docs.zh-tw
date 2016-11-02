<properties 
    pageTitle="教學課程：使用 REST API 建立具有複製活動的管線 | Microsoft Azure" 
    description="在本教學課程中，您會使用 REST API，建立具有複製活動的 Azure Data Factory 管線。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/16/2016" 
    ms.author="spelluru"/>


# <a name="tutorial:-create-a-pipeline-with-copy-activity-using-rest-api"></a>教學課程：使用 REST API 建立具有複製活動的管線
> [AZURE.SELECTOR]
- [概觀和必要條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [複製精靈](data-factory-copy-data-wizard-tutorial.md)
- [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure Resource Manager 範本](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


本教學課程示範如何使用 REST API 建立和監視 Azure Data Factory。 Data Factory 中的管線會使用複製活動將資料從 Azure Blob 複製到 Azure SQL Database。

> [AZURE.NOTE] 
> 這篇文章並未涵蓋所有的 Data Factory REST API。 如需 Data Factory Cmdlet 的完整文件，請參閱 [Data Factory REST API 參考](https://msdn.microsoft.com/library/azure/dn906738.aspx) 。
  

## <a name="prerequisites"></a>必要條件

- 請檢閱 [教學課程概觀](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 並完成 **必要** 步驟。
- 在您的電腦上安裝 [Curl](https://curl.haxx.se/dlwiz/) 。 您可搭配使用 Curl 工具與 REST 命令來建立 Data Factory。 
- 請依照 [本文](../resource-group-create-service-principal-portal.md) 的指示： 
    1. 在 Azure Active Directory 中建立名為 **ADFCopyTutorialApp** 的 Web 應用程式。
    2. 取得**用戶端識別碼**和**秘密金鑰**。 
    3. 取得 **租用戶識別碼**。 
    4. 將 **ADFCopyTutorialApp** 應用程式指派給 **Data Factory 參與者**角色。  
- 安裝 [Azure PowerShell](../powershell-install-configure.md)。  
- 啟動 **PowerShell** 並執行下列命令。 將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。
    1. 執行下列命令並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。
    
            Login-AzureRmAccount   
    2. 執行下列命令以檢視此帳戶的所有訂用帳戶。

            Get-AzureRmSubscription 
    3. 執行下列命令以選取您要使用的訂用帳戶。 以您的 Azure 訂用帳戶名稱取代 **&lt;NameOfAzureSubscription**&gt;。 

            Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    1. 在 PowerShell 中執行以下命令，建立名為 **ADFTutorialResourceGroup** 的 Azure 資源群組。  

            New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

        如果資源群組已存在，您可指定是否要更新 (Y) 或予以保留 (N)。 

        本教學課程的某些步驟假設您使用名為 ADFTutorialResourceGroup 的資源群組。 如果使用不同的資源群組，您必須以資源群組的名稱取代本教學課程中的 ADFTutorialResourceGroup。
  
## <a name="create-json-definitions"></a>建立 JSON 定義
在 curl.exe 所在的資料夾中建立下列 JSON 檔案。 

### <a name="datafactory.json"></a>datafactory.json 
> [AZURE.IMPORTANT] 名稱必須是全域唯一的，所以建議您使用 ADFCopyTutorialDF 做為前置詞/後置詞，使其成為唯一的名稱。 

    {  
        "name": "ADFCopyTutorialDF",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservice.json"></a>azurestoragelinkedservice.json
> [AZURE.IMPORTANT] 以 Azure 儲存體帳戶的名稱和金鑰取代 **accountname** 和 **accountkey**。 若要了解如何取得儲存體存取金鑰，請參閱 [檢視、複製和重新產生儲存體存取金鑰](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

### <a name="azuersqllinkedservice.json"></a>azuersqllinkedservice.json
> [AZURE.IMPORTANT] 將 **servername**、**databasename**、**username** 和 **password** 替換為您的 Azure SQL Server 名稱、SQL Database 名稱、使用者帳戶及帳戶的密碼。  

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }


### <a name="inputdataset.json"></a>inputdataset.json

    {
      "name": "AzureBlobInput",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

JSON 定義會定義名為 **AzureBlobInput**的資料集，以表示管線中活動的輸入資料。 此外，它也會指定將輸入資料放在 Blob 容器 **adftutorial** 的 **emp.txt** 檔案中。 

 請注意下列幾點： 

- 資料集 **type** 設為 **AzureBlob**。
- **linkedServiceName** 設為 **AzureStorageLinkedService**。 
- **folderPath** 設為 **adftutorial** 容器，而 **fileName** 設為 **emp.txt**。  
- 格式 **type** 設為 **TextFormat**
- 文字檔中有兩個欄位 (**FirstName** 和 **LastName**)，以逗號字元分隔 (**columnDelimiter**) 
- **availability** 設定為**每小時**，且 (frequency 設定為小時，interval 設定為 1)。 因此，Data Factory 會每小時都在指定之 Blob 容器 (**adftutorial**) 的根資料夾中尋找輸入資料。 

如果您未指定輸入資料集的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案/Blob 都會被視為輸入。 如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。

如果您未指定**輸出資料表**的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：Data.&lt;Guid&gt;.txt (範例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 **partitionedBy** 屬性。 在下列範例中，folderPath 使用 SliceStart (所處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。 例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。 

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],


### <a name="outputdataset.json"></a>outputdataset.json
    
    {
      "name": "AzureSqlOutput",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


JSON 定義會定義名為 **AzureSqlOutput**的資料集，以表示管線中活動的輸出資料。 此外，它也會指定將結果存放在 AzureSqlLinkedService 所代表的資料庫的 **emp** 資料表中。 **availability** 區段指定每小時產生一次輸出資料集 (頻率：小時，間隔：1)。

請注意下列幾點： 

- 資料集 **type** 設為 **AzureSQLTable**。
- **linkedServiceName** 設為 **AzureSqlLinkedService**。
- **tablename** 設為 **emp**。
- 資料庫的 emp 資料表中有三個資料行 – **ID**、**FirstName** 和 **LastName**。 ID 是識別資料行，所以您只需在此指定 **FirstName** 和 **LastName**。
- **availability** 設定為**每小時**，且 (**frequency** 設定為**小時**，**interval** 設定為 **1**)。  Data Factory 服務會每隔一小時在 Azure SQL Database 的 **emp** 資料表中產生輸出資料配量。

### <a name="pipeline.json"></a>pipeline.json

    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
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
        "start": "2016-08-12T00:00:00Z",
        "end": "2016-08-13T00:00:00Z"
      }
    }


請注意下列幾點：

- 在活動區段中，只會有一個活動的 **type** 設為 **CopyActivity**。
- 活動的輸入設為 **AzureBlobInput**，活動的輸出則設為 **AzureSqlOutput**。
- 在 **transformation** 區段中，來源類型指定為 **BlobSource**，接收類型指定為 **SqlSink**。

將 **start** 屬性的值替換為目前日期，並將 **end**值替換為隔天的日期。 在日期時間中，您只指定日期部分，並略過時間部分。 例如，"2015-02-03"，這相當於 "2015-02-03T00:00:00Z"

開始和結束日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 **end** 時間為選擇性項目，但在本教學課程中會用到。 

如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。 若要無限期地執行管線，請指定 **9999-09-09** 做為 **end** 屬性的值。

在此範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
    
> [AZURE.NOTE] 如需上述範例中使用的 JSON 屬性的詳細資訊，請參閱 [管線的剖析](data-factory-create-pipelines.md#anatomy-of-a-pipeline) 。

## <a name="set-global-variables"></a>設定全域變數

在 Azure PowerShell 中，將值取代為您自己的值之後，執行下列命令：

> [AZURE.IMPORTANT] 如需取得用戶端識別碼、用戶端密碼、租用戶識別碼及訂用帳戶識別碼的指示，請參閱 [必要條件](#prerequisites) 一節。   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "ADFCopyTutorialDF"

## <a name="authenticate-with-aad"></a>使用 AAD 驗證 
執行下列命令來驗證 Azure Active Directory (AAD)。 

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 

## <a name="create-data-factory"></a>建立 Data Factory

在此步驟中，您會建立名為 **ADFCopyTutorialDF**的 Azure Data Factory。 資料處理站可以有一或多個管線。 其中的管線可以有一或多個活動。 例如，「複製活動」會從來源複製資料到目的地資料存放區。 HDInsight Hive 活動則是執行 Hive 指令碼轉換輸入資料以產生輸出資料。 執行以下命令以建立 Data Factory： 

1. 將命令指派給名為 **cmd**的變數。 

    確認您在此指定的名稱 (ADFCopyTutorialDF) 符合在 **datafactory.json**指定的名稱。 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. 使用 **Invoke-Command**執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果已成功建立 Data Factory，您會在 **結果**中看到 Data Factory 的 JSON；不然，您會看到一則錯誤訊息。  

        Write-Host $results

請注意下列幾點：
 
- Azure Data Factory 的名稱在全域必須是唯一的。 如果您在結果中看到錯誤︰「Data factory 名稱 "ADFCopyTutorialDF" 無法使用」 ，請執行下列步驟︰  
    1. 在 **datafactory.json** 檔案中變更名稱 (例如，yournameADFCopyTutorialDF)。
    2. 在指派 **$cmd** 變數值的第一個命令中，以新的名稱取代 ADFCopyTutorialDF 並執行命令。 
    3. 執行下面兩個命令來叫用 REST API，以建立 Data Factory 和列印作業的結果。 
    
    請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md) 主題，以了解 Data Factory 成品的命名規則。
- 若要建立 Data Factory 執行個體，您必須是 Azure 訂用帳戶的參與者/系統管理員
- Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。
- 如果您收到錯誤：「此訂用帳戶未註冊為使用命名空間 Microsoft.DataFactory」，請執行下列其中一項，然後嘗試再次發佈︰ 

    - 在 Azure PowerShell 中，執行下列命令以註冊 Data Factory 提供者。 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        您可以執行下列命令來確認已註冊 Data Factory 提供者。 
    
            Get-AzureRmResourceProvider
    - 使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com) 並瀏覽至 [Data Factory] 刀鋒視窗 (或) 在 Azure 入口網站中建立 Data Factory。 此動作會自動為您註冊提供者。

建立管線之前，您必須先建立一些 Data Factory 項目。 您會先建立連結服務，將來源和目的地資料存放區連結至您的資料存放區。 然後，定義輸入和輸出資料集，以表示連結資料存放區中的資料。 最後，建立會使用這些資料集之活動的管線。

## <a name="create-linked-services"></a>建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。 資料存放區可以是 Azure 儲存體、Azure SQL Database 或內部部署 SQL Server 資料庫，且內含 Data Factory 管線的輸入資料或儲存其輸出資料。 計算服務是處理輸入資料並產生輸出資料的服務。 

在此步驟中，您將建立兩個連結服務：**AzureStorageLinkedService** 和 **AzureSqlLinkedService**。 AzureStorageLinkedService 連結服務會連結 Azure 儲存體帳戶，而 AzureSqlLinkedService 會將 Azure SQL Database 連結至 Data Factory： **ADFCopyTutorialDF**。 您稍後會在本教學課程中建立管線，以將資料從 AzureStorageLinkedService 中的 Blob 容器複製到 AzureSqlLinkedService 中的 SQL 資料表。

### <a name="create-azure-storage-linked-service"></a>建立 Azure 儲存體連結服務
在此步驟中，您會將您的 Azure 儲存體帳戶連結到您的 Data Factory。 在本教學課程中，您會使用 Azure 儲存體帳戶來存放輸入資料。 

1. 將命令指派給名為 **cmd**的變數。 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. 使用 **Invoke-Command**執行命令。
 
        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果已成功建立連結服務，您會在 **結果**中看到連結服務的 JSON；不然，您會看到一則錯誤訊息。
  
        Write-Host $results

### <a name="create-azure-sql-linked-service"></a>建立 Azure SQL 連結服務
在此步驟中，您會將您的 Azure SQL Database 連結到您的 Data Factory。 在本教學課程中，您會使用相同的 Azure SQL Database 來存放輸出資料。

1. 將命令指派給名為 **cmd**的變數。 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. 使用 **Invoke-Command**執行命令。
 
        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果已成功建立連結服務，您會在 **結果**中看到連結服務的 JSON；不然，您會看到一則錯誤訊息。
  
        Write-Host $results

## <a name="create-datasets"></a>建立資料集

在上一個步驟中，您已建立連結服務 **AzureStorageLinkedService** 和 **AzureSqlLinkedService**，將 Azure 儲存體帳戶和 Azure SQL Database 連結至 Data Factory：**ADFCopyTutorialDF**。 在此步驟中，您會建立資料集，而這些資料集在下一個步驟所建立的管線中代表複製活動的輸入和輸出資料。 

本教學課程中的輸入資料集是指 Azure 儲存體中 AzureStorageLinkedService 所指向的 Blob 容器。 輸出資料集則是指 Azure SQL Database 中 AzureSqlLinkedService 所指向的 SQL 資料表。  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>準備教學課程所需的 Azure Blob 儲存體和 Azure SQL 資料庫
執行下列步驟來準備本教學課程中的 Azure Blob 儲存體和 Azure SQL Database。 
 
* 在 **AzureStorageLinkedService** 所指向的 Azure Blob 儲存體中，建立名為 **adftutorial** 的 Blob 容器。 
* 建立文字檔 **emp.txt**，並以 Blob 形式上傳至 **adftutorial** 容器。 
* 在 **AzureSqlLinkedService** 所指向的 Azure SQL Database中，建立名為 **emp** 的資料表。


1. 啟動 [記事本]，貼上以下文字，並命名為 **emp.txt**，然後儲存至您硬碟上的 **C:\ADFGetStartedPSH** 資料夾。 

        John, Doe
        Jane, Doe
                
2. 使用 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/)這類的工具建立 **adftutorial** 容器，以及將 **emp.txt** 檔案上傳至該容器。

    ![Azure 儲存體總管](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. 使用以下 SQL 指令碼，在您的 Azure SQL Database 中建立 **emp** 資料表。  


        CREATE TABLE dbo.emp 
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

    如果您的電腦上已安裝 SQL Server 2014：請遵循[步驟 2：使用 SQL Server Management Studio 連接到管理 Azure SQL Database 的 SQL Database][sql-management-studio] 文章中的指示，連接到您的 Azure SQL Server，並執行 SQL 指令碼。

    如果不允許您的用戶端存取 Azure SQL Server，則必須將 Azure SQL Server 的防火牆設定成允許從您的電腦 (IP 位址) 存取。 請參閱 [本文](../sql-database/sql-database-configure-firewall-settings.md) 的步驟，為 Azure SQL Server 設定防火牆。
        
### <a name="create-input-dataset"></a>建立輸入資料集 
在此步驟中，您將在 **AzureStorageLinkedService** 連結服務所代表的 Azure 儲存體中，建立指向 Blob 容器的 **AzureBlobInput** 資料集。 此 Blob 容器 (**adftutorial**) 包含的輸入資料位於 **emp.txt** 檔案中。 

1. 將命令指派給名為 **cmd**的變數。 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. 使用 **Invoke-Command**執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果已成功建立資料集，您會在 **結果**中看到資料集的 JSON；不然，您會看到一則錯誤訊息。
  
        Write-Host $results

### <a name="create-output-dataset"></a>建立輸出資料集
在此步驟中，您會建立名為 **AzureSqlOutput**的輸出資料表。 此資料集指向 Azure SQL Database 中 **AzureSqlLinkedService** 所代表的 SQL 資料表 (**emp**)。 管線會將輸入 Blob 中的資料複製到 **emp** 資料表。 

1. 將命令指派給名為 **cmd**的變數。
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. 使用 **Invoke-Command**執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果已成功建立資料集，您會在 **結果**中看到資料集的 JSON；不然，您會看到一則錯誤訊息。
  
        Write-Host $results 

## <a name="create-pipeline"></a>建立管線
在此步驟中，您會建立管線，其中含有使用 **AzureBlobInput** 做為輸入和使用 **AzureSqlOutput** 做為輸出的**複製活動**。

1. 將命令指派給名為 **cmd**的變數。
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. 使用 **Invoke-Command**執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果已成功建立資料集，您會在 **結果**中看到資料集的 JSON；不然，您會看到一則錯誤訊息。  

        Write-Host $results

**恭喜！**  您已成功建立 Azure Data Factory，其中有管線可將資料從 Azure Blob 儲存體複製到 Azure SQL Database。

## <a name="monitor-pipeline"></a>監視管線
在此步驟中，您會使用 Data Factory REST API 來監視管線所產生的配量。

    $ds ="AzureSqlOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;


    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }

執行這些命令，直到您看到配量處於 [就緒] 狀態或 [失敗] 狀態。 當配量處於 [就緒] 狀態時，請檢查您的 Azure SQL Database 的 **emp** 資料表中的輸出資料。 

對於每個配量，來源檔案中有兩個資料列會複製到 Azure SQL Database 中的 emp 資料表。 因此，成功處理所有配量 (處於 [就緒] 狀態) 後，您會在 emp 資料表中看到 24 筆新記錄。 


## <a name="summary"></a>摘要
在本教學課程中，您已使用 REST API 建立要將資料從 Azure Blob 複製到 Azure SQL 資料庫的 Azure Data Factory。 以下是您在本教學課程中執行的高階步驟：  

1.  建立 Azure **Data Factory**。
2.  建立 **連結服務**：
    1. Azure 儲存體連結服務可連結保留輸入資料的 Azure 儲存體帳戶。    
    2. Azure SQL 連結服務可連結保留輸出資料的 Azure SQL Database。 
3.  建立可描述管線輸入資料和輸出資料的 **資料集**。
4.  建立具有複製活動的 **管線** ，以 BlobSource 做為來源並以 SqlSink 做為接收器。 

## <a name="see-also"></a>另請參閱
| 主題 | 說明 |
| :---- | :---- |
| [資料移動活動](data-factory-data-movement-activities.md) | 本文提供您在本教學課程中使用的複製活動詳細資訊。 |
| [排程和執行](data-factory-scheduling-and-execution.md) | 本文說明 Azure Data Factory 應用程式模型的排程和執行層面。 |
| [管線](data-factory-create-pipelines.md) | 本文協助您了解 Azure Data Factory 中的管線和活動，以及如何使用這些來為您的案例或業務建構端對端的資料導向工作流程。 |
| [資料集](data-factory-create-datasets.md) | 本文協助您了解 Azure Data Factory 中的資料集。
| [使用監視應用程式來監視和管理管線](data-factory-monitor-manage-app.md) | 本文說明如何使用監視及管理應用程式，來監視、管理管線及進行偵錯。 



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 


<!--HONumber=Oct16_HO2-->


