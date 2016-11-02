<properties
    pageTitle="適用於 Data Factory 服務的所有主題 | Microsoft Azure"
    description="適用於名為 Data Factory 之 Azure 服務的所有主題表格，此表格位於 http://azure.microsoft.com/documentation/articles/ 的標題和說明中。"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>



# <a name="all-topics-for-azure-data-factory-service"></a>適用於 Azure Data Factory 服務的所有主題

本主題會列出每一個直接適用於 Azure **Data Factory** 服務的主題。 您可以使用 **Ctrl+F**，利用關鍵字搜尋本網頁，以尋找目前感興趣的主題。




## <a name="new"></a>新增

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 1 | [使用 Azure Data Factory 從 Amazon Redshift 移動資料](data-factory-amazon-redshift-connector.md) | 了解如何使用 Azure Data Factory 從 Amazon Redshift 移動資料。 |
| 2 | [使用 Azure Data Factory 從 Amazon Simple Storage Service 移動資料](data-factory-amazon-simple-storage-service-connector.md) | 了解如何使用 Azure Data Factory 從 Amazon Simple Storage Service (S3) 移動資料。 |
| 3 | [Azure Data Factory - 複製精靈](data-factory-azure-copy-wizard.md) | 了解如何使用 Azure Data Factory 複製精靈，將資料從支援的資料來源複製到接收。 |
| 4 | [教學課程：使用 Data Factory REST API 建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-rest-api.md) | 在本教學課程中，您會使用 Data Factory REST API 建立範例 Azure Data Factory 管線。 |
| 5 | [教學課程：使用 .NET API 建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-dotnet-api.md) | 在本教學課程中，您會使用 .NET API，建立具有複製活動的 Azure Data Factory 管線。 |
| 6 | [教學課程：使用 REST API 建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-rest-api.md) | 在本教學課程中，您會使用 REST API，建立具有複製活動的 Azure Data Factory 管線。 |
| 7 | [Data Factory 複製精靈](data-factory-copy-wizard.md) | 了解如何使用 Data Factory 複製精靈，將資料從支援的資料來源複製到接收。 |
| 8 | [資料管理閘道](data-factory-data-management-gateway.md) | 設定資料閘道器以在內部部署與雲端之間移動資料。 使用 Azure Data Factory 中的資料管理閘道器移動資料。 |
| 9 | [使用 Azure Data Factory 從內部部署的 Cassandra 資料庫移動資料](data-factory-onprem-cassandra-connector.md) | 了解如何使用 Azure Data Factory 從內部部署的 Cassandra 資料庫移動資料。 |
| 10 | [使用 Azure Data Factory 從 MongoDB 移動資料](data-factory-on-premises-mongodb-connector.md) | 了解如何使用 Azure Data Factory 從 MongoDB 資料庫移動資料。 |
| 11 | [使用 Azure Data Factory 從 Salesforce 移動資料](data-factory-salesforce-connector.md) | 了解如何使用 Azure Data Factory 從 Salesforce 移動資料。 |


## <a name="updated-articles,-data-factory"></a>更新的文章，Data Factory

本節列出具有重大更新內容的最近更新文章。 針對每篇更新的文章，會顯示一段新增的 Markdown 文字概略程式碼片段。 文章的更新日期範圍是在 **2016-08-22** 到 **2016-10-05**。

| &nbsp; | 文章 | 更新的文字、程式碼片段 | 更新時機 |
| --: | :-- | :-- | :-- |
| 12 | [Azure Data Factory - .NET API 變更記錄](data-factory-api-change-log.md) | 本文章提供 Azure Data Factory SDK 在特定版本中有何變更的相關資訊。 您可以在這裡找到 Azure Data Factory 的最新 NuGet 套件 (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) ** 版本 4.11.0** 新增功能：/ 已新增下列連結服務類型：-  OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx)    -  AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx)   -  AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / 已新增下列資料集類型：-  MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx)  -  AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / 已新增下列複製來源類型：-  MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) ** 版本 4.10.0** / 已將下列選用屬性新增到 TextFormat：-  Ski | 2016-09-22 |
| 13 | [使用 Azure Data Factory 從 Azure Blob 來回移動資料](data-factory-azure-blob-connector.md) |  /  copyBehavior  /  當來源為 BlobSource 或 FileSystem 時，定義複製行為。  /  **PreserveHierarchy：**保留目標資料夾中的檔案階層。 來源檔案至來源資料夾的相對路徑，與目標檔案至目標資料夾的相對路徑完全相同。.br/..br/.**FlattenHierarchy：**來源資料夾的所有檔案會位於目標資料夾的第一層。 目標檔案會有自動產生的名稱。 .br/..br/.**MergeFiles：(預設值)**將來源資料夾的所有檔案合併為一個檔案。 如果已指定檔案/Blob 名稱，合併檔案名稱會是指定的名稱；否則，就會是自動產生的檔案名稱。  /  No  /  **BlobSource** 也支援這兩個屬性以提供回溯相容性。 / **treatEmptyAsNull**：指定是否將 null 或空字串視為 null 值。 / **skipHeaderLineCount** - 指定需略過多少行。 僅適用於輸入資料集使用 TextFormat 時。 同樣地，**BlobSink** 支援 | 2016-09-28 |
| 14 | [使用 Azure 機器學習服務活動建立預測管線](data-factory-azure-ml-batch-execution-activity.md) | ** Web 服務需要多個輸入** 如果 Web 服務接受多個輸入，請使用 **webServiceInputs** 屬性，而不要使用 **webServiceInput**。 **webServiceInputs** 所參考的資料集也必須包含在活動的 **inputs** 中。 在您的 Azure ML 實驗中，Web 服務輸入和輸出連接埠及全域參數有您可以自訂的預設名稱 ("input1"、"input2")。 您用於 webServiceInputs、webServiceOutputs 及 globalParameters 設定的名稱必須與實驗中的名稱完全相同。 您可以檢視您 Azure ML 端點之 [批次執行說明] 頁面上的範例要求承載，來確認預期的對應。    {       "name": "PredictivePipeline",       "properties": {             "description": "use AzureML model",             "activities":  {                "name": "MLActivity",               "type": "AzureMLBatchExecution",                "description": "prediction analysis on batch input",                "inputs":  {                    "name": "inputDataset1"                 }, {                    "name": "inputDatase | 2016-09-13 |
| 15 | [複製活動的效能及微調指南](data-factory-copy-activity-performance.md) | 1.**建立基準**。 在開發階段，對具有代表性的資料範例使用複製活動來測試您的管線。 您可以使用 Data Factory 配量模型 (data-factory-scheduling-and-execution.md time-series-datasets-and-data-slices) 來限制使用的資料量。  藉由使用 **監視及管理應用程式**來收集執行時間和效能特性。 在 Data Factory 首頁上選擇 [監視及管理]。 在樹狀檢視中選擇 [輸出資料集] 。 在 [活動時段]  清單中選擇 [複製活動執行]。  會列出複製活動的持續時間和所複製資料的大小。 輸送量會列在 [活動時段總管] 中。 若要深入了解該應用程式，請參閱「使用監視及管理應用程式來監視及管理 Azure Data Factory 管線」(data-factory-monitor-manage-app.md)。  ! 活動執行詳細資料 (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn | 2016-09-27 |
| 16 | [教學課程：使用 Visual Studio 建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-visual-studio.md) |   請注意下列幾點：-資料集 **type** 設為 **AzureBlob**。     - **linkedServiceName** 設為 **AzureStorageLinkedService**。 您已在步驟 2 中建立此連結服務。     - **folderPath** 設為 **adftutorial** 容器。 您也可以使用 **fileName** 屬性指定資料夾內 Blob 的名稱。 由於您未指定 Blob 的名稱，容器中所有 Blob 的資料都會被視為輸入資料。    - 格式 **type** 設為 **TextFormat**  - 文字檔中有兩個欄位 **FirstName** 和 **LastName**，以逗號字元 (**columnDelimiter**) 分隔    - **availability** 設為 **hourly** (**frequency** 設為 **hour**，**interval** 設為 **1**)。 因此，Data Factory 會每小時都在您指定之 Blob 容器 (**adftutorial**) 的根資料夾中尋找輸入資料。  如果您未指定**輸入**資料集的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案/Blob 都會被視為 | 2016-09-29 |
| 17 | [使用 Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory](data-factory-create-data-factories-programmatically.md) | 記下應用程式識別碼和密碼 (用戶端密碼)，然後在逐步解說中使用它。 ** 取得 Azure 訂用帳戶 ID 和租用戶識別碼** 如果您的機器上未安裝最新版的 PowerShell，請依照「如何安裝和設定 Azure PowerShell」(../powershell-install-configure.md) 一文中的指示來安裝它。 1. 開啟 Azure PowerShell 並執行下列命令 2。 執行下列命令並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。         Login-AzureRmAccount    如果您只有一個與此帳戶關聯的 Azure 訂用帳戶，您就不需執行下面兩個步驟。 3. 執行下列命令以檢視此帳戶的所有訂用帳戶。       Get-AzureRmSubscription 4。 執行下列命令以選取您要使用的訂用帳戶。 以您的 Azure 訂用帳戶名稱取代 **NameOfAzureSubscription** 。       Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription  /  Set-AzureRmCo | 2016-09-14 |
| 18 | [Azure Data Factory 中的管線及活動](data-factory-create-pipelines.md) |       ,       "start": "2016-07-12T00:00:00Z",    "end": "2016-07-13T00:00:00Z"       }     } 請注意下列幾點：/ 在活動區段中，只會有一個 **type** 設為 **Copy** 的活動。 / 活動的輸入設定為 **InputDataset**，活動的輸出則設定為 **OutputDataset**。 / 在 **typeProperties** 區段中，來源類型指定為 **BlobSource**，接收類型指定為 **SqlSink**。 如需建立此管線的完整逐步解說，請參閱「教學課程：將資料從 Blob 儲存體複製到 SQL Database」(data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 ** 範例轉換管線** 在以下的範例管線中，**activities** 區段中有一個 **HDInsightHive** 類型的活動。 在此範例中，HDInsight Hive 活動 (data-factory-hive-activity.md) 會執行 Azure HDInsight Hadoop 叢集上的 Hive 指令碼檔案，來轉換 Azure Blob 儲存體的資料。  {     "name": "TransformPipeline",    "p | 2016-09-27 |
| 19 | [Azure Data Factory 中的資料轉換](data-factory-data-transformation-activities.md) | Data Factory 支援下列可個別或與其他活動鏈結而新增至管線 (data-factory-create-pipelines.md) 的資料轉換活動。 建立及管理 SQL Server VM。  AZURE.NOTE  如需逐步解說，請參閱「建立可 Hive 轉換的管線」(data-factory-build-your-first-pipeline.md) 一文。 ** HDInsight Hive 活動** Data Factory 管線中的 HDInsight Hive 活動會在您自己或隨選的 Windows/Linux 架構 HDInsight 叢集上執行 Hive 查詢。 如需此活動的詳細資訊，請參閱「Hive 活動」(data-factory-hive-activity.md) 一文。 ** HDInsight Pig 活動** Data Factory 管線中的 HDInsight Pig 活動會在您自己或隨選的 Windows/Linux 架構 HDInsight 叢集上執行 Pig 查詢。 如需此活動的詳細資訊，請參閱「Pig 活動」(data-factory-pig-activity.md) 一文。 ** HDInsight MapReduce 活動** Data Factory 管線中的 HDInsight MapReduce 活動會在您自己或隨選的 Windows/Linux 架構 | 2016-09-26 |
| 20 | [Data Factory 排程和執行](data-factory-scheduling-and-execution.md) | 唯有當 CopyActivity1 成功執行且 Dataset2 可供使用時，CopyActivity2 才會執行。 以下是範例管線 JSON：{       "name": "ChainActivities",    "properties": {           "description": "Run activities in sequence",      "activities":       {       "type": "Copy",     "typeProperties": {     "source": {     "type": "BlobSource"    },      "sink": {       "type": "BlobSink",     "copyBehavior": "PreserveHierarchy",    "writeBatchSize": 0,    "writeBatchTimeout": "00:00:00"     }       },      "inputs":       {       "name": "Dataset1"      }       ,       "outputs":      {       "name": "Dataset2"      }       ,       "policy": {     "timeout": "01:00:00"       },      "scheduler": {      "frequency": "Hour",    "interval": 1       },      "name": "CopyFromBlob1ToBlob2",     "description": "Copy data from a blob to another"       },      {       "type": "Copy",     "typeProperties": {     "source": {     "type": "BlobSource"    },      "sink": {       "type": "BlobSink",     "writeBatchSize": 0,    "writeBatchTimeout": "00:00:00"     }       },      "in | 2016-09-28 |





## <a name="tutorials"></a>教學課程

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 21 | [教學課程︰使用 Hadoop 叢集建置您的第一個管線來處理資料](data-factory-build-your-first-pipeline.md) | 此 Azure Data Factory 教學課程會示範如何使用 Hadoop 叢集上的 Hive 指令碼，建立和排程處理資料的 Data Factory。 |
| 22 | [教學課程：使用 Azure Resource Manager 範本建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-arm.md) | 在本教學課程中，您會使用 Azure Resource Manager 範本來建立範例 Azure Data Factory 管線。 |
| 23 | [教學課程：使用 Azure 入口網站建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-editor.md) | 在本教學課程中，您會使用 Azure 入口網站中的 Data Factory 編輯器，建立範例 Azure Data Factory 管線。 |
| 24 | [教學課程：使用 Azure PowerShell 建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-powershell.md) | 在本教學課程中，您將使用 Azure PowerShell，建立範例 Azure Data Factory 管線。 |
| 25 | [教學課程：使用 Microsoft Visual Studio 建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-vs.md) | 在本教學課程中，您會使用 Visual Studio，建立範例 Azure Data Factory 管線。 |
| 26 | [教學課程：使用 Azure 入口網站建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-azure-portal.md) | 在本教學課程中，您會使用 Azure 入口網站中的 Data Factory 編輯器，建立具有複製活動的 Azure Data Factory 管線。 |
| 27 | [教學課程：使用 Azure PowerShell 建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-powershell.md) | 在本教學課程中，您會使用 Azure PowerShell，建立具有複製活動的 Azure Data Factory 管線。 |
| 28 | [教學課程：使用 Visual Studio 建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-visual-studio.md) | 在本教學課程中，您會使用 Visual Studio，建立具有複製活動的 Azure Data Factory 管線。 |
| 29 | [使用 Data Factory 將資料從 Blob 儲存體複製到 SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | 本教學課程向您說明如何使用 Azure Data Factory 管線中的複製活動，將資料從 Blob 儲存體複製到 SQL Database。 |
| 30 | [教學課程：使用 Data Factory 複製精靈建立具有複製活動的管線](data-factory-copy-data-wizard-tutorial.md) | 在本教學課程中，您會使用 Data Factory 所支援的複製精靈，建立具有複製活動的 Azure Data Factory 管線。 |



## <a name="data-movement"></a>資料移動

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 31 | [使用 Azure Data Factory 從 Azure Blob 來回移動資料](data-factory-azure-blob-connector.md) | 了解如何在 Azure Data Factory 複製 Blob 資料。 使用我們的範例：如何在 Azure Blob 儲存體和 Azure SQL Database 將資料複製進來和複製出去。 |
| 32 | [使用 Azure Data Factory 從 Azure 資料湖存放區來回移動資料](data-factory-azure-datalake-connector.md) | 了解如何使用 Azure Data Factory 從 Azure 資料湖存放區來回移動資料 |
| 33 | [使用 Azure Data Factory 從 DocumentDB 來回移動資料](data-factory-azure-documentdb-connector.md) | 了解如何使用 Azure Data Factory 從 Azure DocumentDB 集合來回移動資料。 |
| 34 | [使用 Azure Data Factory 從 Azure SQL Database 來回移動資料](data-factory-azure-sql-connector.md) | 了解如何使用 Azure Data Factory 從 Azure SQL 資料庫來回移動資料。 |
| 35 | [使用 Azure Data Factory 從 Azure SQL 資料倉儲來回移動資料](data-factory-azure-sql-data-warehouse-connector.md) | 了解如何使用 Azure Data Factory 從 Azure SQL 資料倉儲來回移動資料 |
| 36 | [使用 Azure Data Factory 從 Azure 資料表來回移動資料](data-factory-azure-table-connector.md) | 了解如何使用 Azure Data Factory 從 Azure 表格儲存體來回移動資料。 |
| 37 | [複製活動的效能及微調指南](data-factory-copy-activity-performance.md) | 了解當您使用複製活動時，Azure Data Factory 中的資料移動效能會受到哪些主要因素的影響。 |
| 38 | [使用複製活動來移動資料](data-factory-data-movement-activities.md) | 了解 Data Factory 管線中的資料移動︰雲端存放區之間和內部部署與雲端之間的資料移轉。 使用「複製活動」。 |
| 39 | [資料管理閘道的版本資訊](data-factory-gateway-release-notes.md) | 資料管理閘道 tory 版本資訊 |
| 40 | [使用 Azure Data Factory 從內部部署的 HDFS 移動資料](data-factory-hdfs-connector.md) | 深入了解如何使用 Azure Data Factory 從內部部署的 HDFS 移動資料 |
| 41 | [使用新的監視及管理應用程式，來監視及管理 Azure Data Factory 管線](data-factory-monitor-manage-app.md) | 了解如何使用監視及管理應用程式來監視及管理 Azure 的資料處理站及管線 |
| 42 | [利用資料管理閘道在內部部署來源和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) | 設定資料閘道器以在內部部署與雲端之間移動資料。 使用 Azure Data Factory 中的資料管理閘道器移動資料。 |
| 43 | [使用 Azure Data Factory 來移動 OData 來源的資料](data-factory-odata-connector.md) | 了解如何使用 Azure Data Factory，來移動 OData 來源的資料。 |
| 44 | [使用 Azure Data Factory 從 ODBC 資料存放區移動資料](data-factory-odbc-connector.md) | 深入了解如何使用 Azure Data Factory 從 ODBC 資料存放區移動資料 |
| 45 | [使用 Azure Data Factory 從 DB2 移動資料](data-factory-onprem-db2-connector.md) | 了解如何使用 Azure Data Factory 從 DB2 資料庫移動資料。 |
| 46 | [使用 Azure Data Factory 將資料移進和移出內部部署檔案系統](data-factory-onprem-file-system-connector.md) | 了解如何使用 Azure Data Factory 將資料移進和移出內部部署檔案系統。 |
| 47 | [使用 Azure Data Factory 從 MySQL 移動資料](data-factory-onprem-mysql-connector.md) | 了解如何使用 Azure Data Factory 從 MySQL Database 移動資料。 |
| 48 | [使用 Azure Data Factory 對內部部署的 Oracle 往返移動資料](data-factory-onprem-oracle-connector.md) | 了解如何使用 Azure Data Factory 從內部部署 Oracle 資料庫來回移動資料。 |
| 49 | [使用 Azure Data Factory 從 PostgreSQL 移動資料](data-factory-onprem-postgresql-connector.md) | 了解如何使用 Azure Data Factory 從 PostgreSQL 資料庫移動資料。 |
| 50 | [使用 Azure Data Factory 從 Sybase 移動資料](data-factory-onprem-sybase-connector.md) | 了解如何使用 Azure Data Factory 從 Sybase 資料庫移動資料。 |
| 51 | [使用 Azure Data Factory 從 Teradata 移動資料](data-factory-onprem-teradata-connector.md) | 了解 Data Factory 服務的 Teradata 連接器，其可讓您從 Teradata 資料庫移動資料 |
| 52 | [使用 Azure Data Factory 從 SQL Server 內部部署或 IaaS (Azure VM) 上來回移動資料](data-factory-sqlserver-connector.md) | 了解如何使用 Azure Data Factory，從內部部署或 Azure VM 中的 SQL Server 資料庫來回移動資料。 |
| 53 | [使用 Azure Data Factory 來移動 Web 資料表的資料](data-factory-web-table-connector.md) | 了解如何使用 Azure Data Factory，來移動網頁上內部部署資料表的資料。 |



## <a name="data-transformation"></a>資料轉換

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 54 | [使用 Azure 機器學習服務活動建立預測管線](data-factory-azure-ml-batch-execution-activity.md) | 說明如何使用 Azure Data Factory 和 Azure Machine Learning 建立預測管線 |
| 55 | [運算連結服務](data-factory-compute-linked-services.md) | 了解您可以在 Azure Data Factory 管線中用來轉換/處理資料的計算環境。 |
| 56 | [使用 Data Factory 和 Batch 處理大型資料集](data-factory-data-processing-using-batch.md) | 說明如何使用 Azure Batch 的平行處理功能處理 Azure Data Factory 管線中的大量資料。 |
| 57 | [Azure Data Factory 中的資料轉換](data-factory-data-transformation-activities.md) | 了解如何使用 Hadoop、Machine Learning 或 Azure Data Lake Analytics 在 Azure Data Factory 中轉換資料或處理資料。 |
| 58 | [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md) | 了解如何使用 Azure Data Factory 中的 Hadoop 串流活動，以在隨選/您自己的 HDInsight 叢集上執行 Hadoop 串流程式。 |
| 59 | [Hive 活動](data-factory-hive-activity.md) | 了解如何使用 Azure 資料處理站中的 Hive 活動，以在隨選/您自己的 HDInsight 叢集上執行 Hive 查詢。 |
| 60 | [從 Data Factory 叫用 MapReduce 程式](data-factory-map-reduce.md) | 了解如何從 Azure Data Factory，在 Azure HDInsight 叢集上執行 MapReduce 程式以處理資料。 |
| 61 | [Pig 活動](data-factory-pig-activity.md) | 了解如何使用 Azure 資料處理站中的 Pig 活動，以在隨選/您自己的 HDInsight 叢集上執行 Pig 指令碼。 |
| 62 | [從 Data Factory 叫用 Spark 程式](data-factory-spark.md) | 了解如何從 Azure Data Factory 使用 MapReduce 活動叫用 Spark 程式。 |
| 63 | [SQL Server 預存程序活動](data-factory-stored-proc-activity.md) | 深入了解如何使用 SQL Server 預存程序活動，以從 Data Factory 管線叫用 Azure SQL Database 或 Azure SQL 資料倉儲中的預存程序。 |
| 64 | [在 Azure 資料處理站管線中使用自訂活動](data-factory-use-custom-activities.md) | 了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。 |
| 65 | [從 Azure Data Factory 在 Azure 資料湖分析上執行 U-SQL 指令碼](data-factory-usql-activity.md) | 了解如何在 Azure 資料湖分析計算服務上執行 U-SQL 指令碼來處理資料。 |



## <a name="samples"></a>範例

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 66 | [Azure Data Factory - 範例](data-factory-samples.md) | 提供 Azure Data Factory 服務隨附範例的詳細資料。 |



## <a name="use-cases"></a>使用案例

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 67 | [客戶案例研究](data-factory-customer-case-studies.md) | 深入了解我們某些客戶使用 Azure Data Factory 的情況。 |
| 68 | [使用案例 - 客戶分析](data-factory-customer-profiling-usecase.md) | 了解如何使用 Azure Data Factory 建立資料驅動的工作流程 (管線) 以分析遊戲客戶。 |
| 69 | [使用案例 - 產品建議](data-factory-product-reco-usecase.md) | 了解使用 Azure Data Factory 以及其他服務所實作的使用案例。 |



## <a name="monitor-and-manage"></a>監視及管理

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 70 | [監視和管理 Azure Data Factory 管線](data-factory-monitor-manage-pipelines.md) | 了解如何使用 Azure 入口網站和 Azure PowerShell 監視並管理您建立的 Azure 資料處理站和管線。 |



## <a name="sdk"></a>SDK

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 71 | [Azure Data Factory - .NET API 變更記錄](data-factory-api-change-log.md) | 描述 Azure Data Factory 的特定版本 NET API 中的重大變更、新增功能、錯誤修正等等... |
| 72 | [使用 Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory](data-factory-create-data-factories-programmatically.md) | 了解如何使用 Data Factory .NET SDK，以程式設計方式建立、監視和管理 Azure Data Factory。 |
| 73 | [Azure Data Factory 開發人員參考](data-factory-sdks.md) | 學習各種建立、監視和管理 Azure 資料處理站的方法。 |



## <a name="miscellaneous"></a>其他資訊

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 74 | [Azure 資料處理站-常見問題集](data-factory-faq.md) | 關於 Azure Data Factory 的常見問題。 |
| 75 | [Azure Data Factory - 函式與系統變數](data-factory-functions-variables.md) | 提供 Azure Data Factory 函式與系統變數清單 |
| 76 | [Azure Data Factory - 命名規則](data-factory-naming-rules.md) | 描述 Data Factory 實體的命名規則。 |
| 77 | [對 Data Factory 問題進行疑難排解](data-factory-troubleshoot.md) | 了解如何使用 Azure Data Factory 進行問題的疑難排解。 |




<!--HONumber=Oct16_HO2-->


