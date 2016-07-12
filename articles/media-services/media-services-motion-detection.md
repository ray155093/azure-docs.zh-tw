<properties
	pageTitle="使用 Azure 媒體分析偵測動作"
	description="「Azure 媒體動作偵測器」媒體處理器 (MP) 能讓您在冗長且平淡的影片中，有效識別出感興趣的區段。"
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/22/2016"  
	ms.author="milanga;juliako;"/>
 
# 使用 Azure 媒體分析偵測動作

##概觀

**Azure 媒體動作偵測器**媒體處理器 (MP) 能讓您在冗長且平淡的影片中，有效識別出感興趣的區段。動作偵測可以用來在靜態攝影機資料上識別影片中有動作發生的區段。它會產生 JSON 檔案，其中包含具有時間戳記的中繼資料，以及發生事件的週框區域。

如果將此技術用於監視器的影片輸出，它將能把動作分類為相關事件及誤判情況 (例如陰影或光源變更)。這能讓您在不用檢視無止境的不相關事件之下，便能從攝影機輸出產生安全性警示，並從時間相當長的監視影片中擷取感興趣的片段。

**Azure 媒體動作偵測器** MP 目前為預覽功能。

本主題提供有關 **Azure 媒體動作偵測器**的詳細資料，並示範如何搭配適用於 .NET 的媒體服務 SDK 來使用它。

##動作偵測器輸入檔案

影片檔案。目前支援下列格式：MP4、MOV 及 WMV。

##動作偵測器輸出檔案

動作偵測工作會在輸出資產中傳回 JSON 檔案，該檔案會在影片中描述動作警示及類別。檔案將包含在影片中所偵測到動作之時間和持續時間的資訊。

目前動作偵測僅支援一般動作類別，此類別在輸出中被稱為 ***type 2***。

X 和 Y 座標和大小將會以介於 0.0 和 1.0 之間的標準化浮點數列出。將此數值乘以影片的高度和寬度解析度，來取得已偵測到動作之區域的週框方塊。

每個輸出都會被分割成片段，並再細分成間隔，以定義影片中的資料。片段長度並不需要相等。在沒有偵測到任何動作的情況下，長度可能會變得非常長。

動作偵測器 API 會在物件於固定背景的影片 (例如監視影片) 中移動時提供指示器。動作偵測器已針對降低誤判 (例如光源和陰影變化) 進行調整。目前演算法的限制包括夜視影片、半透明物件及小型物件。

###<a id="output_elements"></a>輸出 JSON 檔案的元素

下表說明輸出 JSON 的元素。

元素|說明
---|---
版本|這是指影片 API 的版本。
時幅|影片每秒的「刻度」數目。
Offset|時間戳記的時間位移 (以「刻度」為單位)。在版本 1.0 的影片 API 中，這永遠會是 0。在我們於未來將支援的案例中，此值可能會變更。
畫面播放速率|影片的每秒畫面格數。
寬度，高度|指的是影片的寬度和高度 (以像素為單位)。
啟動|開始的時間戳記 (以「刻度」為單位)。
持續時間|事件的長度 (以「刻度」為單位)。
間隔|事件中每個項目的間隔 (以「刻度」為單位)。
事件|每個事件片段皆包含在該持續期間內所偵測到的動作。
類型|在目前的版本中，針對一般動作，這永遠會是「2」。此標籤可讓影片 API 在未來的版本中能夠彈性地為動作進行分類。
RegionID|如前文所述，這在此版本中將永遠會是 0。此標籤可讓影片 API 在未來的版本中能夠彈性地在各個區域中尋找動作。
區域|指的是影片中您會關心其中所發生之動作的區塊。在目前版本的影片 API 中，您並無法指定區域，影片的整個表面都是要進行偵測的動作區域。<br/>-識別碼代表區域 – 此版本中只有一個，亦即識別碼 0。<br/>-矩形代表您會關心其中所發生之動作的區域形狀。在此版本中，它永遠會是矩形。 <br/>- 區域擁有以 X、Y、寬度及高度表示的維度。X 和 Y 座標代表以標準化縮放 (0.0 到 1.0) 顯示之區域左上角的 XY 座標。寬度和高度代表以標準化縮放 (0.0 到 1.0) 顯示之區域的大小。在目前的版本中，X、Y、寬度及高度永遠會被固定在 0，0 和 1，1。<br/>-片段。中繼資料會被分成稱為「片段」的不同區段。每個片段皆包含開始、持續時間、間隔數字及事件。沒有事件的片段，代表在該片段的開始時間和持續時間之間沒有偵測到任何動作。
括號|每個括號皆代表事件中的單一間隔。如果間隔顯示空白括號，便代表沒有偵測到動作。
 

##工作設定 (預設)

以 **Azure 媒體動作偵測器**建立工作時，您必須指定設定預設值。目前您沒辦法在 Azure 媒體動作偵測器設定預設值中設定任何選項。下列為您必須提供的最低設定預設值。

	{"version":"1.0"}

##範例影片和動作偵測器輸出

###具有實際動作的範例

[具有實際動作的範例](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###JSON 輸出

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###具有誤判的範例

[具有誤判的範例 (光源變化)：](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###JSON 輸出

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##限制

- 支援的輸入影片格式包括 MP4、MOV 及 WMV。
- 動作偵測已針對固定背景的影片最佳化。演算法會專注在降低誤判之上，例如光源變化和陰影。
- 某些動作可能會因技術挑戰而無法偵測，例如夜視影片、半透明物件及小型物件。


## 範例程式碼

下列程式將示範如何：

1. 建立資產並將媒體檔案上傳到資產。
1. 根據包含下列 JSON 預設值的設定檔案，建立執行影片動作偵測工作的工作。
					
		{
		    "version": "1.0"
		}

1. 下載輸出 JSON 檔案。
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
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
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
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


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##相關連結

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0629_2016-->