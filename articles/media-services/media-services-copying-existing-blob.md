---
title: "將 blob 從儲存體帳戶複製到 Azure 媒體服務資產 | Microsoft Docs"
description: "本主題說明如何將現有的 blob 複製到媒體服務資產。 下列範例會使用 Azure 媒體服務 .NET SDK 延伸模組。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 14bdbe593d8c6ec5297ce37ce57b759deb533cec
ms.lasthandoff: 03/14/2017


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>將現有的 blob 複製到媒體服務資產
本主題說明如何使用 [Azure 媒體服務 .NET SDK 延伸模組](https://github.com/Azure/azure-sdk-for-media-services-extensions/)將 Blob 從儲存體帳戶複製到新的 Azure 媒體服務 (AMS) 資產。

此擴充方法適用於：

- 一般資產。
- 即時封存資產 (FragBlob 格式)。
- 屬於不同媒體服務帳戶 (甚至跨不同資料中心) 的來源和目的地資產。 不過，這麼做可能會向您索取費用。 如需有關定價的詳細資訊，請參閱 [資料傳輸](https://azure.microsoft.com/pricing/#header-11)。

> [!NOTE]
> 請勿在不使用媒體服務 API 的情況下，嘗試變更由媒體服務所產生的 blob 容器內容。
> 

本主題將示範兩個程式碼範例：

1. 將 Blob 從一個 AMS 帳戶中的資產複製到另一個 AMS 帳戶中的新資產。
2. 將 Blob 從某些儲存體帳戶複製到 AMS 帳戶中的新資產。

## <a name="copy-blobs-between-two-ams-accounts"></a>在兩個 AMS 帳戶之間複製 Blob  

### <a name="prerequisites"></a>必要條件

兩個媒體服務帳戶。 請參閱主題 [如何建立媒體服務帳戶](media-services-portal-create-account.md)。

### <a name="download-sample"></a>下載範例
您可以遵循本文中的步驟，或者您可以從[這裡](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)下載包含本文所述的程式碼範例。

### <a name="set-up-your-project"></a>設定專案

1. 使用 Visual Studio 建立一個包含 C# Console Application 專案的新方案。 
3. 使用 [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) 來安裝和新增 Azure 媒體服務 .NET SDK 延伸模組 (windowsazure.mediaservices.extensions)。 安裝這個封裝，也會安裝 Media Services .NET SDK，並新增所有其他必要相依性。
4. 新增此專案所需的其他參考：System.Configuration。
6. 將 appSettings 區段新增至 .config 檔中，並根據媒體服務帳戶、目的地儲存體帳戶及來源資產識別碼將值更新。 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>將 Blob 從一個 AMS 帳戶中的資產複製到另一個 AMS 帳戶中的資產

下列程式碼使用延伸模組 **IAsset.Copy**方法，將來源資產中的所有檔案都複製到使用單個延伸模組的目的地資產。 非同步支援有其他多載。

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>從儲存體帳戶將 Blob 複製到 AMS 帳戶 

### <a name="prerequisites"></a>必要條件

- 您從中複製 Blob 的來源儲存體帳戶。
- 您要將 Blob 複製到其中的 AMS 帳戶。

### <a name="set-up-your-project"></a>設定專案

1. 使用 Visual Studio 建立一個包含 C# Console Application 專案的新方案。 
3. 使用 [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) 來安裝和新增 Azure 媒體服務 .NET SDK 延伸模組 (windowsazure.mediaservices.extensions)。 安裝這個封裝，也會安裝 Media Services .NET SDK，並新增所有其他必要相依性。
4. 新增此專案所需的其他參考：System.Configuration。
6. 將 appSettings 區段新增至 .config 檔案，並根據您的來源儲存體和目的地 AMS 帳戶將值更新。
   
          <appSettings>
            <add key="MediaServicesAccountName" value="name" />
            <add key="MediaServicesAccountKey" value="key" />
            <add key="MediaServicesStorageAccountName" value="name" />
            <add key="MediaServicesStorageAccountKey" value="key" />
            <add key="SourceStorageAccountName" value="name" />
            <add key="SourceStorageAccountKey" value="key" />
          </appSettings>

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-a-ams-account"></a>將 Blob 從某些儲存體帳戶複製到 AMS 帳戶中的新資產

下列程式碼會將 Blob 從儲存體帳戶複製到媒體服務資產。 

>[!NOTE]
>對於不同的 AMS 原則 (例如 Locator 原則或 ContentKeyAuthorizationPolicy) 有 1,000,000 個原則的限制。 如果您一律使用相同的日期 / 存取權限，例如，要長時間維持就地 (非上載原則) 的定位器原則，您應該使用相同的原則識別碼。 如需詳細資訊，請參閱 [這個](media-services-dotnet-manage-entities.md#limit-access-policies) 主題。

    using System;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
            private static readonly string _sourceStorageAccountName =
         ConfigurationManager.AppSettings["SourceStorageAccountName"];
            private static readonly string _sourceStorageAccountKey =
                ConfigurationManager.AppSettings["SourceStorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static CloudStorageAccount _sourceStorageAccount = null;
            private static CloudStorageAccount _destinationStorageAccount = null;
    
            static void Main(string[] args)
            {
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey));
    
                _sourceStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName, 
                        _sourceStorageAccountKey), true);
    
                _destinationStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_mediaServicesStorageAccountName, 
                        _mediaServicesStorageAccountKey), true);
                
                CloudBlobClient sourceCloudBlobClient = 
                    _sourceStorageAccount.CreateCloudBlobClient();
                CloudBlobContainer sourceContainer = 
                    sourceCloudBlobClient.GetContainerReference("NameOfBlobContainerYouWantToCopy");
    
                CreateAssetFromExistingBlobs(sourceContainer);
            }
            
            static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
            {
                CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
    
                // Create a new asset. 
                IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);
    
                IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                    TimeSpan.FromHours(24), AccessPermissions.Write);
    
                ILocator destinationLocator = 
                    _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    
                // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
                CloudBlobContainer destAssetContainer =
                    destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);
    
                if (destAssetContainer.CreateIfNotExists())
                {
                    destAssetContainer.SetPermissions(new BlobContainerPermissions
                    {
                        PublicAccess = BlobContainerPublicAccessType.Blob
                    });
                }
    
                var blobList = sourceBlobContainer.ListBlobs();
    
                foreach (var sourceBlob in blobList)
                {
                    var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
    
                    ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);
    
                    // Call the CopyBlobHelpers.CopyBlobAsync extension method to copy blobs.
                    using (Task task = 
                        CopyBlobHelpers.CopyBlobAsync((CloudBlockBlob)sourceBlob, 
                            (CloudBlockBlob)destinationBlob, 
                            new BlobRequestOptions(), 
                            CancellationToken.None))
                    {
                        task.Wait();
                    }
    
                    assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                    assetFile.Update();
                    Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
                }
    
                asset.Update();
    
                destinationLocator.Delete();
                writePolicy.Delete();
    
                // Set the primary asset file.
                // If, for example, we copied a set of Smooth Streaming files, 
                // set the .ism file to be the primary file. 
                // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
                var ismAssetFiles = asset.AssetFiles.ToList().
                    Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
    
                // The following code assigns the first .ism file as the primary file in the asset.
                // An asset should have one .ism file.  
                ismAssetFiles.First().IsPrimary = true;
                ismAssetFiles.First().Update();
    
                return asset;
            }
        }
    }
    
## <a name="next-steps"></a>後續步驟

您現在可以將上傳的資產編碼。 如需詳細資訊，請參閱 [為資產編碼](media-services-portal-encode.md)。

您也可以使用 Azure Functions，以根據在所設定容器到達的檔案來觸發編碼作業。 如需詳細資訊，請參閱[此範例](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


