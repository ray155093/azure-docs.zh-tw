<properties pageTitle="Azure 媒體服務遙測搭配 .NET | Microsoft Azure" 
	description="本文示範如何使用 Azure 媒體服務遙測。" 
	services="" 
	documentationCenter=""
	authors="juliako" />

# Azure 媒體服務遙測搭配 .NET

## 概觀

媒體服務遙測/監視可讓媒體服務客戶存取其服務的度量資料。目前的版本支援 "Channel" 和 "StreamingEndpoint" 實體的遙測資料。您可以設定元件層級細微度的遙測。詳細資料層級可分為「正常」和「詳細資訊」兩種。目前的版本僅支援「正常」。

遙測會寫入由客戶提供的 Azure 儲存體帳戶 (儲存體帳戶必須附加至媒體服務帳戶)。遙測會寫入指定儲存體帳戶中的 Azure 儲存體資料表。遙測系統會以 00:00 UTC 為基準為每一天建立個別的資料表。以 "TelemetryMetrics20160321" 為例，其中的 "20160321" 即代表資料表的建立日期。每天都會有個別的資料表。

遙測系統不提供資料保留，也不會自動刪除舊記錄。基於這個原因，您需要管理及刪除舊記錄。每天都有個別的資料表讓您能輕鬆地刪除舊記錄。您只要刪除舊的資料表就可以了。

本主題示範如何為指定的 AMS 服務啟用遙測，以及如何使用 .NET 查詢度量。

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


## StreamingEndpoint 記錄檔

###可用的度量

您可以查詢下列 StreamingEndPoint 度量。

- **PartitionKey** 能取得記錄的資料分割索引鍵。
- **RowKey** 能取得記錄的列索引鍵。
- **AccountId** 能取得媒體服務帳戶識別碼。
- **AccountId** 能取得媒體服務串流端點識別碼。
- **ObservedTime** 能取得度量的觀察時間。
- **HostName** 能取得串流端點主機名稱。
- **StatusCode** 能取得狀態碼。
- **ResultCode** 能取得的結果碼。
- **RequestCount** 能取得要求計數。
- **BytesSent** 能取得傳送位元組。
- **BytesSent** 能取得伺服器延遲。
- **BytesSent** 能取得端對端要求時間。

###串流端點查詢結果範例

![串流端點頁面](media/media-services-telemetry/media-services-telemetry01.png)


## 直播頻道活動訊號

###可用的度量

您可以查詢下列直播頻道度量。

- **PartitionKey** 能取得記錄的資料分割索引鍵。
- **RowKey** 能取得記錄的列索引鍵。
- **AccountId** 能取得媒體服務帳戶識別碼。
- **ChannelId** 能取得媒體服務頻道識別碼。
- **ObservedTime** 能取得度量的觀察時間。
- **CustomAttributes** 能取得自訂屬性。
- **TrackType** 能取得資料軌類型。
- **TrackName** 能取得資料軌名稱。
- **Bitrate** 能取得位元速率。
- **IncomingBitrate** 能取得連入位元速率。
- **OverlapCount** 能取得重疊計數。
- **DiscontinuityCount** 能取得不連續計數。
- **LastTimestamp** 能取得最後一個時間戳記。
 
###直播頻道查詢結果範例

![串流端點頁面](media/media-services-telemetry/media-services-telemetry01.png)

## StreamingEndpoint 度量範例
		
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
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
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

<!---HONumber=AcomDC_0713_2016-->