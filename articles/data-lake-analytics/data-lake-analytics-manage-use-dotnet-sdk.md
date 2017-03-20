---
title: "使用 Azure .NET SDK 管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何管理 Data Lake Analytics 工作、資料來源、使用者。 "
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
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>使用 Azure .NET SDK 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

瞭解如何使用 Azure .NET SDK 管理 Azure Data Lake Analytics 的帳戶、資料來源、使用者和作業。 若要使用其他工具查看管理主題，請按一下上方的索引標籤選取器。

## <a name="prerequisites"></a>必要條件

* **已安裝 Visual Studio 2015、Visual Studio 2013 更新 4，或具有 Visual C++ 的 Visual Studio 2012**。
* **Microsoft Azure SDK for .NET 2.5 版或更新版本**。  使用 [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx)來進行安裝。
* **需要新增 Nuget 套件**

### <a name="install-nuget-packages"></a>安裝 NuGet 套件
   
   1. 在 Visual Studio 中，於 [方案總管] 中專案的名稱上按一下滑鼠右鍵，然後按一下 [管理 NuGet 套件]。
   2. 在 [Nuget 封裝管理員] 索引標籤中，確定 [封裝來源] 設為 [nuget.org]，且已選取 [包含發行前版本] 核取方塊。

   3. 搜尋並安裝下列 NuGet 封裝：

    - Microsoft.Rest.ClientRuntime.Azure.Authentication - 本教學課程使用 V2.2.12
    - Microsoft.Azure.Management.DataLake.Analytics - 本教學課程使用 V2.1.0 預覽
    - Microsoft.Azure.Management.DataLake.Store - 本教學課程使用 V2.1.0 預覽

   4. 關閉 [ **Nuget 封裝管理員**]。

## <a name="client-management-objects"></a>用戶端管理物件
Azure Data Lake Analytics 和 Azure Data Lake Store API 包含幾組用戶端管理物件，您可以從這些物件執行大多數的程式設計。 這些物件位於兩個命名空間：
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

下表顯示用戶端管理物件，包含在這整篇文章中於它們的程式碼範例中使用的變數。

| 用戶端管理物件                  | 程式碼變數        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Data Lake Store 管理用戶端物件︰
* DataLakeStoreAccountManagementClient - 用來建立和管理 Data Lake Store 帳戶。
* DataLakeFileSystemAccountManagementClient - 用於檔案系統工作，例如建立資料夾和檔案、上傳檔案、列出檔案、存取 ACL 和認證，並將連結新增至 Azure 儲存體 Blob。

雖然您可以從 Data Lake 建立 Azure 儲存體的連結，但您無法存取其內容。 若要這樣做，您必須使用 Azure 儲存體 SDK API。 但是，您可以執行 Azure 儲存體 blob 上的 U-SQL 指令碼。

### <a name="data-lake-analytics-management-client-objects"></a>Data Lake Analytics 管理用戶端物件︰
* DataLakeAnalyticsAccountManagementClient - 用來建立和管理 Data Lake Analytics 帳戶。
* DataLakeAnalyticsCatalogManagementClient - 用來探索 Data Lake Analytics 中的目錄項目。
* DataLakeAnalyticsJobManagementClient - 送出及管理 Data Lake Analytics 中的工作。

### <a name="example"></a>範例

請使用透過您慣用的驗證方法取得的認證 (本文接下來將會說明)，將用戶端管理物件初始化。 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>驗證並連接到 Azure Data Lake Analytics
您有多個可登入 Azure Data Lake Analytics 的選項。

### <a name="interactive-with-provided-credentials"></a>使用提供的認證以互動方式執行
下列程式碼片段示範由使用者提供認證 (例如使用者名稱和密碼或 PIN 號碼) 的最簡單驗證。

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

建議您在 Azure Active Directory 租用戶中建立自己的應用程式和服務主體，然後將用戶端識別碼用於該應用程式，而不是使用此處所用的範例識別碼。

### <a name="non-interactive-with-a-client-secret"></a>使用用戶端密碼以非互動方式執行
您可以使用下列程式碼片段，藉由應用程式/服務主體的用戶端密碼/金鑰，以非互動方式驗證您的應用程式。 請將此驗證選項用於現有的 [Azure AD「Web 應用程式」應用程式](../azure-resource-manager/resource-group-create-service-principal-portal.md)。

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>使用服務主體以非互動方式執行
第三個選項，下列程式碼片段可供使用應用程式 / 服務主體的憑證，以非互動方式驗證您的應用程式。 請將此驗證選項用於現有的 [Azure AD「Web 應用程式」應用程式](../azure-resource-manager/resource-group-create-service-principal-portal.md)。

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>建立帳戶
您必須擁有 Data Lake Analytics 帳戶，才能執行任何 Data Lake Analytics 工作。 此外，Data Lake Analytics 帳戶都必須至少擁有一個 Data Lake Store 帳戶。 如需詳細資訊，請參閱 [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)

### <a name="create-an-azure-resource-group"></a>建立 Azure 資源群組
如果您尚未建立，您必須具有 Azure 資源群組來建立 Data Lake Analytics 元件。 您將需要您的驗證認證、訂用帳戶 ID 及一個位置。 下列程式碼示範如何建立資源群組：

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

如需詳細資訊，請參閱 [Azure 資源群組和 Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics)。


### <a name="create-a-data-lake-store-account"></a>建立 Data Lake Store 帳戶
下列程式碼示範如何建立 Data Lake Store 帳戶。 使用 Create 方法之前，您必須指定位置來定義其參數。

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>建立 Data Lake Analytics 帳戶
下列程式碼示範如何使用非同步方法來建立 Data Lake Analytics 帳戶。 CreateAsync 方法會採用 Data Lake Store 帳戶集合作為它的其中一個參數。 此集合必須填入 DataLakeStoreAccountInfo 物件的執行個體。 在此範例中，這些 DataLakeStoreAccountInfo 物件會取自於 Helper 方法 (AdlaFromAdlsStoreAccounts)。

針對任何 Data Lake Analytics 帳戶，您都只需要包含執行必要分析所需的 Data Lake Store 帳戶。 這些 Data Lake Store 帳戶其中之一必須是預設的 Data Lake Store 帳戶。

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>管理帳戶

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>列出 Data Lake Store 和 Data Lake Analytics 帳戶
下列程式碼會列出訂用帳戶中的 Data Lake Store 帳戶。 清單作業不會固定提供物件的所有屬性，且在某些情況下，您需要在物件上執行 Get 作業。

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>取得帳戶
下列程式碼會使用 DataLakeAnalyticsAccountManagementClient 來取得 Data Lake Analytics 帳戶。 首先會執行檢查，以確認帳戶是否存在。

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

同樣地，您可以相同的方式使用 DataLakeStoreAccountManagementClient (adlsClient) 來取得 Data Lake Store 帳戶。

### <a name="delete-an-account"></a>刪除帳戶
下列程式碼會刪除 Data Lake Analytics 帳戶 (如果存在)。

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

您也可使用 DataLakeStoreAccountManagementClient 以相同的方式刪除 Data Lake Store 帳戶。

### <a name="get-the-default-data-lake-store-account"></a>取得預設的 Data Lake Store 帳戶
每個 Data Lake Analytics 帳戶都必須擁有預設 Data Lake Store 帳戶。 您可以使用此程式碼來判斷 Analytics 帳戶的預設 Store 帳戶。

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>管理資料來源
Data Lake Analytics 目前支援下列資料來源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 儲存體帳戶](../storage/storage-introduction.md)

當您建立 Analytics 帳戶時，必須指定一個 Azure Data Lake Store 帳戶作為預設的 Data Lake Store 帳戶。 預設的 Data Lake Store 帳戶是用來儲存工作中繼資料與工作稽核記錄。 建立 Analytics 帳戶之後，您可以新增其他 Data Lake Store 以及「Azure 儲存體」(Blob) 帳戶的連結。

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>從 Data Lake Analytics 帳戶連結至 Azure 儲存體帳戶
您可以建立「Azure 儲存體」帳戶的連結。

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>列出 Data Lake Store 資料來源
下列程式碼會列出用於指定之 Data Lake Analytics 帳戶的 Data Lake Store 帳戶和「Azure 儲存體」帳戶。

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>上傳及下載資料夾和檔案
您可以使用 Data Lake Store 檔案系統用戶端管理物件，透過下列方法，將個別的檔案或資料夾上傳至 Azure 及從 Azure 下載到您的本機電腦：

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

這些方法的第一個參數是 Data Lake Store 帳戶的名稱，後面接著來源路徑和目的地路徑的參數。

下列範例示範如何下載 Data Lake Store 中的資料夾。


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>在 Data Lake Store 帳戶中建立檔案
您可以使用 .NET Framework IO 作業來為 Data Lake Store 中的檔案建立內容。 下列程式碼會將 100 個隨機位元組陣列的前四個值寫入 .csv 檔案。

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

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>列出 Azure 儲存體帳戶的 Blob 容器
下列程式碼會列出所指定「Azure 儲存體」帳戶的容器。

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>確認 Azure 儲存體帳戶路徑
下列程式碼會檢查 Azure 儲存體帳戶 (storageAccntName) 是否存在於 Data Lake Analytics 帳戶 (analyticsAccountName)，以及容器 (containerName) 是否存在於 Azure 儲存體帳戶。

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>管理目錄和作業
DataLakeAnalyticsCatalogManagementClient 物件會提供方法來管理每個為 Azure Data Lake Store 提供的 SQL Database。 DataLakeAnalyticsJobManagementClient 會提供方法來提交及管理使用 U-SQL 指令碼在資料庫上執行的作業。

### <a name="list-databases-and-schemas"></a>列出資料庫和結構描述
在您可以列出的數個項目中，最常見的是資料庫及其結構描述。 下列程式碼會取得資料庫的集合，然後列舉每個資料庫的結構描述。

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
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

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>列出失敗的作業
下列程式碼列出失敗作業的相關資訊。

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>另請參閱
* [Microsoft Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [使用 Azure 入口網站開始使用 Data Lake Analytics](data-lake-analytics-get-started-portal.md)
* [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

