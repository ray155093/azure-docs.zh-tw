---
title: "教學課程：使用 .NET API 建立具有複製活動的管線 | Microsoft Docs"
description: "在本教學課程中，您會使用 .NET API，建立具有複製活動的 Azure Data Factory 管線。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/27/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: d2d3f414d0e9fcc392d21327ef630f96c832c99c
ms.openlocfilehash: 19d1cc75d61a3897c916180afa395bade43d47ec


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>教學課程：使用 .NET API 建立具有複製活動的管線
> [!div class="op_single_selector"]
> * [概觀和必要條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [複製精靈](data-factory-copy-data-wizard-tutorial.md)
> * [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 範本](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

本教學課程示範如何使用 .NET API 建立和監視 Azure Data Factory。 Data Factory 中的管線會使用複製活動將資料從 Azure Blob 複製到 Azure SQL Database。

複製活動會在 Azure Data Factory 中執行資料移動。 此活動是由全域可用的服務所提供，可以使用安全、可靠及可調整的方式，在各種不同的資料存放區之間複製資料。 如需複製活動的詳細資訊，請參閱 [資料移動活動](data-factory-data-movement-activities.md) 文章。

> [!NOTE]
> 這篇文章並未涵蓋所有的 Data Factory .NET API。 請參閱 [Data Factory .NET API 參考](https://msdn.microsoft.com/library/mt415893.aspx) ，以取得有關 Data Factory .NET SDK 的詳細資料。

## <a name="prerequisites"></a>必要條件
* 請檢閱 [教學課程概觀和必要條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，以取得本教學課程的概觀並完成 **必要** 步驟。
* Visual Studio 2012、2013 或 2015
* 下載並安裝 [Azure .NET SDK](http://azure.microsoft.com/downloads/)
* Azure PowerShell。 按照 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 一文中的指示操作，在您的電腦上安裝 Azure PowerShell。 您可以使用 Azure PowerShell 建立 Azure Active Directory 應用程式。

### <a name="create-an-application-in-azure-active-directory"></a>在 Azure Active Directory 中建立應用程式
建立 Azure Active Directory 應用程式、建立該應用程式的服務主體，然後將其指派給 **Data Factory 參與者** 角色。

1. 啟動 **PowerShell**。
2. 執行下列命令並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。

        Login-AzureRmAccount
3. 執行下列命令以檢視此帳戶的所有訂用帳戶。

        Get-AzureRmSubscription
4. 執行下列命令以選取您要使用的訂用帳戶。 以您的 Azure 訂用帳戶名稱取代 **&lt;NameOfAzureSubscription**&gt;。

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

   > [!IMPORTANT]
   > 請記下此命令輸出中的 **SubscriptionId** 和 **TenantId**。

5. 在 PowerShell 中執行以下命令，建立名為 **ADFTutorialResourceGroup** 的 Azure 資源群組。

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    如果資源群組已存在，您可指定是否要更新 (Y) 或予以保留 (N)。

    如果使用不同的資源群組，您必須以資源群組的名稱取代本教學課程中的 ADFTutorialResourceGroup。
6. 建立 Azure Active Directory 應用程式。

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    如果您收到下列錯誤，請指定不同的 URL 並再次執行此命令。

        Another object with the same value for property identifierUris already exists.
7. 建立 AD 服務主體。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
8. 對 **Data Factory 參與者** 角色新增服務主體。

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
9. 取得應用程式識別碼。

        $azureAdApplication

    Note down the application ID (**applicationID** from the output).

您應會從這些步驟取得下列四個值︰

* 租用戶識別碼
* 訂用帳戶識別碼
* 應用程式識別碼
* 密碼 (在第一個命令中指定)

## <a name="walkthrough"></a>逐步介紹
1. 使用 Visual Studio 2012/2013/2015 建立 C# .NET 主控台應用程式。
   1. 啟動 **Visual Studio** 2012/2013/2015。
   2. 按一下 [檔案]，指向 [新增]，然後按一下 [專案]。
   3. 展開 [範本]，然後選取 [Visual C#]。 在此逐步解說中，您使用的是 C#，但您可以使用任何 .NET 語言。
   4. 從右邊的專案類型清單中選取 [主控台應用程式]  。
   5. 在 [名稱] 中輸入 **DataFactoryAPITestApp** 。
   6. 在 [位置] 中選取 **C:\ADFGetStarted**。
   7. 按一下 [確定]  以建立專案。
2. 按一下 [工具]，指向 [Nuget 套件管理員]，然後按一下 [套件管理員主控台]。
3. 在 [Package Manager Console] 中，輸入下列命令：
   1. 執行以下命令安裝 Data Factory 套件：`Install-Package Microsoft.Azure.Management.DataFactories`
   2. 執行下列命令安裝 Azure Active Directory 套件 (您在程式碼中使用 Active Directory API)︰`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. 將下列 **appSetttings** 區段新增 **App.config** 檔案。 以下 Helper 方法會使用這些設定： **Microsoft.identitymodel.waad.preview.graph.graphinterface**。

    以您自己的值取代**&lt;應用程式識別碼&gt;**、**&lt;密碼&gt;**、**&lt;訂用帳戶識別碼&gt;****&lt;租用戶識別碼&gt;**的值。

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <startup>
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
        </startup>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. 將下列 **using** 陳述式加入專案的原始程式檔 (Program.cs) 中。

    ```csharp
    using System.Threading;
    using System.Configuration;
    using System.Collections.ObjectModel;

    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure;
    ```

6. 將下列會建立 **DataPipelineManagementClient** 類別執行個體的程式碼新增至 **Main** 方法中。 您會使用此物件來建立 Data Factory、連結的服務、輸入和輸出資料集，以及管線。 您也會使用此物件來監視執行階段的資料集配量。

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > 以您的 Azure 資源群組名稱取代 **resourceGroupName** 的值。
   >
   > 將 Data Factory 的名稱 (**dataFactoryName**) 更新成唯一的名稱。 Data Factory 的名稱必須是全域唯一的名稱。 請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md) 主題，以了解 Data Factory 成品的命名規則。

7. 將下列會建立 **data Factory** 的程式碼新增至 **Main** 方法中。

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties() { }
            }
        }
    );
    ```

8. 將下列會建立 **Azure 儲存體**的程式碼新增至 **Main** 方法中。

   > [!IMPORTANT]
   > 以 Azure 儲存體帳戶的名稱和金鑰取代 **storageaccountname** 和 **accountkey**。

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

9. 將下列會建立 **Azure SQL 連結服務**的程式碼新增至 **Main** 方法中。

   > [!IMPORTANT]
   > 以您的 Azure SQL 伺服器名稱、資料庫名稱、使用者和密碼取代 **servername**、**databasename**、**username** **password**。

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

10. 將下列會建立**輸入和輸出資料集**的程式碼新增至 **Main** 方法中。

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetAzureSqlDestination";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },

                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```

11. 將下列會**建立並啟用管線**的程式碼新增至 **Main** 方法中。 此管線有一個 **CopyActivity**，它以 **BlobSource** 為來源，**BlobSink** 為接收器。

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

            // Initial value for pipeline's active period. With this, you won't need to set slice status
            Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    },
                }
            }
        });
    ```

12. 將下列程式碼加入 **Main** 方法中，以取得輸出資料集的資料配量狀態。 在此範例中只預期有配量。

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. 將下列會取得資料配量之執行詳細資料的程式碼加入 **Main** 方法中。

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. 將 **Main** 方法所使用的下列 Helper 方法新增至 **Program** 類別中。

    ```csharp
    public static string GetAuthorizationHeader()
    {
        AuthenticationResult result = null;
        var thread = new Thread(() =>
        {
            try
            {
                var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
            }
            catch (Exception threadEx)
            {
                Console.WriteLine(threadEx.Message);
            }
        });

        thread.SetApartmentState(ApartmentState.STA);
        thread.Name = "AcquireTokenThread";
        thread.Start();
        thread.Join();

        if (result != null)
        {
            return result.AccessToken;
        }

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. 在 [方案總管] 中展開專案 (**DataFactoryAPITestApp**)，以滑鼠右鍵按一下 [參考]，然後按一下 [新增參考]。 選取 "**System.Configuration**" 組件的核取方塊，然後按一下 [確定]。
16. 建置主控台應用程式。 按一下功能表上的 [建置]，再按一下 [建置方案]。
17. 確認您 Azure Blob 儲存體之 **adftutorial** 容器中至少有一個檔案。 如果沒有，請在「記事本」中以下列內容建立 **Emp.txt** 檔案，然後將它上傳至 adftutorial 容器。

       John, Doe    Jane, Doe
18. 按一下功能表上的 [偵錯] -> [開始偵錯]，執行範例。 當您看到 [取得資料配量的執行詳細資料]，請等待數分鐘再按 **ENTER**。
19. 使用 Azure 入口網站確認 Data Factory： **APITutorialFactory** 是使用下列成品所建立：
   * 連結服務：**LinkedService_AzureStorage**
   * 資料集：**DatasetBlobSource** 和 **DatasetBlobDestination**。
   * 管線： **PipelineBlobSample**
20. 確認在指定 Azure SQL Database 的 "**emp**" 資料表中建立兩筆員工記錄。

## <a name="next-steps"></a>後續步驟
* 詳閱 [資料移動活動](data-factory-data-movement-activities.md) 一文，其提供您在本教學課程中使用的複製活動詳細資訊。
* 請參閱 [Data Factory .NET API 參考](https://msdn.microsoft.com/library/mt415893.aspx) ，以取得有關 Data Factory .NET SDK 的詳細資料。 這篇文章並未涵蓋所有的 Data Factory .NET API。




<!--HONumber=Dec16_HO2-->


