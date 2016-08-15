<properties pageTitle="Azure 媒體服務遙測搭配 .NET | Microsoft Azure" 
	description="本文示範如何使用 Azure 媒體服務遙測。" 
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
	ms.date="07/27/2016"   
	ms.author="juliako"/>

# Azure 媒體服務遙測搭配 .NET
 
## 概觀

媒體服務遙測/監視可讓媒體服務客戶存取其服務的度量資料。目前的版本支援 "Channel" 和 "StreamingEndpoint" 實體的遙測資料。您可以設定元件層級細微度的遙測。詳細資料層級可分為「正常」和「詳細資訊」兩種。目前的版本僅支援「正常」。

遙測會寫入由客戶提供的 Azure 儲存體帳戶中的儲存體資料表 (儲存體帳戶必須附加至媒體服務帳戶)。遙測系統會以 00:00 UTC 為基準為每一天建立個別的資料表。以 "TelemetryMetrics20160321" 為例，其中的 "20160321" 即代表資料表的建立日期。每天都會有個別的資料表。

請注意遙測系統不會管理資料保留。您可以刪除儲存體資料表來移除舊的遙測資料。

您可以使用下列其中一個方法取用遙測資料︰

- 直接從 Azure 表格儲存體 (例如使用儲存體 SDK) 中讀取資料。如需遙測儲存體資料表的描述，請參閱[這個](https://msdn.microsoft.com/library/mt742089.aspx)主題中的＜Consuming telemetry information (取用遙測資訊)＞。

或

- 使用媒體服務 .NET SDK 中的支援讀取儲存體資料。本主題示範如何為指定的 AMS 帳戶啟用遙測，以及如何使用 Azure 媒體服務 .NET SDK 查詢度量。

## 設定媒體服務帳戶的遙測

若要啟用遙測，您需要執行下列步驟︰

- 取得媒體服務帳戶附加之儲存體帳戶的認證。
- 建立 **EndPointType** 設定為 **AzureTable** 且 endPontAddress 指向儲存體資料表的通知端點。

	    INotificationEndPoint notificationEndPoint = 
	                  _context.NotificationEndPoints.Create("monitoring", 
	                  NotificationEndPointType.AzureTable,
	                  "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- 針對要監視的服務建立監視組態設定。系統最多只允許一個監視組態設定。
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## 取用遙測資訊

遙測資料會寫入設定媒體服務帳戶的遙測時指定的儲存體帳戶中的 Azure 儲存體資料表。遙測系統會以 00:00 UTC 為基準為每一天建立個別的資料表。以 "TelemetryMetrics20160321" 為例，其中的 "20160321" 即代表資料表的建立日期。每天都會有個別的資料表。

您可以查詢下列度量資訊的遙測資料表。本主題稍後所示的範例示範如何使用媒體服務 .NET SDK 來查詢度量。

### StreamingEndpoint 記錄檔

您可以查詢下列 StreamingEndPoint 度量。

屬性|說明|範例值
---|---|---
**PartitionKey**|能取得記錄的資料分割索引鍵。|60b71b0f6a0e4d869eb0645c16d708e1\_6efed125eef44fb5b61916edc80e6e23
**RowKey**|能取得記錄的列索引鍵。|00959\_00000
**AccountId**|能取得媒體服務帳戶識別碼。|6efed125-eef4-4fb5-b619-16edc80e6e23
**StreamingEndpointId**|能取得媒體服務串流端點識別碼。|d17ec9e4-a5d4-033d-0c36-def70229f06f
**ObservedTime**|能取得度量的觀察時間。|1/20/16 23:44:01
**HostName**|能取得串流端點主機名稱。|builddemoserver.origin.mediaservices.windows.net
**StatusCode**|能取得狀態碼。|200
**ResultCode**|能取得結果碼。|S\_OK
**RequestCount**|能取得結果計數。|3
**BytesSent**|能取得傳送位元組。|2987358
**ServerLatency**|能取得伺服器延遲 (包括儲存體)。|129
**EndToEndLatency**|能取得端對端要求時間。|250


### 直播頻道活動訊號

您可以查詢下列直播頻道度量。

屬性|說明|範例值
---|---|---
**PartitionKey**|能取得記錄的資料分割索引鍵。|60b71b0f6a0e4d869eb0645c16d708e1\_0625cc45918e4f98acfc9a33e8066628
**RowKey**|能取得記錄的列索引鍵。|13872\_00005
**AccountId**|能取得媒體服務帳戶識別碼。|6efed125-eef4-4fb5-b619-16edc80e6e23
**ChannelId**|能取得媒體服務通道識別碼。|
**ObservedTime**|能取得度量的觀察時間。|1/21/2016 20:08:49
**CustomAttributes**|能取得自訂屬性。|
**TrackType**|能取得曲目類型。|video
**TrackName**|能取得曲目名稱。|video
**Bitrate**|能取得位元速率。|785000
**IncomingBitrate**|能取得連入位元速率。|784548
**OverlapCount**|能取得重疊計數。|0
**DiscontinuityCount**|能取得不連續計數。|0
**LastTimestamp**|能取得最後一個時間戳記。|1800488800
 
## 範例  
	
下列範例示範如何為指定的 AMS 帳戶啟用遙測，以及如何使用 Azure 媒體服務 .NET SDK 查詢度量。

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace AMSMetrics
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
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
	

	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
		            INotificationEndPoint notificationEndPoint = 
		                          _context.NotificationEndPoints.Create("monitoring", 
								  NotificationEndPointType.AzureTable, GetTableEndPoint());

	                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
	                    new List<ComponentMonitoringSetting>()
	                    {
	                        new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
	                    });
	            }
	
	            //Print metrics for a Streaming Endpoint.
	            PrintStreamingEndpointMetrics();
	
	            Console.ReadLine();
	        }
	
	        private static string GetTableEndPoint()
	        {
	            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
	        }
	
	        private static void PrintStreamingEndpointMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
	                    start,
	                    end);
	
	
	            Console.Title = "Streaming endpoint metrics:";
	
	            foreach (var log in res)
	            {
	                Console.WriteLine("AccountId: {0}", log.AccountId);
	                Console.WriteLine("BytesSent: {0}", log.BytesSent);
	                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
	                Console.WriteLine("HostName: {0}", log.HostName);
	                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
	                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
	                Console.WriteLine("RequestCount: {0}", log.RequestCount);
	                Console.WriteLine("ResultCode: {0}", log.ResultCode);
	                Console.WriteLine("RowKey: {0}", log.RowKey);
	                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
	                Console.WriteLine("StatusCode: {0}", log.StatusCode);
	                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
	                Console.WriteLine();
	            }
	
	            Console.WriteLine();
	        }

	        private static void PrintChannelMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
	                start,
	                end);
	
	            // Print the channel metrics.
	            Console.WriteLine("Channel metrics:");
	
	            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
	            {
	                Console.WriteLine(
	                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
	                    channelHeartbeat.ObservedTime,
	                    channelHeartbeat.LastTimestamp,
	                    channelHeartbeat.IncomingBitrate);
	            }
	
	            Console.WriteLine();
	        }
	    }
	}


##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##後續步驟
 
請參閱 Azure 媒體服務學習途徑，以了解 AMS 所提供的強大功能。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0803_2016-->