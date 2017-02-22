---
title: "使用 Azure .NET SDK 管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何管理資料湖分析工作、資料來源、使用者。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 3ed1b4121e8e64b07abaeb1117f2b8a0cfd75406
ms.openlocfilehash: 7b2380e45c62684ed29fe819db7e254b968d55d0


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>使用 Azure .NET SDK 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

瞭解如何使用 Azure .NET SDK 管理 Azure Data Lake Analytics 的帳戶、資料來源、使用者和作業。 若要使用其他工具查看管理主題，請按一下上方的索引標籤選取器。

**必要條件**

開始進行本教學課程之前，您必須具備下列必要條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="create-an-azure-resource-group"></a>建立 Azure 資源群組
如果您尚未建立，您必須具有 Azure 資源群組來建立 Data Lake Analytics 元件。 下列程式碼示範如何建立資源群組：

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

如需詳細資訊，請參閱 [Azure 資源群組和 Data Lake Analytics](## Azure Resource Groups and Data Lake Analytics)。


## <a name="connect-to-azure-data-lake"></a>連接到 Azure Data Lake
您需要下列 Nuget 套件：

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


下列程式碼範例的主要方法顯示如何連接到 Azure，並初始化 Analytics 帳戶和 Store 帳戶的 Data Lake 用戶端管理物件。

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Data Lake 用戶端管理物件
Azure Data Lake SDK 包含您從中執行大部分程式設計中，且在這兩個命名空間中的用戶端管理物件集合︰
* Mirosoft.Azure.Management.DataLake.Analytics
* Microsot.Azure.Management.DataLake.Store

下表列出這些物件和其在這整篇文章中之範例使用的變數。

| 用戶端管理物件                  | 程式碼變數         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Data Lake Store 管理用戶端物件︰
* DataLakeStoreAccountManagementClient - 用來建立和管理 Data Lake Store。
* DataLakeFileSystemAccountManagementClient - 用於檔案系統工作，例如建立資料夾和檔案、上傳檔案、列出檔案、存取 ACL 和認證，並將連結新增至 Azure 儲存體 Blob。

雖然您可以從 Data Lake 建立 Azure 儲存體的連結，但您無法存取其內容。 若要這樣做，您必須使用 Azure 儲存體 SDK API。 但是，您可以執行 Azure 儲存體 blob 上的 U-SQL 指令碼。

### <a name="data-lake-analytics-management-client-objects"></a>Data Lake Analytics 管理用戶端物件︰
* DataLakeAnaylyticsAccountManagementClient - 用來建立和管理 Data Lake Analytic 帳戶。
* DataLakeAnalyticsCatalogManagementClient - 用來設定 SQL Database，包括列出結構描述。
* DataLakeAnalyticsJobManagementClient - 用來建立及管理 U-SQL 作業。

## <a name="create-accounts"></a>建立帳戶
您必須擁有資料湖分析帳戶，才能執行任何資料湖分析工作。 與 Azure HDInsight 不同的是，分析帳戶未執行工作時，您無需支付該帳戶的費用。  您只需支付執行作業時的費用。  如需詳細資訊，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

此外，Data Lake Analytics 帳戶都必須至少擁有一個 Data Lake Store 帳戶。
  
### <a name="create-a-data-lake-store-account"></a>建立 Data Lake Store 帳戶
下列程式碼示範如何建立 Data Lake Store 帳戶。 使用 Create 方法之前，您必須指定位置來定義其參數。

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>建立 Data Lake Analytics 帳戶
下列程式碼示範如何建立 Data Lake Analytics 帳戶。 DataLakeAnalyticsAccountManagementClient 物件的建立方法會針對其中一個參數採用 Data Lake Store 帳戶的集合。 此集合必須填入 DataLakeStoreAccountInfo 物件的執行個體。 在此範例中，這些 DataLakeStoreAccountInfo 物件會取自於 Helper 方法 (AdlaFromAdlsStoreAccounts)。 此外，並非所有訂用帳戶中的 Data Lake Store 帳戶都必須在單一 Data Lake Store 帳戶中，因此這個程式碼會針對核准清單檢查名稱。

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>管理帳戶

### <a name="list-data-lake-store-and-analytic-accounts"></a>列出 Data Lake Store 和 Analytic 帳戶
下列程式碼會列出訂用帳戶中的 Data Lake Store 帳戶。 清單作業不會固定提供物件的所有屬性，且在某些情況下，您需要在物件上執行 Get 作業。
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>取得帳戶
如果帳戶存在，則下列程式碼會使用 DataLakeAnalyticsAccountManagementClient 來傳回 Data Lake Analytics 帳戶。 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

同樣地，您可以相同的方式使用 DataLakeStoreAccountManagementClient (_adlsClient) 來取得 Data Lake Store 帳戶。        
### <a name="delete-an-account"></a>刪除帳戶
下列程式碼片段會刪除 Data Lake Analytics 帳戶 (如果存在)。 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

您也可使用 DataLakeStoreAccountManagementClient 以相同的方式刪除 Data Lake Store 帳戶。

### <a name="get-the-default-data-lake-store-account"></a>取得預設的 Data Lake Store 帳戶
每個 Data Lake Analytics 帳戶都必須擁有預設 Data Lake Store 帳戶。 您可以使用此程式碼來判斷 Analytics 帳戶的預設 Store 帳戶。

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>管理資料來源
資料湖分析目前支援下列資料來源：

* [Azure 資料湖儲存體](../data-lake-store/data-lake-store-overview.md)
* [Azure 儲存體](../storage/storage-introduction.md)

當您建立分析帳戶時，必須指定 Azure 資料湖儲存體帳戶作為預設的儲存體帳戶。 預設的資料湖存放區帳戶是用來儲存工作中繼資料與工作稽核記錄。 建立分析帳戶後，就可以新增其他 Data Lake 儲存體帳戶和 Azure 儲存體帳戶的連結。 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>在 Data Lake 中包含 Azure 儲存體的連結
您可以在 Data Lake 環境中建立 Azure 儲存體部落格的連結。 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>列出 Data Lake 資料來源
下列程式碼會針對指定的 Data Lake Analytics 帳戶列出 Data Lake Store 帳戶和 Data Lake 儲存體帳戶 (適用於 Azure 儲存體)。

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>將檔案上傳至 Data Lake Store 帳戶
下列程式碼會使用 DataLakeStoreFileSystemManagementClient，將本機檔案上傳至 Data Lake Store 帳戶。

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>在 Data Lake Store 帳戶中建立檔案
除了上傳檔案之外，您可以輕鬆地以程式設計方式在 Data Lake Store 帳戶中建立檔案以供分析。 下列程式碼會將 100 個隨機位元組陣列的前四個值寫入 .csv 檔案。

        MemoryStream azMem = new MemoryStream();
        StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

        for (int i = 0; i < 100; i++)
        {
            byte[] gA = Guid.NewGuid().ToByteArray();
            string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
            sw.WriteLine(dataLine);
        }
        sw.Flush();
        azMem.Position = 0;

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>從 Data Lake Store 帳戶複製檔案
下列程式碼使用 DataLakeFileSystemAccountManagementClient 物件示範檔案系統作業。 它會從 Samples/Data/AmbulanceData 目錄將試算表 (.csv) 檔案複製到您電腦上的本機目錄。

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>列出 Azure 儲存體容器
下列程式碼會列出指定 Azure 儲存體帳戶的容器。

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>確認 Azure 儲存體帳戶路徑
下列程式碼會檢查 Azure 儲存體帳戶 (storageAccntName) 是否存在於 Data Lake Analytics 帳戶 (analyticsAccountName)，以及容器 (containerName) 是否存在於 Azure 儲存體帳戶。 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>管理目錄和作業
DataLakeAnalyticsCatalogManagementClient 物件會提供方法來管理每個為 Azure Data Lake Store 提供的 SQL Database。 DataLakeAnalyticsJobManagementClient 會提供方法來提交及管理使用 U-SQL 指令碼在資料庫上執行的作業。

### <a name="list-databases-and-schemas"></a>列出資料庫和結構描述
在您可以列出的幾個項目中，最常見的是資料庫和其結構描述。 下列程式碼會取得資料庫的集合，然後列舉每個資料庫的結構描述。

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
        }
    }

在預設的主要資料庫上執行，此範例的輸出如下︰

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>列出資料表資料行
下列程式碼示範如何使用 Data Lake Analytics 目錄管理用戶端存取資料庫，以列出指定資料表的資料行。

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>列出失敗的作業
下列程式碼列出失敗作業的相關資訊。

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>參考 U-SQL 指令碼中的 Azure 儲存體
下列程式碼是 U-SQL 指令碼的開頭。 此指令碼會指定從 Data Lake Store 帳戶上的檔案讀取資料："/Samples/Data/SearchLog.tsv"

    @searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

若要從連結 Azure 儲存體帳戶上的 blob 讀取資料，您必須使用 blob 的完整 URL，格式如下︰

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

例如，如果原始程式檔 (SearchLog.tsv) 儲存在 "contso_33" 儲存體帳戶中名為「範例」的 blob 容器中，則 FROM 陳述式的路徑為︰

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Azure 資源群組和 Data Lake Analytics
應用程式通常由許多元件組成，例如 Web 應用程式、資料庫、資料庫伺服器、儲存體及協力廠商服務。 Azure Resource Manager 可讓您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。 您可以透過單一、協調的作業，來部署、更新、監視或刪除應用程式的所有資源。 您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。 您可以檢視整個群組的彙總成本，為您的組織釐清計費。 如需詳細資訊，請參閱 [Azure 資源管理員概觀](../azure-resource-manager/resource-group-overview.md)。 

資料湖分析服務可包含下列元件：

* Azure 資料湖分析帳戶
* 必要的預設 Azure 資料湖儲存體帳戶
* 一或多個 Azure Data Lake Analytics 帳戶
* 一或多個 Azure Data Lake Analytics 帳戶，至少需要一個
* 其他連結 Azure Data Lake 儲存體帳戶
* 其他 Azure 儲存體帳戶

您可以在某個資源管理群組下建立上述所有元件，這樣更容易管理。

![Azure 資料湖分析帳戶與儲存體](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

資料湖分析帳戶和相依的儲存體帳戶必須位於相同的 Azure 資料中心。
但資源管理群組可位在不同的資料中心內。  

## <a name="see-also"></a>另請參閱
* [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
* [使用 Azure 入口網站開始使用 Data Lake Analytics](data-lake-analytics-get-started-portal.md)
* [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Feb17_HO1-->


