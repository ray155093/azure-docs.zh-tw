---
title: "Azure Service Fabric 監視和診斷概觀 | Microsoft Docs"
description: "了解如何監視和診斷裝載於 Azure、開發或內部部署的 Microsoft Azure Service Fabric 應用程式。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: ebbb29ee031fb477ce284f7a0d27c1522317f4f0
ms.openlocfilehash: 46a35fa4ec341561ab234f7ec19fb20658fcb2c4
ms.lasthandoff: 02/27/2017


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>監視和診斷 Azure Service Fabric 應用程式

在實際生產環境中，監視和診斷非常重要。 Azure Service Fabric 可協助您在開發服務時實作監視和診斷，以確保服務可以在單一機器的本機開發環境和實際的生產叢集安裝中順暢地運作。

監視和診斷可在您開發服務時協助您︰
* 盡可能不干擾您的客戶。
* 提供商業見解。
* 監視資源使用量。
* 偵測硬體與軟體錯誤或效能問題。
* 診斷可能的服務問題。

監視是一個廣義的詞彙，涵蓋下列工作︰
* 檢測程式碼
* 收集檢測記錄
* 分析記錄
* 以視覺化方式呈現從記錄資料獲得的見解
* 根據記錄值和觀點設定警示
* 監視基礎結構
* 偵測和診斷影響客戶的問題

對於 Azure 中、內部部署、部署在 Windows 或 Linux，或使用 Microsoft .NET Framework 裝載的 Service Fabric 叢集，本文提供監視方面的概觀。 我們探討監視和診斷的三個重要層面︰
- 檢測程式碼或基礎結構
- 收集產生的事件
- 儲存體、彙總、視覺效果和分析

雖然有很多產品已涵蓋所有這三個領域，但許多客戶會針對每個監視層面而選擇不同的技術。 重要的是每個環節能夠互相配合，為應用程式提供端對端的監視解決方案。

## <a name="monitoring-infrastructure"></a>監視基礎結構

Service Fabric 有助於在基礎結構失敗時讓應用程式繼續執行，但您必須了解錯誤是發生於應用程式，還是發生於基礎結構。 您也需要監視基礎結構來規劃容量，以了解何時該新增或移除基礎結構。 重點是監視組成 Service Fabric 部署的基礎結構和應用程式，並進行疑難排解。 即使應用程式可供客戶使用，基礎結構仍可能發生問題。

### <a name="azure-monitor"></a>Azure 監視器

您可以使用 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview.md)，監視構成 Service Fabric 叢集的許多 Azure 資源。 對於[虛擬機器擴展集](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)和個別的[虛擬機器](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)，Azure 入口網站會自動收集並顯示一組計量。 若要檢視收集的資訊，請在 Azure 入口網站中，選取包含 Service Fabric 叢集的資源群組。 接著，選取您想要檢視的虛擬機器擴展集。 在 [監視] 區段中，選取 [計量]，以檢視圖表中的值。

![已收集計量資訊的 Azure 入口網站檢視](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.PNG)

若要自訂圖表，請依照 [Microsoft Azure 中的計量](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)中的指示。 您也可以根據這些計量建立警示，如[在 Azure 監視器中建立 Azure 服務的警示](../monitoring-and-diagnostics/insights-alerts-portal.md)所述。 您可以使用 Web 攔截來傳送警示給通知服務，如[在 Azure 計量警示上設定 Web 攔截](../monitoring-and-diagnostics/insights-webhooks-alerts.md)所述。 Azure 監視器只支援一個訂用帳戶。 如果您需要監視多個訂用帳戶，或需要其他功能，Microsoft Operations Management Suite 中的 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 已經為內部部署和雲端式基礎結構，提供全面性的 IT 管理解決方案。 您可以將 Azure 監視器的資料直接路由傳送至 Log Analytics，讓您在同一個地方看到整個環境的計量與記錄。

建議您使用 Operations Management Suite 來監視內部部署基礎結構，但也可以使用您的組織已用來監視基礎結構的任何現有解決方案。

### <a name="service-fabric-support-logs"></a>Service Fabric 支援記錄

如果您需要連絡 Microsoft 支援服務以協助處理您的 Azure Service Fabric 叢集，通常都需要提供支援記錄。 如果您的叢集裝載於 Azure，建立叢集時會自動設定和收集支援記錄。 記錄會儲存在叢集資源群組中的專用儲存體帳戶。 儲存體帳戶沒有固定名稱，但您會在帳戶中看到名稱以 fabric 開頭的 blob 容器和資料表。 如需有關為獨立叢集設定記錄收集的資訊，請參閱[建立和管理獨立 Azure Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)和[獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)。 若為獨立 Service Fabric 執行個體，記錄應該傳送至本機檔案共用。 您**需要**擁有這些記錄才能取得支援，而這些記錄只限 Microsoft 客戶支援小組使用。

## <a name="instrument-your-code"></a>實作您的程式碼

檢測程式碼是其他監視服務層面的基礎。 為了讓您知道有問題發生，並診斷有什麼需要修正，檢測是唯一方式。 雖然技術上可以將偵錯工具連線至生產服務，但這不是常見的做法。 因此，取得詳細的檢測資料很重要。 當您產生如此大量的資訊時，從本機節點移出所有事件的成本會很高。 許多服務會使用兩段式策略來處理檢測資料量：
1.  所有事件會短暫保留在本機循環記錄檔案中，只在偵錯有需要時才收集。 詳細診斷需要的事件通常會留在節點上，以降低成本和資源使用率。
2.  指出服務健全狀況的任何事件會傳送至中央儲存機制，可在發現狀況不良的服務時發出警示。 服務健全狀況事件包括錯誤事件、活動訊號事件和效能事件。

某些產品會自動檢測您的程式碼。 雖然這些產品的效果不錯，但幾乎都還是需要手動檢測。 最後您還是要有足夠資訊，才能以抽絲剝繭的方式對應用程式進行偵錯。 下列幾節說明檢測程式碼的不同方法，以及在什麼情況下選擇哪一個方法。

### <a name="eventsource"></a>EventSource

當您在 Visual Studio 中從範本建立 Service Fabric 解決方案時，將會產生 **EventSource** 衍生類別 (**ServiceEventSource** 或 **ActorEventSource**)。 建立的範本可讓您為應用程式或服務新增事件。 **EventSource** 名稱**必須**是獨一無二，並應該將預設範本字串 MyCompany-&lt;solution&gt;-&lt;project&gt; 重新命名。 多個同名的 **EventSource** 定義會導致執行階段發生問題。 每個已定義事件都必須有獨一無二的識別碼。 如果識別碼並非獨一無二，會發生執行階段失敗。 有些組織會預先指派識別碼的值範圍，以避免不同開發小組之間用法不一致。 如需詳細資訊，請參閱 [Vance 的部落格](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)或 [MSDN 文件](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)。

#### <a name="using-structured-eventsource-events"></a>使用結構化 EventSource 事件

在本節的程式碼範例中，每個事件都是針對特定案例而定義，例如註冊服務類型。 當您依使用案例定義訊息時，資料可以和錯誤文字一起封裝，讓您依據指定屬性的名稱或值，更輕鬆地搜尋和篩選。 建構檢測輸出可讓您更容易取用，但需要更多考量和時間為每個使用案例定義新事件。 某些事件定義可以在整個應用程式內共用。 例如，應用程式內的許多不同服務會重複使用方法啟動或停止事件。 特定網域服務有自己的獨特事件，例如，訂單系統可能有 **CreateOrder** 事件。 此方法可能產生很多事件，專案小組之間可能需要協調識別碼。 如需 Service Fabric 中 **EventSource** 事件的完整範例，請參閱 Party Cluster 範例中的 **PartyCluster.ApplicationDeployService** 事件。

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

#### <a name="using-eventsource-generically"></a>以一般方式使用 EventSource

因為定義特定事件可能很困難，許多人會以一組常用的參數定義少量事件，且通常會將資訊輸出為字串。 大部分的結構層面會遺失，因此更難搜尋和篩選結果。 此方法定義的少量事件通常對應至記錄層級。 下列程式碼片段定義偵錯和錯誤訊息：

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```

使用混合式的結構化和泛型檢測也可行。 結構化檢測用於報告錯誤和計量。 泛型事件可用來產生詳細記錄，讓工程師用於疑難排解。

### <a name="aspnet-core-logging"></a>ASP.NET Core 記錄

必須仔細規劃您檢測程式碼的方式。 正確規劃檢測有助於避免可能使程式碼基底不穩定，導致需要重新檢測程式碼。 為了降低風險，開發人員可以選擇檢測程式庫，例如 Microsoft ASP.NET Core 中的 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。 ASP.NET Core 提供 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 介面，可搭配您選擇的提供者一起使用，讓現有程式碼所受的影響降到最低。 您可以在 Windows 和 Linux 上使用 ASP.NET Core 中的程式碼，也可以使用完整 .NET Framework 中的程式碼，使檢測程式碼標準化。

#### <a name="using-microsoftextensionslogging-in-service-fabric"></a>使用 Service Fabric 中的 Microsoft.Extensions.Logging

1. 將 Microsoft.Extensions.Logging NuGet 套件新增至您要檢測的專案。 另外也新增任何提供者套件 (若是協力廠商套件，請參閱下列範例)。 如需詳細資訊，請參閱 [ASP.NET Core 中的記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。
2. 為 Microsoft.Extensions.Logging 將 **using** 指示詞新增至您的服務檔案。
3. 在服務類別內定義私用變數。

  ```csharp
  private ILogger _logger = null;

  ```
4. 在服務類別的建構函式中，新增此程式碼。

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. 開始用您的方法檢測程式碼。 以下是幾個範例：

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

#### <a name="using-other-logging-providers"></a>使用其他記錄提供者

某些協力廠商提供者會使用上節所述的方法，包括 [Serilog](https://serilog.net/)、[NLog](http://nlog-project.org/) 和 [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging)。 您可以將這些全部插入 ASP.NET Core 記錄中，也可以分開使用。 Serilog 有一項功能讓記錄器傳來的所有訊息更豐富。 這項功能可用來輸出服務名稱、類型和資料分割資訊。 若要在 ASP.NET Core 基礎結構中使用這項功能，請執行下列步驟：

1. 將 Serilog、Serilog.Extensions.Logging 和 Serilog.Sinks.Observable NuGet 套件新增至專案。 下一個範例中也新增 Serilog.Sinks.Literate。 本文稍後會示範更好的方法。
2. 在 Serilog 中，建立 LoggerConfiguration 和記錄器執行個體。

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. 將 SeriLog.ILogger 引數新增至服務建構函式，並傳遞新建立的記錄器。

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. 在服務建構函式中，新增下列程式碼。 此程式碼為服務的 **ServiceTypeName**、**ServiceName**、**PartitionId** 和 **InstanceId ** 屬性建立屬性豐富器。 它也會將屬性豐富器新增至 ASP.NET Core 記錄工廠，讓您在程式碼中使用 Microsoft.Extensions.Logging.ILogger。

  ```csharp
  public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
      : base(context)
  {
      PropertyEnricher[] properties = new PropertyEnricher[]
      {
          new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
          new PropertyEnricher("ServiceName", context.ServiceName),
          new PropertyEnricher("PartitionId", context.PartitionId),
          new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
      };

      serilog.ForContext(properties);

      _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
  }
  ```

5. 檢測程式碼，就如同在沒有 SeriLog 的情況下使用 ASP.NET Core 一樣。

  > [!NOTE]
  > 我們建議您不要在上述範例中使用靜態 Log.Logger。 Service Fabric 可以在單一處理序內裝載相同服務類型的多個執行個體。 如果您使用靜態 Log.Logger，屬性豐富器的最後一個寫入器會顯示所有執行中執行個體的值。 這是為什麼 _logger 變數是服務類別私人成員變數的其中一個原因。 此外，您還必須使 _logger 可供各服務可能用到的通用程式碼使用。

### <a name="choosing-a-logging-provider"></a>選擇記錄提供者

如果您的應用程式仰賴高效能，最佳方式是使用 **EventSource**。 **EventSource**「通常」耗用較少資源，效能優於 ASP.NET Core 記錄或任何可用的協力廠商解決方案。  這對於許多服務而言並不是問題，但如果您的服務屬於效能導向，使用 **EventSource** 可能是更好的選擇。 為了發揮像結構化記錄一樣的優點，**EventSource** 需要工程團隊投注大量心力。 若要決定適用於專案的方法，請迅速建構雛形來了解每個選項可能的結果，然後選擇最符合需求的方法。

## <a name="event-and-log-collection"></a>事件和記錄集合

### <a name="azure-diagnostics"></a>Azure 診斷

除了 Azure 監視器提供的資訊，Azure 也會集中收集每個服務的事件。 如需詳細資訊，請了解如何為 [Windows](service-fabric-diagnostics-how-to-setup-wad.md) 和 [Linux](service-fabric-diagnostics-how-to-setup-lad.md) 設定事件收集。 這些文章示範如何收集事件資料並傳送至 Azure 儲存體。 做法是在 Azure 入口網站或 Azure Resource Manager 範本中啟用診斷。 Azure 診斷會收集 Service Fabric 自動產生的一些事件來源︰

- 當您使用 Reliable Actor 程式設計模型時的 **EventSource** 事件和效能計數器。 [Reliable Actors 的診斷和效能監視](service-fabric-reliable-actors-diagnostics.md)中列舉這些事件。
- 當您使用 Reliable Services 程式設計模型時的 **EventSource** 事件。 [具狀態 Reliable Services 的診斷功能](service-fabric-reliable-services-diagnostics.md)中列舉這些事件。
- 系統事件以 Windows 事件追蹤 (ETW) 事件的形式發出。 從 Service Fabric 發出的許多事件都屬於此類別，包括服務放置和啟動/停止事件。 若要查看發出的事件，最佳方式是在本機電腦上使用 [Visual Studio 診斷事件檢視器](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 由於這些事件是原生 ETW 事件，因此收集方式有所限制。
- 從 Service Fabric 5.4 版開始，健全狀況和負載計量事件都會公開。 因此，您可以使用事件收集來產生歷程記錄報告和警示。 這些事件也是原生 ETW 事件，收集方式有所限制。

設定之後，事件會出現在您建立叢集時所建立的 Azure 儲存體帳戶中 (假設已啟用診斷)。 資料表的名稱是 WADServiceFabricReliableActorEventTable、WADServiceFabricReliableServiceEventTable 和 WADServiceFabricSystemEventTable。 依預設不會新增健全狀況事件，您必須修改 Resource Manager 範本來新增這種事件。 如需詳細資訊，請參閱[使用 Azure 診斷收集記錄](service-fabric-diagnostics-how-to-setup-wad.md)。

本節列出的這些文件也示範如何將自訂事件放入 Azure 儲存體。 其他有關設定效能計數器的 Azure 診斷文件，或有關從虛擬機器取得其他監視資訊給 Azure 診斷的文件，也都適用於 Service Fabric 叢集。 例如，如果您不要使用 Azure 資料表儲存體做為目的地，請參閱[使用事件中樞串流最忙碌路徑中的 Azure 診斷資料](../event-hubs/event-hubs-streaming-azure-diags-data.md)。 當 Azure 事件中樞裡出現事件時，您可以讀取事件並傳送至您選擇的位置。 您可以取得有關整合 [Azure 診斷訊與 Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/) 的詳細資訊。

使用 Azure 診斷的缺點在於您必須使用 Resource Manager 範本來設定。 因此，只會在虛擬機器擴展集層級上診斷。 虛擬機器擴展集對應至 Service Fabric 中節點類型。 針對各類型的節點上可能執行的所有應用程式和服務，您應該設定每個節點類型。 根據您設定的應用程式和服務數量而定，這可能是許多 **EventSource** 事件。 每當應用程式組態變更時，您也必須部署 Resource Manager。 監視組態和服務組態一起傳送時最理想。

Azure 診斷只適用於部署至 Azure 的 Service Fabric 叢集。 也適用於 Windows 和 Linux 叢集。

### <a name="eventflow"></a>EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) 可以將節點的事件路由傳送至一個或多個監視目的地。 由於是以 NuGet 套件形式加入服務專案中，EventFlow 程式碼和組態會隨著服務一起傳送，不會發生稍後提及有關 Azure 診斷的個別節點組態問題。 EventFlow 在您的服務處理序中執行，並直接連線到已設定的輸出。 因為是直接連線，所以 EventFlow 適用於 Azure、容器和內部部署的服務部署。 如果您在高密度情況下執行 EventFlow，例如在容器中，請務必小心，因為每個 EventFlow 管線來建立外部連線。 如果您裝載許多處理序，將會產生大量輸出連線！ 這對 Service Fabric 應用程式來說不會有什麼問題，因為 `ServiceType` 的所有複本都在同一處理序中執行，也就限制輸出連線的數量。 EventFlow 也提供事件篩選，因此只會傳送符合指定篩選的事件。 如需有關搭配 Service Fabric 使用 EventFlow 的詳細資訊，請參閱[直接從 Azure Service Fabric 服務處理序收集記錄](service-fabric-diagnostic-collect-logs-without-an-agent.md)。

若要使用 EventFlow：

1. 將 Nuget 套件新增至您的服務專案。
2. 在服務的 **Main** 函式中建立 EventFlow 管線，然後設定輸出。 在下列範例中，我們使用 Serilog 做為輸出。

  ```csharp
  internal static class Program
  {
      /// <summary>
      /// This is the entry point of the service host process.
      /// </summary>
      private static void Main()
      {
          try
          {
              using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
              {
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
                  Thread.Sleep(Timeout.Infinite);
              }
          }
          catch (Exception e)
          {
              ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
              throw;
          }
      }
  }
  ```

3. 在服務的 \\PackageRoot\\Config 資料夾中，建立名為 eventFlowConfig.json 的檔案。 檔案中的組態看起來如下所示：

  ```json
      {
      "inputs": [
          {
          "type": "EventSource",
          "sources": [
              { "providerName": "Microsoft-ServiceFabric-Services" },
              { "providerName": "Microsoft-ServiceFabric-Actors" },
              { "providerName": "MyCompany-MonitoringE2E-Stateless" }
          ]
          },
          {
          "type": "Serilog"
          }
      ],
      "filters": [
          {
          "type": "drop",
          "include": "Level == Verbose"
          },
          {
          "type": "metadata",
          "metadata": "request",
          "requestNameProperty": "RequestName",
          "include":  "RequestName==MyRequest",
          "durationProperty": "Duration",
          "durationUnit": "milliseconds"
          }
      ],
      "outputs": [
          {
          "type": "StdOutput"
          },
          {
          "type": "ApplicationInsights",
          "instrumentationKey": "== instrumentation key here =="
          }
      ],
      "schemaVersion": "2016-08-11",
      "extensions": []
      }
  ```
    組態中定義兩個輸入：由 Service Fabric 建立的兩個 **EventSource** 型來源，以及服務的 **EventSource**。 使用 ETW 的系統層級和健全狀況事件無法供 EventFlow 使用。 這是因為需要高層級權限才可接聽 ETW 來源，但永遠不應該以高權限執行服務。 另一個輸入是 Serilog。 Serilog 組態位於 **Main** 方法中。  套用一些篩選。 第一個篩選會指示 EventFlow 捨棄事件層級為詳細資訊的所有事件。 設定兩個輸出：標準輸出 (寫入 Visual Studio 中的輸出視窗) 和 ApplicationInsights。 請確定您已新增檢測金鑰。

4. 檢測程式碼。 在下一個範例中，我們以幾種不同方式來示範檢測 `RunAsync`。 在下列程式碼中，我們仍使用 Serilog。 我們使用 Serilog 特有的一些語法。 請注意您所選擇之記錄解決方案的特定功能。 產生三個事件︰一個偵錯層級事件和兩個參考事件。 第二個參考事件會追蹤要求持續時間。 在稍早所述的 EventFlow 組態中，偵錯層級事件不應該流向輸出。

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

若要檢視 Azure Application Insights 中的事件，請在 Azure 入口網站中移至您的 Application Insights 資源。 若要查看事件，請選取 [搜尋] 方塊。

![Application Insights 搜尋事件檢視](./media/service-fabric-diagnostics-overview/ai-search-events.PNG)

您可以在前一個螢幕擷取畫面底部看到追蹤。 其中只顯示兩個事件，EventFlow 已捨棄偵錯層級事件。 追蹤之前的要求項目是第三行 `_logger` 檢測。 第一行顯示事件已在 Application Insights 中轉譯成要求計量。

在篩選定義中，類型是**中繼資料**。 這宣告具有屬性 `RequestName` (值為 `MyRequest`) 和另一個屬性 `Duration` 的事件包含要求的持續時間 (以毫秒為單位)。 這是您在 Application Insights 的要求事件中可以看到的資訊。 相同的方法適用於任何支援的 EventFlow 輸入，包括 **EventSource**。

如果您的獨立叢集基於原則理由，而無法連線至雲端式解決方案，您可以使用 Elasticsearch 做為輸出。 不過，可以撰寫其他輸出，也建議使用提取要求。 ASP.NET Core 記錄的一些協力廠商提供者，也有支援內部部署安裝的解決方案。

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric 健全狀況和負載報告

Service Fabric 有自己的健全狀況模型，詳述於下列文件：
- [Service Fabric 健全狀況監視簡介](service-fabric-health-introduction.md)
- [回報和檢查服務健全狀況](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [新增自訂 Service Fabric 健康狀態報告](service-fabric-report-health.md)
- [檢視 Service Fabric 健康狀態報告](service-fabric-view-entities-aggregated-health.md)

對於服務運作的許多層面而言，健全狀況監視不可或缺。 當 Service Fabric 執行具名的應用程式升級時，健全狀況監視尤其重要。 當服務的每個升級網域完成升級並提供給客戶之後，升級網域必須通過健全狀況檢查，才能繼續部署至下一個升級網域。 如果無法達到良好的健全狀態，部署就會復原，讓應用程式保持已知的良好狀態。 雖然在服務復原之前會影響一些客戶，但大部分客戶都不會遇到問題。 此外，很快就會開始解決，無需等待操作人員採取行動。 在程式碼中納入越多健全狀況檢查，服務越能夠從部署問題中恢復。

服務健全狀況的另一個層面是來自服務的報告計量。 計量是用來平衡資源使用量，在 Service Fabric 中很重要。 計量也可以當做系統健全狀況的指標。 例如，您的應用程式可能有許多服務，而每個執行個體會報告每秒要求數 (RPS) 計量。 如果有一項服務比其他服務使用更多資源，Service Fabric 會在叢集內移動服務執行個體，嘗試維護平衡的資源使用量。 如需資源耗用量運作方式的詳細說明，請參閱[在 Service Fabric 中使用計量管理資源耗用量和負載](service-fabric-cluster-resource-manager-metrics.md)。

計量也可協助您深入探索服務的運作方式。 經過一段時間，您可以使用計量，檢查服務是否在預期的參數內運作。 比方說，如果趨勢指出星期一早上 9 點的平均 RPS 是 1,000，您可能將健全狀況報告設定為當 RPS 低於 500 或高於 1,500 時發出警示。 一切可能都沒問題，但您可能需要檢查一下，確保客戶擁有絕佳的體驗。 您的服務可以定義一組計量，供健全狀況檢查時報告，但又不影響叢集的資源平衡。 若要這樣做，請將計量權數設為零。 我們建議您在開始時將所有計量的加權設為零，並且在確定您了解計量加權會如何影響叢集的資源平衡前，都不要提高權數。

> [!TIP]
> 請勿使用太多的加權計量。 很難了解服務執行個體為了平衡而移動的原因。 少數幾個計量就很夠用！

任何能夠指出應用程式健全狀況和效能的資訊，都適合納入計量和健全狀況報告中。 CPU 效能計數器可以告訴您節點的使用情況，但無法讓您知道某個特定服務是否健康，因為可能有多項服務在單一節點上執行。 不過，像是 RPS、處理的項目和求延遲等計量，都可以指出特定服務的健全狀況。

若要報告健全狀況，請使用如下的程式碼︰

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

若要報告計量，請使用如下的程式碼︰

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

## <a name="watchdogs"></a>監視程式

監視程式是個別的服務，可以觀察服務之間的健全狀況和負載，並報告健全狀況模型階層中任何項目的健全狀況。 如此可避免在單一服務檢視中無法偵測到的錯誤。 監視程式也適合裝載能夠針對已知狀況執行修復動作的程式碼，而無需使用者互動。

## <a name="visualization-analysis-and-alerts"></a>視覺效果、分析和警示

監視的最後一個部分是視覺化事件資料流、報告服務效能以及在偵測到問題時發出警示。 針對監視的這個部分，您可以使用不同的解決方案。 您可以使用 Azure Application Insights 和 Operations Management Suite，根據事件資料流發出警示。 您可以使用 Microsoft Power BI 或協力廠商解決方案 (例如 [Kibana](https://www.elastic.co/products/kibana) 或 [Splunk](https://www.splunk.com/)) 將資料視覺化。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 診斷收集記錄](service-fabric-diagnostics-how-to-setup-wad.md)
* [直接從 Azure Service Fabric 服務處理序收集記錄](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [在 Service Fabric 中使用計量管理資源耗用量和負載](service-fabric-cluster-resource-manager-metrics.md)

