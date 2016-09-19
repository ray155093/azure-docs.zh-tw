<properties
	pageTitle="適用於 Data Factory 服務的所有主題 | Microsoft Azure"
	description="適用於名為 Data Factory 之 Azure 服務的所有主題表格，此表格位於 http://azure.microsoft.com/documentation/articles/ 的標題和說明中。"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-factory"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="spelluru"/>


# 適用於 Azure Data Factory 服務的所有主題

本主題會列出每一個直接適用於 Azure **Data Factory** 服務的主題。您可以使用 **Ctrl+F**，利用關鍵字搜尋本網頁，以尋找目前感興趣的主題。




## 新增

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 1 | [使用 Data Factory REST API 建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-rest-api.md) | 在本教學課程中，您會使用 Data Factory REST API 建立範例 Azure Data Factory 管線。 |
| 2 | [教學課程：使用 REST API 建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-rest-api.md) | 在本教學課程中，您會使用 REST API，建立具有複製活動的 Azure Data Factory 管線。 |
| 3 | [Data Factory 複製精靈](data-factory-copy-wizard.md) | 了解如何使用 Data Factory 複製精靈，將資料從支援的資料來源複製到接收。 |
| 4 | [資料管理閘道](data-factory-data-management-gateway.md) | 設定資料閘道器以在內部部署與雲端之間移動資料。使用 Azure Data Factory 中的資料管理閘道器移動資料。 |
| 5 | [使用 Azure Data Factory 從內部部署的 Cassandra 資料庫移動資料](data-factory-onprem-cassandra-connector.md) | 了解如何使用 Azure Data Factory 從內部部署的 Cassandra 資料庫移動資料。 |
| 6 | [使用 Azure Data Factory 從 MongoDB 移動資料](data-factory-on-premises-mongodb-connector.md) | 了解如何使用 Azure Data Factory 從 MongoDB 資料庫移動資料。 |
| 7 | [使用 Azure Data Factory 從 Salesforce 移動資料](data-factory-salesforce-connector.md) | 了解如何使用 Azure Data Factory 從 Salesforce 移動資料。 |


## 更新的文章

本節列出具有重大更新內容的最近更新文章。針對每篇更新的文章，會顯示一段新增的 Markdown 文字概略程式碼片段。文章的更新日期範圍是在 **2016-07-26** 到 **2016-08-21**。

| &nbsp; | 文章 | 更新的文字、程式碼片段 |
| --: | :-- | :-- |
| 8 | [使用 Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory](data-factory-create-data-factories-programmatically.md) | **不使用快顯對話方塊來登入** 上述範例程式碼會啟動一個對話方塊供您輸入 Azure 認證。如果您需要以程式設計的方式登入，而不使用對話方塊，請參閱使用 Azure Resource Manager 驗證服務主體 (resource-group-authenticate-service-principal.md authenticate-service-principal-with-certificate---powershell)。**範例** 建立 GetAuthorizationHeaderNoPopup 方法，如下所示︰public static string GetAuthorizationHeaderNoPopup() { var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings "ActiveDirectoryTenantId" ); var context = new AuthenticationContext(authority.AbsoluteUri); var credential = new ClientCredential(ConfigurationManager.AppSettings "AdfClientId" , ConfigurationManager.AppSettings "AdfClientSecret" ); AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings "WindowsManagementUri" , credential).Result; |
| 9 | [使用複製活動移動資料](data-factory-data-movement-activities.md) | **支援的檔案格式** 複製活動可以在兩個檔案型資料存放區 (例如 Azure Blob、檔案系統和 Hadoop 分散式檔案系統 (HDFS)) 之間原封不動地複製檔案。若要這樣做，您可以在輸入和輸出資料集定義中同時略過格式化區段 (data-factory-create-datasets.md)，如此一來，就不必進行任何序列化/還原序列化作業，而可以有效率地複製資料。複製活動也會以指定格式讀取和寫入檔案︰文字、Avro、ORC 和 JSON。以下是您可以實現的一些複製活動範例︰/ 從 Azure Blob 複製文字 (CSV) 格式的資料並寫入至 Azure SQL / 從檔案系統內部部署環境複製文字 (CSV) 格式的檔案並以 Avro 格式寫入至 Azure Blob / 複製 Azure SQL Database 中的資料並以 ORC 格式寫入至 HDFS 內部部署環境 **.a name="global"../a.全域可用的資料移動** 雖然 Azure Data Factory 僅供在美國西部、美國東部及北歐地區使用，但是為「複製活動」提供技術支援的服務則是在下列區域和地理位置全域可供使用。|
| 10 | [使用 Azure Data Factory 來移動 OData 來源的資料](data-factory-odata-connector.md) | **使用存取內部部署 OData 來源的 Windows 驗證** { "name": "inputLinkedService", "properties": { "type": "OData", "typeProperties": { "url": ".endpoint of on-premises OData source e.g. Dynamics CRM.", "authenticationType": "Windows", "username": "domain\\user", "password": "password", "gatewayName": "mygateway" } } } |





## 教學課程

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 11 | [教學課程︰使用 Hadoop 叢集建置您的第一個管線來處理資料](data-factory-build-your-first-pipeline.md) | 此 Azure Data Factory 教學課程會示範如何使用 Hadoop 叢集上的 Hive 指令碼，建立和排程處理資料的 Data Factory。 |
| 12 | [教學課程：使用 Azure Resource Manager 範本建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-arm.md) | 在本教學課程中，您將使用 Azure Resource Manager 範本，來建立範例 Azure Data Factory 管線。 |
| 13 | [使用 Azure 入口網站/Data Factory 編輯器建置您的第一個 Azure Data Factory。](data-factory-build-your-first-pipeline-using-editor.md) | 在本教學課程中，您會使用 Azure 入口網站中的 Data Factory 編輯器，建立範例 Azure Data Factory 管線。 |
| 14 | [使用 Azure PowerShell 建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-powershell.md) | 在本教學課程中，您將使用 Azure PowerShell，建立範例 Azure Data Factory 管線。 |
| 15 | [使用 Microsoft Visual Studio 建置您的第一個 Azure Data Factory](data-factory-build-your-first-pipeline-using-vs.md) | 在本教學課程中，您會使用 Visual Studio，建立範例 Azure Data Factory 管線。 |
| 16 | [教學課程：使用 Data Factory 編輯器建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-azure-portal.md) | 在本教學課程中，您會使用 Azure 入口網站中的 Data Factory 編輯器，建立具有複製活動的 Azure Data Factory 管線。 |
| 17 | [教學課程：使用 Azure PowerShell 建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-powershell.md) | 在本教學課程中，您會使用 Azure PowerShell，建立具有複製活動的 Azure Data Factory 管線。 |
| 18 | [教學課程：使用 Visual Studio 建立具有複製活動的管線](data-factory-copy-activity-tutorial-using-visual-studio.md) | 在本教學課程中，您會使用 Visual Studio，建立具有複製活動的 Azure Data Factory 管線。 |
| 19 | [使用 Data Factory 將資料從 Blob 儲存體複製到 SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | 本教學課程向您說明如何使用 Azure Data Factory 管線中的複製活動，將資料從 Blob 儲存體複製到 SQL Database。 |
| 20 | [教學課程：使用 Data Factory 複製精靈建立具有複製活動的管線](data-factory-copy-data-wizard-tutorial.md) | 在本教學課程中，您會使用 Data Factory 所支援的複製精靈，建立具有複製活動的 Azure Data Factory 管線。 |



## 資料移動

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 21 | [使用 Azure Data Factory 從 Azure Blob 來回移動資料](data-factory-azure-blob-connector.md) | 了解如何在 Azure Data Factory 複製 Blob 資料。使用我們的範例：如何在 Azure Blob 儲存體和 Azure SQL Database 將資料複製進來和複製出去。 |
| 22 | [使用 Azure Data Factory 從 Azure 資料湖存放區來回移動資料](data-factory-azure-datalake-connector.md) | 了解如何使用 Azure Data Factory 從 Azure 資料湖存放區來回移動資料 |
| 23 | [使用 Azure Data Factory 從 DocumentDB 來回移動資料](data-factory-azure-documentdb-connector.md) | 了解如何使用 Azure Data Factory 從 Azure DocumentDB 集合來回移動資料。 |
| 24 | [使用 Azure Data Factory 從 Azure SQL Database 來回移動資料](data-factory-azure-sql-connector.md) | 了解如何使用 Azure Data Factory 從 Azure SQL 資料庫來回移動資料。 |
| 25 | [使用 Azure Data Factory 從 Azure SQL 資料倉儲來回移動資料](data-factory-azure-sql-data-warehouse-connector.md) | 了解如何使用 Azure Data Factory 從 Azure SQL 資料倉儲來回移動資料 |
| 26 | [使用 Azure Data Factory 從 Azure 資料表來回移動資料](data-factory-azure-table-connector.md) | 了解如何使用 Azure Data Factory 從 Azure 表格儲存體來回移動資料。 |
| 27 | [複製活動的效能及微調指南](data-factory-copy-activity-performance.md) | 了解在 Azure Data Factory 中透過複製活動的資料移動效能會受到哪些主要因素的影響。 |
| 28 | [使用複製活動移動資料](data-factory-data-movement-activities.md) | 了解 Data Factory 管線中的資料移動︰雲端存放區之間和內部部署與雲端之間的資料移轉。使用複製活動。 |
| 29 | [資料管理閘道的版本資訊](data-factory-gateway-release-notes.md) | 資料管理閘道 tory 版本資訊 |
| 30 | [使用 Azure Data Factory 從內部部署的 HDFS 移動資料](data-factory-hdfs-connector.md) | 深入了解如何使用 Azure Data Factory 從內部部署的 HDFS 移動資料 |
| 31 | [使用新的監視及管理應用程式，來監視及管理 Azure Data Factory 管線](data-factory-monitor-manage-app.md) | 了解如何使用監視及管理應用程式來監視及管理 Azure 的資料處理站及管線 |
| 32 | [利用資料管理閘道在內部部署來源和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) | 設定資料閘道器以在內部部署與雲端之間移動資料。使用 Azure Data Factory 中的資料管理閘道器移動資料。 |
| 33 | [使用 Azure Data Factory 來移動 OData 來源的資料](data-factory-odata-connector.md) | 了解如何使用 Azure Data Factory，來移動 OData 來源的資料。 |
| 34 | [使用 Azure Data Factory 從 ODBC 資料存放區移動資料](data-factory-odbc-connector.md) | 深入了解如何使用 Azure Data Factory 從 ODBC 資料存放區移動資料 |
| 35 | [使用 Azure Data Factory 從 DB2 移動資料](data-factory-onprem-db2-connector.md) | 了解如何使用 Azure Data Factory 從 DB2 資料庫移動資料。 |
| 36 | [使用 Azure Data Factory 從內部部署檔案系統來回移動資料](data-factory-onprem-file-system-connector.md) | 了解如何使用 Azure Data Factory 從內部部署檔案系統來回移動資料。 |
| 37 | [使用 Azure Data Factory 從 MySQL 移動資料](data-factory-onprem-mysql-connector.md) | 了解如何使用 Azure Data Factory 從 MySQL Database 移動資料。 |
| 38 | [使用 Azure Data Factory 對內部部署的 Oracle 往返移動資料](data-factory-onprem-oracle-connector.md) | 了解如何使用 Azure Data Factory 從內部部署 Oracle 資料庫來回移動資料。 |
| 39 | [使用 Azure Data Factory 從 PostgreSQL 移動資料](data-factory-onprem-postgresql-connector.md) | 了解如何使用 Azure Data Factory 從 PostgreSQL 資料庫移動資料。 |
| 40 | [使用 Azure Data Factory 從 Sybase 移動資料](data-factory-onprem-sybase-connector.md) | 了解如何使用 Azure Data Factory 從 Sybase 資料庫移動資料。 |
| 41 | [使用 Azure Data Factory 從 Teradata 移動資料](data-factory-onprem-teradata-connector.md) | 了解 Data Factory 服務的 Teradata 連接器，其可讓您從 Teradata 資料庫移動資料 |
| 42 | [使用 Azure Data Factory 從 SQL Server 內部部署或 IaaS (Azure VM) 上來回移動資料](data-factory-sqlserver-connector.md) | 了解如何使用 Azure Data Factory，從內部部署或 Azure VM 中的 SQL Server 資料庫來回移動資料。 |
| 43 | [使用 Azure Data Factory 來移動 Web 資料表的資料](data-factory-web-table-connector.md) | 了解如何使用 Azure Data Factory，來移動網頁上內部部署資料表的資料。 |



## 資料轉換

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 44 | [使用 Azure 機器學習服務活動建立預測管線](data-factory-azure-ml-batch-execution-activity.md) | 說明如何使用 Azure Data Factory 和 Azure Machine Learning 建立預測管線 |
| 45 | [運算連結服務](data-factory-compute-linked-services.md) | 了解您可以在 Azure Data Factory 管線中用來轉換/處理資料的計算環境。 |
| 46 | [使用 Data Factory 和 Batch 處理大型資料集](data-factory-data-processing-using-batch.md) | 說明如何使用 Azure Batch 的平行處理功能處理 Azure Data Factory 管線中的大量資料。 |
| 47 | [了解在 Azure Data Factory 中轉換和分析資料](data-factory-data-transformation-activities.md) | 了解 Azure Data Factory 中的資料轉換。在 Azure HDInsight 叢集或 Azure Batch 中轉換和處理資料。 |
| 48 | [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md) | 了解如何使用 Azure Data Factory 中的 Hadoop 串流活動，以在隨選/您自己的 HDInsight 叢集上執行 Hadoop 串流程式。 |
| 49 | [Hive 活動](data-factory-hive-activity.md) | 了解如何使用 Azure 資料處理站中的 Hive 活動，以在隨選/您自己的 HDInsight 叢集上執行 Hive 查詢。 |
| 50 | [從 Data Factory 叫用 MapReduce 程式](data-factory-map-reduce.md) | 了解如何從 Azure Data Factory，在 Azure HDInsight 叢集上執行 MapReduce 程式以處理資料。 |
| 51 | [Pig 活動](data-factory-pig-activity.md) | 了解如何使用 Azure 資料處理站中的 Pig 活動，以在隨選/您自己的 HDInsight 叢集上執行 Pig 指令碼。 |
| 52 | [從 Data Factory 叫用 Spark 程式](data-factory-spark.md) | 了解如何從 Azure Data Factory 使用 MapReduce 活動叫用 Spark 程式。 |
| 53 | [SQL Server 預存程序活動](data-factory-stored-proc-activity.md) | 深入了解如何使用 SQL Server 預存程序活動，以從 Data Factory 管線叫用 Azure SQL Database 或 Azure SQL 資料倉儲中的預存程序。 |
| 54 | [在 Azure 資料處理站管線中使用自訂活動](data-factory-use-custom-activities.md) | 了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。 |
| 55 | [從 Azure Data Factory 在 Azure 資料湖分析上執行 U-SQL 指令碼](data-factory-usql-activity.md) | 了解如何在 Azure 資料湖分析計算服務上執行 U-SQL 指令碼來處理資料。 |



## 範例

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 56 | [Azure Data Factory - 範例](data-factory-samples.md) | 提供 Azure Data Factory 服務隨附範例的詳細資料。 |



## 使用案例

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 57 | [客戶案例研究](data-factory-customer-case-studies.md) | 深入了解我們某些客戶使用 Azure Data Factory 的情況。 |
| 58 | [使用案例 - 客戶分析](data-factory-customer-profiling-usecase.md) | 了解如何使用 Azure Data Factory 建立資料驅動的工作流程 (管線) 以分析遊戲客戶。 |
| 59 | [使用案例 - 產品建議](data-factory-product-reco-usecase.md) | 了解使用 Azure Data Factory 以及其他服務所實作的使用案例。 |



## 監視及管理

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 60 | [監視和管理 Azure Data Factory 管線](data-factory-monitor-manage-pipelines.md) | 了解如何使用 Azure 入口網站和 Azure PowerShell 監視並管理您建立的 Azure 資料處理站和管線。 |



## SDK

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 61 | [Azure Data Factory - .NET API 變更記錄](data-factory-api-change-log.md) | 描述 Azure Data Factory 的特定版本 NET API 中的重大變更、新增功能、錯誤修正等等... |
| 62 | [使用 Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory](data-factory-create-data-factories-programmatically.md) | 了解如何使用 Data Factory .NET SDK，以程式設計方式建立、監視和管理 Azure Data Factory。 |
| 63 | [Azure Data Factory 開發人員參考](data-factory-sdks.md) | 學習各種建立、監視和管理 Azure 資料處理站的方法。 |



## 其他資訊

| &nbsp; | 課程名稱 | 說明 |
| --: | :-- | :-- |
| 64 | [Azure 資料處理站-常見問題集](data-factory-faq.md) | 關於 Azure Data Factory 的常見問題。 |
| 65 | [Azure Data Factory - 函式與系統變數](data-factory-functions-variables.md) | 提供 Azure Data Factory 函式與系統變數清單 |
| 66 | [Azure Data Factory - 命名規則](data-factory-naming-rules.md) | 描述 Data Factory 實體的命名規則。 |
| 67 | [對 Data Factory 問題進行疑難排解](data-factory-troubleshoot.md) | 了解如何使用 Azure Data Factory 進行問題的疑難排解。 |

<!----HONumber=AcomDC_0907_2016-->