<properties
   pageTitle="使用 Data Lake Store .NET SDK 來開發應用程式 | Microsoft Azure"
   description="使用 Azure 資料湖存放區 .NET SDK 來開發應用程式"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/15/2016"
   ms.author="nitinme"/>

# 使用 .NET SDK 開始使用 Azure 資料湖存放區

> [AZURE.SELECTOR]
- [入口網站](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

了解如何使用 [Azure Data Lake Store .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) 來執行基本作業，例如建立資料夾、上傳和下載資料檔案等等。如需有關資料湖的詳細資訊，請參閱 [Azure Data Lake Store](data-lake-store-overview.md)。

## 必要條件

* **Visual Studio 2013 或 2015**。以下指示使用 Visual Studio 2015。

* **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帳戶**。如需有關如何建立帳戶的指示，請參閱[開始使用 Azure Data Lake Store](data-lake-store-get-started-portal.md)

* 如果您希望您的應用程式自動向 Azure Active Directory 進行驗證，請**建立 Azure Active Directory 應用程式**。

	* **針對非互動式、服務主題驗證** - 在 Azure Active Directory 中，您必須建立 **Web 應用程式**。一旦您建立應用程式，請擷取與下列應用程式相關的值。
		- 取得應用程式的**用戶端識別碼**和**用戶端密碼**
		- 將 Azure Active Directory 應用程式指派給角色。角色可以在您要授與權限給 Azure Active Directory 應用程式的範圍層級。例如，您可以在訂用帳戶層級或資源群組的層級指派應用程式。

	如需有關如何擷取這些值、設定權限和指派角色的指示，請參閱[使用入口網站建立 Active Directory 應用程式和服務主體](../resource-group-create-service-principal-portal.md)。

## 建立 .NET 應用程式

1. 開啟 Visual Studio，建立主控台應用程式。

2. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。

3. 在 [**新增專案**] 中，輸入或選取下列值：

	| 屬性 | 值 |
	|----------|-----------------------------|
	| 類別 | 範本/Visual C#/Windows |
	| 範本 | 主控台應用程式 |
	| 名稱 | CreateADLApplication |

4. 按一下 [確定] 以建立專案。

5. 將 Nuget 封裝新增至您的專案。

	1. 在方案總管中以滑鼠右鍵按一下專案名稱，然後按一下 [**管理 NuGet 封裝**]。
	2. 在 [Nuget 封裝管理員] 索引標籤中，確定 [封裝來源] 設為 [nuget.org]，且已選取 [包含發行前版本] 核取方塊。
	3. 搜尋並安裝下列 NuGet 封裝：

		* `Microsoft.Azure.Management.DataLake.Store` - 本教學課程使用 v0.12.5-preview。
		* `Microsoft.Azure.Management.DataLake.StoreUploader` - 本教學課程使用 v0.10.6-preview。
		* `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 本教學課程使用 v2.2.8-preview。

		![新增 Nuget 來源](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "建立新的 Azure 資料湖帳戶")

	4. 關閉 [**Nuget 封裝管理員**]。

6. 開啟 **Program.cs**，刪除現有的程式碼，然後納入下列陳述式以新增命名空間的參考。

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

7. 宣告如下所示的變數，並提供已存在的 Data Lake Store 名稱和資源群組名稱的值。此外，請確定您在此處提供的本機路徑和檔案名稱必須存在於電腦。將下列程式碼片段加在命名空間宣告之後。

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    
                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

在本文的其餘章節中，您可以了解如何使用可用的 .NET 方法來執行一些作業，例如驗證、檔案上載等。

## 驗證

下列程式碼片段可用於互動式登入體驗。

    // User login via interactive popup
    //    Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"))
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

此外，下列程式碼片段可供使用應用程式 / 服務主體的用戶端密碼 / 金鑰，以非互動方式驗證您的應用程式。

    // Service principal / appplication authentication with client secret / key
    //    Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-clientid>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

第三個選項，下列程式碼片段可供使用應用程式 / 服務主體的憑證，以非互動方式驗證您的應用程式。

    // Service principal / application authentication with certificate
    //    Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## 建立用戶端物件

下列程式碼片段會建立 Data Lake Store 帳戶和檔案系統用戶端物件，以便對服務發出要求。

    // Create client objects
    var fileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## 列出訂用帳戶內的所有 Data Lake Store 帳戶

下列程式碼片段列出指定的 Azure 訂用帳戶中的所有 Data Lake Store 帳戶。

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## 建立目錄

下列程式碼片段顯示的 `CreateDirectory` 方法可用於在 Data Lake Store 帳戶中建立目錄。

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## 上傳檔案

下列程式碼片段顯示的 `UploadFile` 方法可用於將檔案上傳到 Data Lake Store 帳戶。

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

DataLakeStoreUploader 支援本機檔案 (或資料夾) 路徑與 Data Lake Store 之間的遞迴上傳和下載。

## 取得檔案或目錄資訊

下列程式碼片段顯示的 `GetItemInfo` 方法可用於擷取 Data Lake Store 中可用檔案或目錄的相關資訊。

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## 列出檔案或目錄

下列程式碼片段顯示的 `ListItem` 方法可用於列出 Data Lake Store 帳戶中的檔案和目錄。

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## 串連檔案

下列程式碼片段顯示的 `ConcatenateFiles` 方法可用於串連檔案。

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## 附加到檔案

下列程式碼片段顯示的 `AppendToFile` 方法可用於將資料附加到 Data Lake Store 帳戶中已儲存的檔案。

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## 下載檔案

下列程式碼片段顯示的 `DownloadFile` 方法可用於從 Data Lake Store 帳戶下載檔案。

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## 後續步驟

- [保護 Data Lake Store 中的資料](data-lake-store-secure-data.md)
- [搭配 Data Lake Store 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [搭配 Data Lake Store 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Store .NET SDK 參考](https://msdn.microsoft.com/library/mt581387.aspx)
- [Data Lake Store REST 參考](https://msdn.microsoft.com/library/mt693424.aspx)

<!---HONumber=AcomDC_0921_2016--->