<properties 
   pageTitle="使用事件中樞串流最忙碌路徑中的 Azure 診斷資料"
   description="說明如何使用事件中樞從端對端設定 Azure 診斷，包括常見案例的指引。"
   services="event-hubs"
   documentationCenter="na"
   authors="tomarcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/30/2016"
   ms.author="tarcher" />

# 使用事件中樞串流最忙碌路徑中的 Azure 診斷資料

## 概觀
Azure 診斷會提供彈性的方法，用來收集來自計算 VM 的度量和記錄檔，並傳輸至 Azure 儲存體。從 2016 年 3 月 (SDK 2.9) 的時間範圍開始，就能夠將 Azure 診斷接收為完全自訂的資料來源，並且使用 Azure 事件中樞在數秒內傳輸最忙碌路徑資料。

支援的資料類型包括：

- ETW 事件
- 效能計數器
- Windows 事件記錄檔 
- 應用程式記錄檔
- Azure 診斷基礎結構記錄檔
 
本文將說明如何使用事件中樞從端對端設定 Azure 診斷。也提供常見案例的指引，例如自訂哪個記錄檔和度量接收至事件中樞、如何在各個環境中變更組態、您如何檢視事件中樞串流資料的其中一個範例，以及如何針對連接問題進行疑難排解。

## 必要條件
在 Azure 診斷中接收的事件中樞，在所有計算類型中受到支援 - 雲端服務、VM、VMSS 和 Servic Fabric - 支援 WAD，從 Azure SDK 2.9 和對應的 Azure Tools for Visual Studio 開始。
  
- Azure 診斷擴充 1.6 ([Azure SDK for.NET 2.9 或更高版本](https://azure.microsoft.com/downloads/)預設以此為目標)
- [Visual Studio 2013 或更高版本](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- 使用下列其中一個方法，在 Azure 診斷組態成功設定之前，在應用程式中使用 *.wadcfgx* 檔案︰
	- Visual Studio：[為 Azure 雲端服務和虛擬機器設定診斷功能](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
	- Windows PowerShell：[使用 PowerShell 在 Azure 雲端服務中啟用診斷](../cloud-services/cloud-services-diagnostics-powershell.md)
- 文章中佈建的事件中樞命名空間，[開始使用事件中樞](./event-hubs-csharp-ephcs-getstarted.md)

## 將 Azure 診斷連接至事件中樞接收
根據預設，Azure 診斷一律會將記錄檔和度量接收至 Azure 儲存體帳戶。應用程式可能會額外接收到事件中樞，方法是將 [接收] 區段新增至 .wadcfgx 檔案的 PublicConfig 區段中的 WadCfg 元素。在 Visual Studio 中，.wadcfgx 檔案是儲存在 [雲端服務專案] > [角色] > (RoleName) > [diagnostics.wadcfgx] 檔案。
  
	  <SinksConfig>
	    <Sink name="HotPath">
	      <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
	    </Sink>
	  </SinksConfig>

在此範例中，事件中樞 URL 設定為事件中樞的完整命名空間 (ServiceBus 命名空間 + “/” + 事件中樞名稱)。

事件中樞 URL 會在[傳統 Azure 入口網站](https://manage.windowsazure.com)中的 [事件中樞] 儀表板上顯示。

[接收] 名稱可以設定為任何有效的字串，只要對於整個組態檔一致使用相同的值。

**附註︰**此區段中可能有其他設定的接收，例如「applicationInsights」。Azure 診斷可以定義一或多個接收，前提是每個接收也在 **PrivateConfig** 區段中宣告。

事件中樞接收也必須宣告並定義於 .wadcfgx 組態檔的**PrivateConfig** 區段。

	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
	  </PrivateConfig>

**SharedAccessKeyName** 必須符合已在 **ServiceBus/EventHub** 命名空間中定義的 SAS 金鑰和原則。這個動作可以藉由下列方式完成，瀏覽至[傳統 Azure 入口網站](https://manage.windowsazure.com)中的事件中樞儀表板、按一下 [設定] 索引標籤，然後設定具名原則 (例如，“SendRule”)，具有「傳送」權限。**StorageAccount** 也已經在 **PrivateConfig** 中宣告。不需要在這裡變更值，特別是當他們可以運作時。在此範例中，我們將值保留空白，這是下游資產將會設定值的符號，例如 *ServiceConfiguration.Cloud.cscfg* 環境組態檔將設定環境的適當名稱和金鑰。

>[AZURE.WARNING] 請注意，事件中樞 SAS 金鑰會以純文字儲存在 .wadcfgx 檔案。有時候這會簽入原始程式碼控制，或做為組建伺服器中的資產，因此您應該適當地保護。建議在這裡使用具有「僅傳送」權限的 SAS 金鑰，讓任何惡意使用者頂多只能寫入至事件中樞，而永遠無法接聽，或加以管理。

## 設定 Azure 診斷記錄檔和度量以使用事件中樞接收
如前所述，所有預設和自訂診斷資料 (亦即，度量和記錄檔) 會在設定的間隔自動接收到 Azure 儲存體。使用事件中樞 (及任何其他接收)，您可以指定要以事件中樞接收的階層中的任何根節點或分葉節點。這包括 ETW 事件、效能計數器、Windows 事件記錄檔和應用程式記錄檔。

請務必考慮實際上應該將多少資料點傳輸至事件中樞。通常開發人員對於必須快速使用及解譯的低延遲最忙碌路徑資料 (例如藉由監視警示系統或自動調整規則)，使用此功能。也可以用來設定替代分析或搜尋存放區 - 例如，串流分析、ElasticSearch、自訂監視系統或您最愛的第三方監視系統。

以下是一些範例組態。

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>

在下列範例中，接收會套用至階層中的父 **PerformanceCounters**，這表示所有子 **PerformanceCounters** 將傳送至事件中樞。

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
        </PerformanceCounters>

在上述範例中，接收只會套用至三個計數器︰已排入佇列的要求、遭拒絕的要求和 % Processor Time。

下列範例會示範開發人員如何控制和限制傳送至用於此服務的健全狀況的關鍵度量的資料量。

        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />

在此範例中，接收會套用至記錄檔，並且只篩選為「錯誤」層級追蹤。
 
## 部署和更新雲端服務應用程式和診斷設定

部署應用程式以及事件中樞接收組態的最簡單路徑是使用 Visual Studio。若要檢視和進行想要的編輯，請在 Visual Studio 中開啟 .wadcfgx 檔案，該檔案儲存在 [雲端服務專案] -> [角色] -> (RoleName) -> [diagnostics.wadcfgx] 檔案，並且在完成時加以儲存。

此時，Visual Studio、Visual Studio Team System 或以 MSBUILD 為基礎的命令或指令碼中的所有部署和部署更新 (使用 /t:publish 目標)，將會在封裝程序中包含 .wadcfgx，並且確定使用與您的 VM 適合的適當 WAD 代理程式擴充將它部署到 Azure 中。
  
應用程式與 Azure 診斷組態部署成功時，您會立即看到事件中樞的儀表板中的活動。這表示您準備就緒繼續在接聽程式用戶端或您選擇的分析工具中檢視最忙碌路徑資料。
 
在下圖中，當事件中樞已部署更新的 .wadcfgx 並且正確設定接收時，事件中樞儀表板會顯示從下午 11 點開始，對事件中樞狀況良好的診斷資料傳送。

![][0]
  
>[AZURE.NOTE] 當您對 Azure 診斷組態檔 (.wadcfgx) 進行更新時，建議您將更新推送至整個應用程式，並且使用 Visual Studio 發佈或 Windows PowerShell 指令碼的設定。

## 檢視最忙碌路徑資料

如前文所述，接聽和處理事件中樞資料有許多使用案例。
  
一個簡單的作法是建立小型測試主控台應用程式，接聽事件中樞並列印輸出串流。下列程式碼 (會在[開始使用事件中樞](./event-hubs-csharp-ephcs-getstarted.md)文章中詳細說明) 會放在主控台應用程式。

請注意，主控台應用程式必須包含 [EventProcessor Nuget 封裝](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)。

請記得使用您的資源的值取代以下 **Main** 函數中角括弧裡面的值。

	//Console application code for EventHub test client
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	
	namespace EventHubListener
	{
	    class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	
	                foreach (var x in eventData.Properties)
	                {
	                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
	                }
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                this.checkpointStopWatch.Restart();
	            }
	        }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            string eventHubConnectionString = "Endpoint= <Your Connection String>”
	            string eventHubName = "<EventHub Name>";
	            string storageAccountName = "<Storage Account Name>";
	            string storageAccountKey = "<Storage Account Key>”;
	            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
	
	            string eventProcessorHostName = Guid.NewGuid().ToString();
	            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
	            Console.WriteLine("Registering EventProcessor...");
	            var options = new EventProcessorOptions();
	            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
	            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
	
	            Console.WriteLine("Receiving. Press enter key to stop worker.");
	            Console.ReadLine();
	            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
	        }
	    }
	}

## 針對事件中樞接收進行疑難排解

- 事件中樞不會如預期般顯示傳入或傳出事件活動

	檢查已成功佈建您的事件中樞。.wadcfgx 中 **PrivateConfig** 區段的所有連接資訊必須符合在入口網站中所示的資源的值。請確定您已在入口網站中定義 SAS 原則 (此範例中為 “SendRule”)，並且對其授與「傳送」權限。

- 執行更新之後，事件中樞不會再顯示傳入或傳出事件活動

	請先確定事件中樞和組態資訊符合上述所有項目。有些問題是因為 **PrivateConfig** 在部署更新中重設所導致。建議的修正方式是對專案中的 .wadcfgx 進行所有變更，然後再推送完整的應用程式更新。如果不可行，請確定診斷更新推送完整的 **PrivateConfig**，包括 SAS 金鑰。

- 我試過上述說明，事件中樞仍然無法運作

	請嘗試查看 Azure 儲存體資料表，其中包含記錄檔和 Azure 診斷本身的錯誤︰**WADDiagnosticInfrastructureLogsTable**。其中一個選項是使用例如 [Azure 儲存體總管](http://www.storageexplorer.com)的工具，連接到此儲存體帳戶，檢視此資料表，並且新增過去 24 小時內時間戳記的查詢。您可以使用工具來匯出 CSV，並且在例如 Microsoft Excel 的應用程式中開啟，因為 Excel 可讓您輕鬆地搜尋如「EventHubs」的電話卡字串，以查看報告了哪些錯誤。

## 後續步驟
• [深入了解事件中樞](https://azure.microsoft.com/services/event-hubs/)

## 附錄：完成 Azure 診斷組態檔 (.wadcfgx) 範例
	<?xml version="1.0" encoding="utf-8"?>
	<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
	        <Directories scheduledTransferPeriod="PT1M">
	          <IISLogs containerName="wad-iis-logfiles" />
	          <FailedRequestLogs containerName="wad-failedrequestlogs" />
	        </Directories>
	        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
	          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
	        </PerformanceCounters>
	        <WindowsEventLog scheduledTransferPeriod="PT1M">
	          <DataSource name="Application!*" />
	        </WindowsEventLog>
	        <CrashDumps>
	          <CrashDumpConfiguration processName="WaIISHost.exe" />
	          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
	          <CrashDumpConfiguration processName="w3wp.exe" />
	        </CrashDumps>
	        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
	      </DiagnosticMonitorConfiguration>
	      <SinksConfig>
	        <Sink name="HotPath">
	          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
	        </Sink>
	        <Sink name="applicationInsights">
	          <ApplicationInsights />
	          <Channels>
	            <Channel logLevel="Error" name="errors" />
	          </Channels>
	        </Sink>
	      </SinksConfig>
	    </WadCfg>
	    <StorageAccount />
	  </PublicConfig>
	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
	  </PrivateConfig>
	  <IsEnabled>true</IsEnabled>
	</DiagnosticsConfiguration>

此範例的補充 *ServiceConfiguration.Cloud.cscfg* 如下所示。

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
	  <Role name="MyFixIt.WorkerRole">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
	    </ConfigurationSettings>
	  </Role>
	</ServiceConfiguration>

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0406_2016-->