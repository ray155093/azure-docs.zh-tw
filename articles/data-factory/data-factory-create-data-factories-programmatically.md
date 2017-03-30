---
title: "使用 Azure .NET SDK 建立資料管線 |Microsoft Docs"
description: "了解如何使用 Data Factory .NET SDK，以程式設計方式建立、監視和管理 Azure Data Factory。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 6a76c399e626ea85581d5f8fb863da878bdbf50b
ms.lasthandoff: 03/09/2017


---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>使用 Azure Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory
## <a name="overview"></a>概觀
您可以使用 Data Factory .NET SDK，以程式設計方式建立、監視及管理 Azure Data Factory 本文包含指導您建立範例 .NET 主控台應用程式的逐步解說，此應用程式將會建立並監視 Data Factory。 請參閱 [Data Factory 類別庫參考](https://msdn.microsoft.com/library/mt415893.aspx)，以取得有關 Data Factory .NET SDK 的詳細資料。

## <a name="prerequisites"></a>必要條件
* Visual Studio 2012、2013 或 2015
* 下載並安裝 [Azure .NET SDK](http://azure.microsoft.com/downloads/)。
* 將原生用戶端應用程式新增到 Azure Active Directory。 如需有關新增應用程式的步驟，請參閱 [整合應用程式與 Azure Active Directory](../active-directory/active-directory-integrating-applications.md) 。 記下 [設定] 頁面上的**用戶端識別碼**和**重新導向 URI**。
* 取得您的 Azure **訂用帳戶 ID** 和**租用戶識別碼**。 如需相關指示，請參閱 [取得 Azure 訂用帳戶 ID 和租用戶識別碼](#get-azure-subscription-and-tenant-ids) 。
* 下載並安裝適用於 Azure Data Factory 的 NuGet 封裝 逐步解說中包含相關指示。

## <a name="walkthrough"></a>逐步介紹
1. 使用 Visual Studio 2012 或 2013 來建立 C# .NET 主控台應用程式。
   1. 啟動 **Visual Studio 2012/2013/2015**。
   2. 按一下 [檔案]，指向 [新增]，然後按一下 [專案]。
   3. 展開 [範本]，然後選取 [Visual C#]。 在此逐步解說中，您使用的是 C#，但您可以使用任何 .NET 語言。
   4. 從右邊的專案類型清單中選取 [主控台應用程式]  。
   5. 在 [名稱] 中輸入 **DataFactoryAPITestApp**。
   6. 在 [位置] 選取 **C:\ADFGetStarted**。
   7. 按一下 [確定]  以建立專案。
2. 按一下 [**工具**]，指向 [**NuGet 封裝管理員**]，然後按一下 [**封裝管理員主控台**]。
3. 在 [封裝管理員主控台] 中，逐一執行下列命令。

    ```
    Install-Package Microsoft.Azure.Management.DataFactories
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    ```
4. 將下列 **appSetttings** 區段新增 **App.config** 檔案。 **GetAuthorizationHeader** 方法會使用這些組態值。

   > [!IMPORTANT]
   > 以您自己的值取代 **AdfClientId**、**RedirectUri****SubscriptionId** 和 **ActiveDirectoryTenantId** 的值。

    ```XML
    <appSettings>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    
        <!-- Replace the following values with your own -->
        <add key="AdfClientId" value="Your AAD application ID" />
        <add key="RedirectUri" value="Your AAD application's redirect URI" />
        <add key="SubscriptionId" value="your subscription ID" />
        <add key="ActiveDirectoryTenantId" value="your tenant ID" />
    </appSettings>
    ```
5. 將下列 **using** 陳述式加入專案的原始程式檔 (Program.cs) 中。

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. 將下列會建立 **DataPipelineManagementClient** 類別執行個體的程式碼新增至 **Main** 方法中。 您會使用此物件來建立 Data Factory、連結的服務、輸入和輸出資料集，以及管線。 您也會使用此物件來監視執行階段的資料集配量。

    ```csharp
    // create data factory management client
    string resourceGroupName = "resourcegroupname";
    string dataFactoryName = "APITutorialFactorySP";
    
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);
    
    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);
    
    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!NOTE]
   > 用您的 Azure 資源群組名稱取代 **resourcegroupname** 。 若要建立資源群組，請使用 [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) Cmdlet。
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
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. 將下列建立**連結服務**的程式碼加入 **Main** 方法中。

   > [!NOTE]
   > 使用您 Azure 儲存體帳戶的**帳戶名稱**和**帳戶金鑰**做為 **ConnectionString**。

    ```csharp
    // create a linked service
    Console.WriteLine("Creating a linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "LinkedService-AzureStorage",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                )
            }
        }
    );
    ```
9. 將下列會建立**輸入和輸出資料集**的程式碼新增至 **Main** 方法中。

    輸入 Blob 的 **FolderPath** 是設定為 **adftutorial/**，其中的 **adftutorial** 是 Blob 儲存體中容器的名稱。 如果 Azure Blob 儲存體中沒有此容器，請以下列名稱建立容器： **adftutorial** ，並將文字檔上傳至容器。

    輸出 Blob 的 FolderPath 是設定為：**adftutorial/apifactoryoutput/{Slice}**，其中的 **Slice** 是根據 **SliceStart** (每個配量的開始日期時間) 的值自動計算而得。

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "LinkedService-AzureStorage",
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
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "LinkedService-AzureStorage",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
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
10. 將下列會**建立並啟用管線**的程式碼新增至 **Main** 方法中。 此管線有一個 **CopyActivity**，它以 **BlobSource** 為來源，**BlobSink** 為接收器。

    複製活動會在 Azure Data Factory 中執行資料移動。 此活動是由全域可用的服務所提供，可以使用安全、可靠及可調整的方式，在各種不同的資料存放區之間複製資料。 如需複製活動的詳細資訊，請參閱 [資料移動活動](data-factory-data-movement-activities.md) 文章。

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
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
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
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
11. 將 **Main** 方法所使用的下列 Helper 方法新增至 **Program** 類別中。 此方法會顯示一個對話方塊，讓您提供用來登入 Azure 入口網站的**使用者名稱**和**密碼**。

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientId: ConfigurationManager.AppSettings["AdfClientId"],
            redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
            promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```
12. 將下列程式碼加入 **Main** 方法中，以取得輸出資料集的資料配量狀態。 預期此範例中只有一個配量。

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
13. **(選用)** 將下列取得資料配量之執行詳細資料的程式碼新增到 **Main** 方法中。

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
        });
    
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
14. 在 [方案總管] 中展開專案 (**DataFactoryAPITestApp**)，以滑鼠右鍵按一下 [參考]，然後按一下 [新增參考]。 選取 `System.Configuration` 組件的核取方塊，然後按一下 [確定]。
15. 建置主控台應用程式。 按一下功能表上的 [建置]，再按一下 [建置方案]。
16. 確認您 Azure Blob 儲存體之 adftutorial 容器中至少有一個檔案。 如果沒有，請在「記事本」中以下列內容建立 Emp.txt 檔案，然後將它上傳至 adftutorial 容器。

    ```
    John, Doe
    Jane, Doe
    ```
17. 按一下功能表上的 [偵錯] -> [開始偵錯]，執行範例。 當您看到 [取得資料配量的執行詳細資料]，請等待數分鐘再按 **ENTER**。
18. 使用 Azure 入口網站確認 Data Factory： **APITutorialFactory** 是使用下列成品所建立：
    * 連結服務：**LinkedService_AzureStorage**
    * 資料集：**DatasetBlobSource** 和 **DatasetBlobDestination**。
    * 管線： **PipelineBlobSample**
19. 確認輸出檔案已建立於 **adftutorial** 容器的 **apifactoryoutput** 資料夾中。

## <a name="log-in-without-popup-dialog-box"></a>不使用快顯對話方塊來登入
逐步解說中的範例程式碼會啟動一個對話方塊，供您輸入 Azure 認證。 如果您需要透過程式設計方式登入，而不使用對話方塊，請參閱[使用 Azure Resource Manager 來驗證服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

> [!IMPORTANT]
> 將 Web 應用程式新增到 Azure Active Directory，並記下該應用程式的用戶端識別碼和用戶端密碼。
>
>

### <a name="example"></a>範例
建立 GetAuthorizationHeaderNoPopup 方法。

```csharp
public static async Task<string> GetAuthorizationHeaderNoPopup()
{
    var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
    var context = new AuthenticationContext(authority.AbsoluteUri);
    var credential = new ClientCredential(
        ConfigurationManager.AppSettings["AdfClientId"],
    ConfigurationManager.AppSettings["AdfClientSecret"]);
    
    AuthenticationResult result = await context.AcquireTokenAsync(
        ConfigurationManager.AppSettings["WindowsManagementUri"],
    credential);

    if (result != null)
        return result.AccessToken;

    throw new InvalidOperationException("Failed to acquire token");
}
```

在 **Main** 函式中，將 **GetAuthorizationHeader** 呼叫取代為對 **GetAuthorizationHeaderNoPopup** 的呼叫：

```csharp
TokenCloudCredentials aadTokenCredentials =
    new TokenCloudCredentials(
    ConfigurationManager.AppSettings["SubscriptionId"],
    GetAuthorizationHeaderNoPopup().Result);
```

以下說明如何建立 Active Directory 應用程式和服務主體，然後將其指派給 Data Factory 參與者角色︰

1. 建立 AD 應用程式。

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"
    ```
2. 建立 AD 服務主體。

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
3. 對 Data Factory 參與者角色新增服務主體。

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
4. 取得應用程式識別碼。

    ```PowerShell
    $azureAdApplication
    ```

記下應用程式識別碼和密碼 (用戶端密碼)，然後在逐步解說中使用它。

## <a name="get-azure-subscription-and-tenant-ids"></a>取得 Azure 訂用帳戶 ID 和租用戶識別碼
如果您的機器上未安裝最新版的 PowerShell，請依照 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 一文中的指示來安裝它。

1. 啟動 Azure PowerShell 並執行下列命令
2. 執行下列命令並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。

    ```PowerShell
    Login-AzureRmAccount
    ```

    如果您只有一個與此帳戶關聯的 Azure 訂用帳戶，您就不需執行下面兩個步驟。
3. 執行下列命令以檢視此帳戶的所有訂用帳戶。

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. 執行下列命令以選取您要使用的訂用帳戶。 以您的 Azure 訂用帳戶名稱取代 **NameOfAzureSubscription** 。

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext
    ```PowerShell

Note down the **SubscriptionId** and **TenantId** values.

