---
title: "使用 .NET 設定 Azure 媒體服務遙測 | Microsoft Docs"
description: "本文示範如何透過 .NET SDK 使用 Azure 媒體服務遙測。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: f8f55e37-0714-49ea-bf4a-e6c1319bec44
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: d693bc0de2f8a03d67b346f3b2d4693284ae4d71
ms.openlocfilehash: da7d4f87fdcaca6517fa95152d42c138533772b9


---

# <a name="configuring-azure-media-services-telemetry-with-net"></a>使用 .NET 設定 Azure 媒體服務遙測

本主題說明您使用 .NET SDK 設定 Azure 媒體服務 (AMS) 遙測時可能採取的一般步驟。 

>[!NOTE]
>如需什麼是 AMS 遙測及如何取用的詳細說明，請參閱[概觀](media-services-telemetry-overview.md)主題。

您可以使用下列其中一個方法取用遙測資料︰

- 直接從 Azure 表格儲存體 (例如使用儲存體 SDK) 中讀取資料。 如需遙測儲存體資料表的說明，請參閱[這個](https://msdn.microsoft.com/library/mt742089.aspx)主題中的**取用遙測資訊**。

或

- 使用媒體服務 .NET SDK 中的支援讀取儲存體資料。 本主題示範如何為指定的 AMS 帳戶啟用遙測，以及如何使用 Azure 媒體服務 .NET SDK 查詢度量。  

## <a name="configuring-telemetry-for-a-media-services-account"></a>設定媒體服務帳戶的遙測

若要啟用遙測，您需要執行下列步驟︰

- 取得媒體服務帳戶附加之儲存體帳戶的認證。 
- 建立 **EndPointType** 設定為 **AzureTable** 且 endPontAddress 指向儲存體資料表的通知端點。

        INotificationEndPoint notificationEndPoint = 
                      _context.NotificationEndPoints.Create("monitoring", 
                      NotificationEndPointType.AzureTable,
                      "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- 針對要監視的服務建立監視組態設定。 系統最多只允許一個監視組態設定。 
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## <a name="consuming-telemetry-information"></a>取用遙測資訊

如需取用遙測資訊的相關資訊，請參閱[這個](media-services-telemetry-overview.md)主題。
 
## <a name="example"></a>範例  
    
本節所示的範例假設您在 App.config 檔案中有下個區段。
    
    <appSettings>
      <add key="MediaServicesAccountName" value="ams_acct_name" />
      <add key="MediaServicesAccountKey" value="ams_acct_key" />
      <add key="MediaServicesAccountID" value="ams_acct_id" />
      <add key="StorageAccountName" value="storage_name" />
      <add key="StorageAccountKey" value="storage_key" />
    </appSettings>

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
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesAccountID =
                ConfigurationManager.AppSettings["MediaServicesAccountID"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["StorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["StorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            private static IStreamingEndpoint _streamingEndpoint = null;
            private static IChannel _channel = null;
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                _streamingEndpoint = _context.StreamingEndpoints.FirstOrDefault();
                _channel = _context.Channels.FirstOrDefault();
    
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
                            new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
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
                Console.WriteLine(string.Format("Telemetry for streaming endpoint '{0}'", _streamingEndpoint.Name));
    
                var end = DateTime.UtcNow;
                var start = DateTime.UtcNow.AddHours(-5);
    
                // Get some streaming endpoint metrics.
                var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
                        GetTableEndPoint(),
                        _mediaServicesStorageAccountKey,
                        _mediaServicesAccountID,
                        _streamingEndpoint.Id,
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
                if (_channel == null)
                {
                    Console.WriteLine("There are no channels in this AMS account");
                    return;
                }
    
                Console.WriteLine(string.Format("Telemetry for channel '{0}'", _channel.Name));
    
                var end = DateTime.UtcNow;
                var start = DateTime.UtcNow.AddHours(-5);
    
                // Get some channel metrics.
                var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
                    GetTableEndPoint(),
                    _mediaServicesStorageAccountKey,
                    _mediaServicesAccountID,
                   _channel.Id,
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


## <a name="next-steps"></a>後續步驟

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!--HONumber=Nov16_HO5-->


