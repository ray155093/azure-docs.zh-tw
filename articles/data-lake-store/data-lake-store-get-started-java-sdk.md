<properties
   pageTitle="使用 Data Lake Store Java SDK 來開發應用程式 | Azure"
   description="使用 Azure Data Lake Store Java SDK 來開發應用程式"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# 使用 Java 開始使用 Azure Data Lake Store

> [AZURE.SELECTOR]
- [入口網站](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

了解如何使用 Azure Data Lake Store Java SDK 以建立 Azure Data Lake 帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需有關 Data Lake 的詳細資訊，請參閱 [Azure Data Lake Store](data-lake-store-overview.md)。

## Azure Data Lake Store Java SDK

下列連結適用於 Data Lake Store 的 Java SDK 和 Java SDK 參考的下載位置。在本教學課程中，您不需要下載 SDK 或遵循參考文件。下列連結僅供參考。

* 在 [GitHub](https://github.com/Azure/azure-sdk-for-java) 上可取得適用於 Data Lake Store 的 Java SDK 原始程式碼。
* 在 [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/) 可取得適用於 Data Lake Store 的 Java SDK 參考。

## 必要條件

* Java Development Kit (JDK) 8 (使用 Java 1.8 版)。
* IntelliJ 或其他合適的 Java 開發環境。此為選用步驟，但建議執行。以下指示使用 IntelliJ。
* **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **啟用您的 Azure 訂用帳戶**以使用資料湖存放區公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。
* **建立 Azure Active Directory 應用程式**。有兩種方式可使用 Azure Active Direcotry 來進行驗證 - **互動式**和**非互動式**。根據您要進行驗證的方式，會有不同的必要條件。
	* **針對互動式驗證** - 在 Azure Active Directory 中，您必須建立**原生用戶端應用程式**。一旦您建立應用程式，請擷取與下列應用程式相關的值。
		- 取得應用程式的**用戶端識別碼**和**重新導向 URI**
		- 設定委派權限

	* **針對非互動式驗證** (本文所使用的方式) - 在 Azure Active Directory 中，您必須建立 **Web 應用程式**。一旦您建立應用程式，請擷取與下列應用程式相關的值。
		- 取得應用程式的**用戶端識別碼**、**用戶端密碼**和**重新導向 URI**
		- 設定委派權限
		- 將 Azure Active Directory 應用程式指派給角色。角色可以在您要授與權限給 Azure Active Directory 應用程式的範圍層級。例如，您可以在訂用帳戶層級或資源群組的層級指派應用程式。 

	如需有關如何擷取這些值、設定權限和指派角色的指示，請參閱[使用入口網站建立 Active Directory 應用程式和服務主體](../resource-group-create-service-principal-portal.md)。

## 如何使用 Azure Active Directory 驗證？

下列程式碼片段提供**非互動式**驗證的程式碼，其中應用程式會提供它自己的認證。

您必須提供可在 Azure 中建立資源的應用程式權限，本教學課程才能運作。**強烈建議**您在為了達到本教學課程的目的時，只針對 Azure 訂用帳戶中新的、未使用的和空的資源群組提供此應用程式的參與者權限。

## 建立 Java 應用程式

1. 開啟 IntelliJ，並使用**命令列應用程式**範本建立新的 Java 專案。完成精靈以建立專案。

2. 在畫面左側的專案上按一下滑鼠右鍵，然後按一下 [新增架構支援]。選擇 [Maven] 並按一下 [確定]。

3. 開啟新建立的 **"pom.xml"** 檔案，並在 **</version>** 標記和 **</project>** 標記之間新增下列一小段文字︰

    >[AZURE.NOTE] 此步驟是暫時性的，在 Maven 提供 Azure Data Lake Store SDK 後便不適用。一旦 Maven 提供此 SDK 後便會更新本文。此 SDK 未來的所有更新皆會透過 Maven 提供。

        <repositories>
        	<repository>
	            <id>adx-snapshots</id>
	            <name>Azure ADX Snapshots</name>
	            <url>http://adxsnapshots.azurewebsites.net/</url>
	            <layout>default</layout>
	            <snapshots>
                	<enabled>true</enabled>
            	</snapshots>
        	</repository>
        	<repository>
	            <id>oss-snapshots</id>
	            <name>Open Source Snapshots</name>
	            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
	            <layout>default</layout>
	            <snapshots>
	                <enabled>true</enabled>
	                <updatePolicy>always</updatePolicy>
	            </snapshots>
        	</repository>
    	</repositories>
    	<dependencies>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-authentication</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-store</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
    	</dependencies>


4. 移至 [檔案] > [設定] > [建置] > [執行] > [部署]。展開 [建置工具] > [Maven] > [匯入]。選取 [自動匯入 Maven 專案] 的核取方塊。按一下 [套用]，然後按一下 [確定]。

5. 從左窗格中瀏覽至 [src] > [主要] > [java] > [<封裝名稱>]，然後開啟 **Main.java** 並以下列程式碼取代現有程式碼區塊。此外，請提供程式碼片段中呼叫的參數值 (例如 **localFolderPath**、**\_adlsAccountName**、**\_resourceGroupName**) 並取代 **CLIENT-ID**、**CLIENT-SECRET**、**TENANT-ID** 和 **SUBSCRIPTION-ID** 的預留位置。

    這個程式碼會進行以下程序：建立 Data Lake Store 帳戶、在存放區中建立檔案、串連檔案、下載檔案，並於最後刪除帳戶。

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. 執行應用程式。請依照提示來執行並完成應用程式。

## 後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [搭配資料湖存放區使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [搭配 Data Lake Store 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0518_2016-->