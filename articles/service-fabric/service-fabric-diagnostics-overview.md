---
title: "Azure Service Fabric 監視和診斷概觀 | Microsoft Docs"
description: "學習如何監視和診斷裝載於 Azure、開發或內部部署的 Microsoft Azure Service Fabric 應用程式。"
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: a83fe451ec8b77b8b84575c5fd46f3661fc261c0
ms.openlocfilehash: 77e5872654aa6f50b5a6564fb8ee8c7a6a4c29a5


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>監視和診斷 Azure Service Fabric 應用程式

在對使用者體驗只有少量干擾而可以繼續服務的情況下，進行監視、偵測、診斷與疑難排解等項目並提供商業見解、監視資源使用量、偵測硬體和軟體失敗或效能問題、診斷潛在的服務行為問題。 雖然監視和診斷在實際部署的生產環境中相當重要，然而效率取決於服務開發期間採用類似的模型服務，以確保移動至實際設定時能正常運作。 Service Fabric 可讓服務開發人員輕鬆實作診斷，可以在單一電腦本機開發設定和實際生產叢集設定上順暢地工作。 

監視是廣義的詞彙，包含檢測程式碼、收集檢測記錄、分析記錄、根據記錄資料塑造見解、根據記錄值和見解發出警示、監視基礎結構和讓工程師偵測及診斷影響客戶的問題。 本文旨在提供 Service Fabric 叢集監視的概觀，包含裝載於 Azure 或內部部署、使用 .NET 部署在 Windows 或 Linux 上的叢集。 讓我們先將問題分成三個部分
- 檢測程式碼或基礎結構
- 產生事件的集合
- 儲存體、彙總、視覺效果和分析

雖然有部分產品會完全涵蓋這三個層面，許多客戶仍然會為每個領域選擇不同的技術。 重要的是每項技術能夠互相配合，為應用程式提供端對端的監視解決方案。 

## <a name="monitoring-infrastructure"></a>監視基礎結構

雖然 Service Fabric 在基礎結構失敗時能讓應用程式繼續執行，但應用程式運算子需要了解錯誤是發生於應用程式內部，還是發生於基礎結構。 容量規劃亦需要監視基礎結構，以找出何時要新增或移除基礎結構。 組成 Service Fabric 部署的基礎結構和應用程式對監視和疑難排解很重要。 只要客戶可以使用應用程式，一些基礎結構便有可能發生問題。

### <a name="azure-monitoring"></a>Azure 監視

若為 Azure 部署的叢集，[Azure 監視](../monitoring-and-diagnostics/monitoring-overview.md)讓您能夠監視建立 Service Fabric 叢集的多個 Azure 資源。 系統會自動收集一組計量並顯示在 Azure 入口網站，供[虛擬機器擴展集 (VMSS) ](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)和個別 [VM](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) 使用。 您可以選取包含 Service Fabric 叢集的資源群組，並選擇 [虛擬機器擴展集] 以在 Azure 入口網站檢視此資訊。 然後在 [監視] 導覽章節選擇 [計量] 以檢視不同值的圖表

![已收集計量資訊的 Azure 入口網站檢視](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

您可以依照 [Microsoft Azure 計量概觀](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)文章中的指示自訂圖表。 您也可以依照[使用 Azure 入口網站建立 Azure 服務警示](../monitoring-and-diagnostics/insights-alerts-portal.md)一文所述，根據這些計量建立警示。 警示可以使用 webhook 傳送至通知服務，如 [在 Azure 計量警示上設定 webhook(../monitoring-and-diagnostics/insights-webhooks-alerts.md) 文章所述。 Azure 監視支援單一訂用帳戶。 如果需要支援多個訂用帳戶或其他功能，Operations Management Suite 中的 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 可為內部部署和雲端式基礎結構提供全面性的 IT 管理解決方案。 Azure 監視的資料可以直接路由至 Log Analytics，以便您可以在同一個地方看到整個環境的計量與記錄。

建議您使用 Operations Management Suite 方法來監視內部部署基礎結構，但您也可以使用貴公司現有的任何基礎結構監視解決方案。

### <a name="service-fabric-support-logs"></a>Service Fabric 支援記錄

如果您需要連絡 Microsoft 支援以取得 Azure Service Fabric 叢集的幫助，通常都需要提供支援記錄。 如果您的叢集裝載於 Azure 中，系統會自動設定和收集這些記錄作為建立叢集的一部分。 記錄會儲存在專用儲存體帳戶中，讓您的叢集資源群組可以看到。 儲存體帳戶沒有固定名稱，但您會在帳戶中看到 blob 容器和以 'fabric' 開頭的資料表。 如果您的叢集是獨立的叢集，應該遵循[建立和管理獨立的 Azure Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)和[獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)的指引來設定，以收集這些記錄。 若為獨立的 Service Fabric，記錄應該傳送至本機檔案共用。 您**需要**擁有這些記錄才能取得支援，而這些記錄只限 Microsoft 客戶支援小組使用。 我認為，您不會對這些記錄感興趣…

## <a name="instrument-your-code"></a>實作您的程式碼

檢測程式碼是其他監視服務層面的基礎。 檢測非常必要，許多人卻對此感到意外，但考慮到檢測是了解發生什麼問題和診斷需要修正事項的唯一方法，其實這很正常。 雖然技術上可行，但我們通常不會把偵錯工具連接至生產服務，所以詳細的檢測相當重要。 當產生如此大量的資訊時，把所有事件傳送至本機節點外的費用可能十分昂貴。 許多服務會使用兩段式策略來處理檢測資料量：
* 所有事件都會在本機循環記錄檔案中保留短暫數天，當需要偵錯時才收集。 詳細診斷需要的事件通常會留在節點上，以減低成本和善用資源
* 任何指出服務健全狀況的事件，例如錯誤事件、活動訊號事件或效能事件，都會傳送至中央存放庫，並用來發出不健全服務的警示。

有些解決方案能自動檢測您的程式碼。 雖然這些產品的效果不錯，但手動檢測通常是必需的。 最後您還是要有足夠資訊，才能以抽絲剝繭的方式對應用程式進行偵錯。 下節說明檢測程式碼的其他方法，以及在什麼情況下應該選擇哪些方法。

### <a name="eventsource"></a>EventSource

當您從 Visual Studio 的範本建立 Azure Service Fabric 解決方案時，會產生 EventSource 衍生類別 (ServiceEventSource 或 ActorEventSource)。 提供的範本讓您可以新增適合您應用程式或服務的其他事件。 EventSource 名稱**必須**是獨一無二的，並應該從 'MyCompany-&lt;solution&gt;-&lt;project&gt;' 的起始字串開始重新命名。 如果多個 EventSource 定義使用相同名稱，會導致執行階段發生問題。 每個已定義事件都必須有獨一無二的識別碼。 如果識別碼並非獨一無二，會發生執行階段失敗。 一般來說，識別項都有預先指派的值範圍，以避免個別開發小組間的衝突。 如需有關 EventSource 的其他資訊，請參閱 [Vance 的部落格](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)或[ MSDN 文件](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)。

#### <a name="using-structured-eventsource-events"></a>使用結構化 EventSource 事件

下列每個事件都定義為特定使用案例，例如註冊為服務類型。 用這種方式定義訊息可讓資料與錯誤文字一起封裝。 也可根據特定屬性的名稱或值進行更完善的搜尋和篩選。 建構檢測輸出可讓您更容易取用，但需要更多考量和時間為每個使用案例定義新事件。 部分事件定義可能由整個應用程式共用，例如方法啟動或停止事件會在應用程式中由不同服務重新使用。 特定網域的服務例如訂單系統，可能包含 CreateOrder 事件並具備自己的獨特事件。 此方法通常會產生很多事件，可能需要專案小組之間協調識別碼。 如需 Service Fabric 中 EventSources 結構的完整範例，請參閱 Party Cluster 範例中的 [PartyCluster.ApplicationDeployService.ServiceEventSource](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster/blob/master/src/PartyCluster.ApplicationDeployService/ServiceEventSource.cs)。

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        // ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```
#### <a name="using-eventsource-generically"></a>以一般方式使用 EventSource

因為定義特定事件可能很困難，許多人會以一組常用的參數定義少量事件，且通常會將資訊輸出為字串。 大部分的結構層面會遺失，因此更難搜尋和篩選結果。 系統通常會透過此方法，定義對應記錄層級的少數事件。 下列程式碼片段會定義偵錯和錯誤訊息。
```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
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
使用結構化和泛型檢測的混合式方法也可行。 此案例使用結構化檢測報告錯誤和計量，而泛型檢測可用於工程師在進行疑難排解時所取用的詳細記錄。

### <a name="aspnet-core-logging"></a>ASP.NET Core 記錄

選擇檢測程式碼的方式可能很困難，如果您的選擇有誤而要重新檢測，則必須重新造訪程式碼基底，而且可能造成程式碼基底不穩定。 若要降低風險，開發人員可以選擇檢測程式庫，例如 ASP.NET Core 提供的 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。 所提供的 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 介面讓您選擇想使用的提供者，並將對現有程式碼的影響降到最低。 另一個好處是，程式碼不但可以在 Windows 和 Linux 的 .NET Core 中使用，也可用於整個 .NET 架構中，讓您可以標準化 .NET 和 .NET Core 之間的檢測節點。

#### <a name="how-to-use-microsoftextensionslogging-within-service-fabric"></a>如何在 Service Fabric 中使用 Microsoft.Extensions.Logging

1. 將 **Microsoft.Extensions.Logging** NuGet 套件新增至您在檢測的專案。 您可能也希望新增其他提供者套件，我們在下方提供了第三方廠商套件的使用方法。 請參閱[登入 ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) 以取得詳細資訊

2. 為 'Microsoft.Extensions.Logging' 將 **using** 指示詞新增至您的服務檔案

3. 在您的服務類別中定義私人變數

    ```csharp
        private ILogger _logger = null;
    ```

4. 在您的服務類別建構函式中，新增
    
    ```csharp
        _logger = new LoggerFactory().CreateLogger<Stateless>();
    ```

5. 開始用您的方法檢測程式碼。 以下是幾個範例
    
    ```csharp

        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");

        // In this variant, we're adding structured properties RequestName and Duration that has values MyRequest and the duration of the request.
        // More on why you'll want to do this later.
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

    ```

#### <a name="using-other-logging-providers"></a>使用其他記錄提供者

有一些第三方廠商提供者使用此方法，例如 [SeriLog](https://serilog.net/)、[NLog](http://nlog-project.org/) 和 [loggr](https://github.com/imobile3/Loggr.Extensions.Logging)。 每一種都可插入至 ASP.Net Core 記錄中，也可以獨立使用。 SeriLog 其中一個功能是豐富所有由記錄器傳送的訊息，在輸出服務名稱、類型和分割區資訊時十分實用。 若要在 ASP.NET Core 基礎結構中使用這項功能，請執行以下動作

1. 將 **Serilog**、**Serilog.Extensions.Logging**、**Serilog.Sinks.Observable** NuGet 套件新增至專案。 在此範例中也新增了 **SeriLog.Sinks.Literate**，本文稍後會提供更好的方法
2. 在 SeriLog 中建立 LoggerConfiguration 和記錄器執行個體

    ```csharp

        Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();

    ```

3. 將 SeriLog.ILogger 引數新增至服務的建構函式中，並傳遞新建立的記錄器
    
    ```csharp

        ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();

    ```

4. 在服務建構函式中新增下列命令，為ServiceTypeName、ServiceName、PartitionId 和 InstanceId 等服務屬性建立屬性豐富器。 它也會新增至 ASP.NET Core 記錄工廠，讓 Microsoft.Extensions.Logging.ILogger 可用於您的程式碼。
    
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
> 我們不建議在這個方法中使用靜態 Log.Logger，因為 Service Fabric 可以在單一處理序中裝載同一服務類型的多個執行個體。 這表示屬性豐富器的最後一個寫入器，會向所有執行中的執行個體顯示其值。 這是為什麼 _logger 變數是服務類別私人成員變數的其中一個原因。 這也表示，_logger 必須可於常見程式碼使用，並可能於服務間使用。 

### <a name="which-one-should-i-use"></a>應該使用哪一種方法？

如果效能對您的應用程式很重要，那麼使用 EventSource 是最好的方法，因為 EventSource **通常**使用較少資源，而且效果優於使用 ASP.NET Core 記錄或其他第三方廠商解決方案。  這對於許多服務而言並不是問題，但如果您的服務屬於效能導向，使用 EventSource 可能是更好的選擇。 若要獲得與結構化記錄相同的優點，EventSource 需要工程團隊投注大量心力。 要為專案選擇何種方案，最好的方法是針對每個方案建立一套快速的標準，然後選擇最符合您需求的方案。

## <a name="event-and-log-collection"></a>事件和記錄集合

### <a name="azure-diagnostics"></a>Azure 診斷

除了 Azure 監視已提供的功能，Azure 也提供從每個服務中將事件收集到中央位置的功能。 有兩篇文章說明如何為 [Windows](service-fabric-diagnostics-how-to-setup-wad.md) 和 [Linux](service-fabric-diagnostics-how-to-setup-lad.md) 設定事件集合。 這些文章中包含收集事件資料並傳送到 Azure 儲存體的資訊。 透過在入口網站或 Resource Manager 範本啟用診斷功能便能輕鬆做到。 開啟這個功能會收集一些 Service Fabric 自動產生的事件來源︰

- 使用 Reliable Actor 程式設計模型時的 EventSource 事件和效能計數器。 這些事件在 [Reliable Actors 的診斷和效能監視](service-fabric-reliable-actors-diagnostics.md)一文中有列舉出來
- 使用 Reliable Services 程式設計模型時的 EventSource 事件。 這些事件在[適用於可設定狀態之 Reliable Services 的診斷功能](service-fabric-reliable-services-diagnostics.md)一文中有列舉出來
- 系統事件會作為 ETW 事件發出。 Service Fabric 發出很多事件是此類別的一部份，包含服務位置、啟動/停止事件。 若要查看發出的事件，最佳方式是在本機電腦上執行時，使用 [Visual Studio 診斷檢視器](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 由於這些是原生的 ETW 事件，因此對於如何收集這些事件的方式有一些限制
- 從 Service Fabric 5.4 版開始，健全狀況和負載計量事件都會公開。 如此可讓這些事件的集合能夠在歷程記錄報告和警示中使用。 這些也是原生的 ETW 事件，因此對於如何收集這些事件的方式有一些限制

在設定時，這些事件會在建立叢集時所建立的 Azure 儲存體帳戶中出現，並且假設已啟用診斷功能。 資料表的名稱是 *WADServiceFabricReliableActorEventTable*、*WADServiceFabricReliableServiceEventTable* 和 *WADServiceFabricSystemEventTable*。 依預設，系統不會新增健全狀況事件，而是需要修改 Resource Manager 範本。 請參閱[使用 Azure 診斷收集記錄](service-fabric-diagnostics-how-to-setup-wad.md)以取得詳細資訊。

這些文章也會示範如何將自訂事件儲存到 Azure 儲存體。 任何有關如何設定效能計數器的 Azure 診斷文章，或其他從 VM 到 Azure 診斷的監視資訊，也適用於 Service Fabric 叢集。 例如，如果 Azure 資料表儲存體不是您要的目的地，可以參閱如何[使用事件中樞在忙碌路徑中串流 Azure 診斷資料](../event-hubs/event-hubs-streaming-azure-diags-data.md)的文章。 當事件位於事件中樞時，系統可以加以讀取並傳送至您選擇的位置。 另外還有一篇關於整合 [Application Insights 和 Azure 診斷資訊](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)的文章

使用 Azure 診斷的其中一個缺點是，系統使用 Resource Manager 範本完成組態，因此只會發生在虛擬機器擴展集層級。 VMSS 會對應至 Service Fabric 中的節點類型。 這表示您必須設定可能在該節點類型上執行所有應用程式和服務的每個節點類型。 取決於已設定應用程式和服務的數量，可能會有大量的 EventSources。 此外，任何應用程式組態變更時，也需要完成 Resource Manager 部署。 理想情況下，監視組態會和服務組態一起傳送。

Azure 診斷僅適用於部署於 Azure 的 Service Fabric 叢集，但適用於 Windows 和 Linux 叢集。

### <a name="eventflow"></a>EventFlow

[EventFlow 由 Visual Studio 小組發行](https://github.com/Azure/diagnostics-eventflow)，並提供將事件從節點路由至一個或多個監視目的地的機制。 由於其作為 NuGet 套件包含在您的服務專案中，EventFlow 的程式碼和組態會和服務一起傳送，消除 Azure 診斷所述的每個節點組態問題。 EventFlow 在您的服務處理序中執行，並直接連線到已設定的輸出。 這種直接連線的方式讓 EventFlow 適用於 Azure、容器或內部部署的服務部署。 在相同節點上執行許多複本時請務必小心，因為每個 EventFlow 管線都會進行外部連線。 如果您主控許多處理序，結果會有大量的輸出連線！ 這對 Service Fabric 應用程式來說不是問題，因為所有 ServiceType 複本都在同一處理序中執行，限制了輸出連線的數量。 EventFlow 也提供篩選事件，因此只會傳送符合指定篩選條件的事件。 如需在 Service Fabric 使用 EventFlow 的詳細資訊，請參閱[直接從 Azure Service Fabric 服務處理序收集記錄](service-fabric-diagnostic-collect-logs-without-an-agent.md)

> [!NOTE]
> Service Fabric 的未來版本中將會包含可用的 EventSource 主機應用程式，容許接聽以 ETW 為基礎的輸入、節點層級集合計量和循環記錄檔支援。

使用 EventFlow 十分簡單
1. 將 Nuget 套件新增至您的服務專案
2. 在服務的**主要**函式中建立 EventFlow 管線，並設定輸出。 在此案例中，我們會使用 SeriLog 當做輸出
    ```csharp

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

                    // The ServiceManifest.XML file defines one or more service type names.
                    // Registering a service maps a service type name to a .NET type.
                    // When Service Fabric creates an instance of this service type,
                    // an instance of the class is created in this host process.

                    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);
                    
                    // Prevents this host process from terminating so services keep running.
                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }

    ```
3. 在服務中名為 *eventFlowConfig.json* 的 PackageRoot | Config 組態資料夾中建立檔案。 檔案中的組態看起來如下所示
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
組態中有兩個已定義的輸入，分別是由 Service Fabric 建立的兩個 EventSource 型來源，以及服務的 EventSource。 請注意，使用 ETW 的系統層級和健全狀況事件並不適用於 EventFlow。 這是因為高層級的權限才可接聽 ETW 來源，而且永遠不應該以任何高權限執行服務。 另一個輸入是 SeriLog，其組態發生於**主要**方法中。  另外還有一些已套用的篩選，其中第一個篩選告知 EventFlow 卸除所有詳細資訊事件層級的事件。 我們稍後會再說明其他篩選定義。 另外還有兩個已設定的輸出，一個是標準輸出，會於 Visual Studio 中寫入輸出視窗。 另一個輸出是 ApplicationInsights，請務必新增您的檢測金鑰。

4. 最後一個步驟是檢測程式碼。 在此範例中，我們會示範用幾個不同的方法檢測 RunAsync。 在下列程式碼中，我們仍然會使用 SeriLog ，而部分語法特定只能用於 SeriLog。 請注意您所選擇記錄解決方案的特定功能。 會產生的三個事件，包含一個偵錯層級事件和兩個資訊事件，第二個用作追蹤要求持續時間。 根據上方的 EventFlow 組態，偵錯層級事件不應該傳遞至輸出。

    ```csharp
        Stopwatch sw = Stopwatch.StartNew();

        while (true)
        {
            cancellationToken.ThrowIfCancellationRequested();

            sw.Restart();

            // Delay a random interval to provide a more interesting request duration.
            await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

            ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
            _logger.LogDebug("Debug level event from Microsoft.Logging");
            _logger.LogInformation("Informational level event from Microsoft.Logging");
            _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
        }
    ```

若要在 Application Insights 中檢視事件，開啟 Azure 入口網站並瀏覽至您的 Application Insights 資源。 然後按一下左上角的 [搜尋] 便可看到事件。

![Application Insights 搜尋事件檢視](./media/service-fabric-diagnostics-overview/ai-search-events.png)

追蹤資訊位於圖片底部。 您可以看到我們只有兩個事件，而 EventFlow 已中斷偵錯層級事件。 那麼追蹤資訊上方的要求項目是什麼？ 那是第三個「_logger」檢測行，顯示事件在 Application Insights 中轉譯成要求的計量。 讓我們回到篩選定義，其類型是 'metadata'。 這是在宣告一個事件具有 'RequestName' 屬性和 'MyRequest' 值，而另一個屬性 'Duration' 則包含要求的持續時間，以毫秒為單位。 這是您在 Application Insights 的要求事件中可以看到的資訊。 相同的方法適用於任何支援的 EventFlow 輸入，包括 EventSource。

如果叢集是獨立叢集，並由於原則而無法連線至以雲端為基礎的解決方案，則 EventFlow 支援彈性搜尋做為輸出，但可以寫入其他輸出，並鼓勵提取要求。 ASP.NET Core 記錄的一些第三方廠商提供者，也有支援內部部署安裝的解決方案。

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric 健全狀況和負載報告

Service Fabric 有自己的健全狀況模型，在幾篇文章中有詳細說明
- [Service Fabric 健全狀況監視簡介](service-fabric-health-introduction.md)
- [回報和檢查服務健全狀況](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [新增自訂 Service Fabric 健康狀態報告](service-fabric-report-health.md)
- [檢視 Service Fabric 健康狀態報告](service-fabric-view-entities-aggregated-health.md)

健全狀況監視對服務運作的各個層面都很關鍵。 最重要的是，Service Fabric 會執行具名的應用程式升級，服務的每個升級網域升級後便會開放給客戶，而在移到下一個升級網域前必須通過健全狀況檢查。 如果無法達到良好的健全狀況，部署就會復原，讓應用程式保持已知的良好狀態。 在服務能夠復原前，有些客戶會受影響，但大部分客戶都不會感覺到有問題。 此外，系統會快速地提供解決方案，無需等待實際操作就能解決問題。 程式碼納入越多健全狀況檢查，服務就更能應對部署問題。

服務健全狀況的另一個層面是來自服務的報告計量。 計量對 Service Fabric 很重要，因為計量是用來平衡資源使用量，並可以當做系統健全狀況的指標。 假設您的應用程式包含許多服務，而每個執行個體會報告每秒要求 (RPS) 計量。 如果其中一項服務比其他服務使用更多資源，Service Fabric 會在叢集間移動服務執行個體，嘗試維護平衡的資源使用量。 [在 Service Fabric 中使用計量管理資源耗用量和負載](service-fabric-cluster-resource-manager-metrics.md)文中提供其運作方式的詳細說明。

計量也讓您深入了解服務的表現如何，而一段時間後，可以用來檢查服務是否在預期的參數內運作。 例如根據趨勢，如果星期一早上 9 點的平均 RPS 是 1000，您可以將健康狀態報告設定為當 RPS 低於 500 或高於 1500 時發出警示。 可能一切都如常運作，但您也可以檢查一下，確保客戶擁有絕佳的體驗。 您的服務可以定義一組以健全狀況為目標的可報告的計量，而在不影響叢集資源平衡的情況下，將計量加權設為零。 我們建議您在開始時將所有計量的加權設為零，並且在了解加權會如何影響叢集的資源平衡前，都不要增加該值。

> [!TIP]
> 請注意不要設定太多加權計量，因為您可能不明白為何服務執行個體會被到處移動，少數計量對首次使用者來說，已經十分足夠了！

任何能夠指出應用程式健全狀況和效能的項目，都是計量和健全狀況報告的候選對象。 CPU 效能計數器可以告訴您節點的使用情況，但不能告知您某個特定服務是否健康，因為可能有多項服務在單一節點上執行。 相反地，像是 RPS、已處理項目或要求延遲等計量，都可以表示特定服務的健全狀況。

若要報告健全狀況，請新增程式碼，例如
```csharp
        if (!result.HasValue)
        {
            HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
            this.Partition.ReportInstanceHealth(healthInformation);
        }
```

若要報告計量，請在服務中新增以下程式碼
```csharp
        this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

## <a name="watchdogs"></a>監視程式

監視程式是個別的服務，可以觀察服務之間的健全狀況和負載，並報告健全狀況態模型階層中任何項目的狀況。 如此可避免在單一服務檢視中無法偵測到的錯誤。 監視程式可用來裝載能夠針對已知狀況執行修復動作的程式碼，無需人員操作。

## <a name="visualization-analysis-and-alerting"></a>視覺效果、分析和警示

監視的最後一個部分是視覺化事件資料流、報告服務效能以及在偵測到問題時發出警示。 這方面的監視有很多解決方案可以使用，Application Insights 和 OMS 可以用來根據事件資料流發出警示。 PowerBI 或第三方廠商解決方案，例如 [Kibana](https://www.elastic.co/products/kibana) 或 [Splunk](https://www.splunk.com/) 可將資料視覺化。

## <a name="next-steps"></a>後續步驟

* [如何利用 Azure 診斷收集記錄檔](service-fabric-diagnostics-how-to-setup-wad.md)
* [直接從 Azure Service Fabric 服務處理序收集記錄](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [在 Service Fabric 中使用計量管理資源耗用量和負載](service-fabric-cluster-resource-manager-metrics.md)




<!--HONumber=Feb17_HO3-->


