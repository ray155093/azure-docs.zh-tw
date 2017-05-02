---
title: "使用 Azure 媒體分析修訂臉部 | Microsoft Docs"
description: "本主題示範如何使用 Azure 媒體分析修訂臉部。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 5b6d8b8c-5f4d-4fef-b3d6-dc22c6b5a0f5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/16/2017
ms.author: juliako;
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 2600c5cec36a8a44a85a62d6672d6ae57343f20c
ms.lasthandoff: 04/17/2017


---
# <a name="redact-faces-with-azure-media-analytics"></a>使用 Azure 媒體分析修訂臉部
## <a name="overview"></a>概觀
**Azure 媒體修訂器** 是 [Azure 媒體分析](media-services-analytics-overview.md) 媒體處理器 (MP)，可在雲端提供可調整的臉部修訂。 臉部修訂可讓您修改視訊，以模糊所選人物的臉部。 在公共安全和新聞媒體案例中，您可能會想要使用臉部修訂服務。 若要手動修訂包含多個臉部的幾分鐘影片，可能要花上數小時的時間，若使用此服務，則只需要幾個簡單的步驟就能完成臉部修訂程序。 如需詳細資訊，請參閱[此](https://azure.microsoft.com/blog/azure-media-redactor/)部落格。

本主題提供有關 **Azure 媒體修訂** 的詳細資料，並示範如何搭配適用於 .NET 的媒體服務 SDK 來使用它。

**Azure 媒體修訂器** MP 目前為預覽功能。 在所有公開的 Azure 區域及美國政府和中國的數據中心皆有提供。 此預覽版本目前以免費方式提供。 

## <a name="face-redaction-modes"></a>臉部修訂模式
臉部修訂的運作方式是，偵測視訊中每個畫面內出現的臉部，並及時向前及向後追蹤臉部物體，讓同一個人在其他角度也可以變得模糊。 自動化修訂程序非常複雜，而且不一定會 100% 產生所需的輸出，因此，媒體分析提供您幾種方式來修改最終輸出。

除了完全自動模式，還有一種兩段式的工作流程可讓您透過識別碼清單選取/取消選取找到的臉部。 此外，為了進行任意的每一畫面調整，MP 使用 JSON 格式的中繼資料檔案。 此工作流程分割成**分析**和**修訂**模式。 您可以將這兩種模式結合成單一階段，以在一個作業中執行這兩項工作，我們將這個模式稱為 **結合**。

### <a name="combined-mode"></a>結合模式
這會自動產生修訂的 mp4，而不需要手動輸入。

| 階段 | 檔案名稱 | 注意事項 |
| --- | --- | --- |
| 輸入資產 |foo.bar |WMV、MOV 或 MP4 格式的視訊 |
| 輸入組態 |作業組態預設值 |{'version':'1.0', 'options': {'mode':'combined'}} |
| 輸出資產 |foo_redacted.mp4 |已套用模糊處理的視訊 |

#### <a name="input-example"></a>輸入範例︰
[請觀看這個影片](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>輸出範例：
[請觀看這個影片](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>分析模式
兩段式工作流程的 **分析** 階段會接受視訊輸入，並產生臉部位置的 JSON 檔案和每個偵測到之臉部的 jpg 影像。

| 階段 | 檔案名稱 | 注意事項 |
| --- | --- | --- |
| 輸入資產 |foo.bar |WMV、MPV 或 MP4 格式的視訊 |
| 輸入組態 |作業組態預設值 |{'version':'1.0', 'options': {'mode':'analyze'}} |
| 輸出資產 |foo_annotations.json |JSON 格式的臉部位置註解資料。 使用者可編輯此資料以修改模糊處理的邊框。 請參閱以下範例。 |
| 輸出資產 |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |所偵測到之每個臉部的已裁剪 jpg，數字表示臉部的 labelId |

#### <a name="output-example"></a>輸出範例：
    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

    … truncated

### <a name="redact-mode"></a>修訂模式
工作流程的第二個階段會接受必須結合成單一資產的較大量輸入。

這包括要模糊處理的識別碼清單、原始視訊和註解 JSON。 這個模式會使用註解在輸入視訊上套用模糊處理。

分析階段的輸出不包含原始視訊。 視訊必須上傳到修訂模式工作的輸入資產並選取做為主要檔案。

| 階段 | 檔案名稱 | 注意事項 |
| --- | --- | --- |
| 輸入資產 |foo.bar |WMV、MPV 或 MP4 格式的視訊。 和步驟 1 相同的視訊。 |
| 輸入資產 |foo_annotations.json |來自第一個階段的註解中繼資料檔案，並帶有選擇性的修改。 |
| 輸入資產 |foo_IDList.txt (選擇性) |要修訂之臉部 ID 的選擇性換行分隔清單。 如果保持空白，則會模糊所有臉部。 |
| 輸入組態 |作業組態預設值 |{'version':'1.0', 'options': {'mode':'redact'}} |
| 輸出資產 |foo_redacted.mp4 |已根據註解套用模糊處理的視訊 |

#### <a name="example-output"></a>範例輸出
這是選取了一個識別碼的 IDList 輸出。

[請觀看這個影片](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Example foo_IDList.txt
 
     1
     2
     3
 
## <a name="attribute-descriptions"></a>屬性描述
修訂 MP 能提供高精確度的臉部位置偵測和追蹤功能，可在單一視訊畫面中偵測到最多 64 個人類臉部。 正面的臉部能提供最佳結果，而側面的臉部和較小的臉部 (小於或等於 24x24 像素) 則頗具挑戰性。

所偵測和追蹤的臉部在傳回時會有指出臉部位置的座標，以及指出正在追蹤該個人的臉部識別碼。 臉部識別碼很容易在正面臉部長時間於畫面中遺失或重疊的情況下重設，導致某些人員被指派多個識別碼。

如需屬性的詳細說明，請參閱 [使用 Azure 媒體分析偵測臉部和情緒](media-services-face-and-emotion-detection.md) 主題。

## <a name="sample-code"></a>範例程式碼
下列程式將示範如何：

1. 建立資產並將媒體檔案上傳到資產。
2. 根據包含下列 JSON 預設值的組態檔案，建立具有臉部修訂工作的作業。 
   
        {'version':'1.0', 'options': {'mode':'combined'}}
3. 下載輸出 JSON 檔案。 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace FaceRedaction
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
   
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
   
                static void Main(string[] args)
                {
   
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
   
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
   
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
   
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
   
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
   
                    // Launch the job.
                    job.Submit();
   
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
   
                    progressJobTask.Wait();
   
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
   
                    return job.OutputMediaAssets[0];
                }
   
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
   
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
   
                    return asset;
                }
   
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
   
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
   
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
   
                    return processor;
                }
   
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
   
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
   
            }
        }

## <a name="next-step"></a>後續步驟
檢閱媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>相關連結
[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)


