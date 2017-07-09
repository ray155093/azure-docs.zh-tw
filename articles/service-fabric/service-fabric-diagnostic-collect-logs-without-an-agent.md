---
title: "直接從 Azure Service Fabric 服務處理程序收集記錄檔 | Microsoft Azure"
description: "說明 Service Fabric 應用程式可以將記錄檔直接傳送到中央位置 (例如 Azure Application Insights 或 Elasticsearch)，而不需倚賴「Azure 診斷」代理程式。"
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz
redirect_url: /azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 08f7b57985382f2abbb90ba1e13a30f40b38917f
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>直接從 Azure Service Fabric 服務處理程序收集記錄檔
## <a name="in-process-log-collection"></a>同處理序記錄檔收集
如果記錄檔來源與目的地的集合都很小、不常變更，並且來源與其目的地之間有直接的對應，則使用 [Azure 診斷擴充功能](service-fabric-diagnostics-how-to-setup-wad.md)來收集應用程式記錄檔，對 **Azure Service Fabric** 服務來說是一個好選項。 如果不是上述情況，替代方式是讓服務將其記錄檔直接傳送到一個中央位置。 此處理程序稱為「同處理序記錄檔收集」，其中提供數個潛在的優點：

* *輕鬆設定及部署*

    * 診斷資料收集組態只是服務組態的一部分。 將其與應用程式的其餘部分始終保持「同步」很簡單。
    * 輕鬆就可達成個別應用程式或個別服務的組態。
        * 代理程式型記錄檔收集通常需要個別部署和設定診斷代理程式，這是額外的系統管理工作，也是可能的錯誤來源。 通常，每一虛擬機器 (節點) 只允許一個代理程式執行個體，而在該節點上執行的所有應用程式和服務會共用代理程式組態。 

* *彈性*
   
    * 只要用戶端程式庫支援目標資料儲存系統，應用程式可以將資料傳送至任何需要的地方。 您可以視需要新增新的目的地。
    * 可以實作複雜的擷取、篩選和資料彙總規則。
    * 代理程式型記錄檔收集通常會受限於代理程式所支援的資料接收器。 有些代理程式是可擴充的。

* *存取內部應用程式資料與內容*
   
    * 應用程式/服務處理序內執行的診斷子系統可以輕鬆地隨著內容資訊而擴大追蹤。
    * 使用代理程式型記錄檔收集時，必須透過某種處理序間的通訊機制 (例如 Windows 的「事件追蹤」) 將資料傳送給代理程式。 此機制可能會造成額外的限制。

您可以結合這兩種收集方法來充分運用其優點。 事實上，它可能是許多應用程式的最佳解決方案。 代理程式型收集是一個自然解決方案，適用於收集與整個叢集和個別叢集節點相關的記錄檔。 與同處理序記錄檔收集相比，它是可診斷服務啟動問題和當機的較可靠方法。 此外，在有許多服務在 Service Fabric 叢集內執行的情況下，如果每個服務都執行自己的同處理序記錄檔收集，將會導致從叢集傳出無數的連線。 大量的傳出連線會同時為網路子系統和記錄檔目的地帶來負擔。 [**Azure 診斷**](../cloud-services/cloud-services-dotnet-diagnostics.md)之類的代理程式可以從多個服務收集資料，然後透過幾條連線傳送所有資料，藉此改善輸送量。 

在本文中，我們將說明如何使用 [**EventFlow 開放原始碼程式庫**](https://github.com/Azure/diagnostics-eventflow)來設定同處理序記錄檔收集。 其他程式庫或許可以用於相同的目的，但 EventFlow 的優點在於它是專門針對同處理序記錄檔收集而設計，並且支援 Service Fabric 服務。 我們將使用 [**Azure Application Insights**](https://azure.microsoft.com/services/application-insights/) 作為記錄檔目的地。 其他像是[**事件中樞**](https://azure.microsoft.com/services/event-hubs/)或 [**Elasticsearch**](https://www.elastic.co/products/elasticsearch) 也都是支援的目的地。 所要做的就是安裝適當的 NuGet 套件，並在 EventFlow 組態檔中設定目的地。 如需有關 Application Insights 以外之記錄檔目的地的詳細資訊，請參閱 [EventFlow 文件](https://github.com/Azure/diagnostics-eventflow)。

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>將 EventFlow 程式庫新增到 Service Fabric 服務專案
EventFlow 二進位檔是以一組 NuGet 套件的形式提供。 若要將 EventFlow 新增到 Service Fabric 服務專案，請在 [方案總管] 中該專案上按一下滑鼠右鍵，然後選擇 [管理 NuGet 套件]。 切換到 [瀏覽] 索引標籤，然後搜尋 "`Diagnostics.EventFlow`"：

![Visual Studio NuGet 套件管理員 UI 中的 EventFlow NuGet 套件][1]

視應用程式記錄檔的來源和目的地而定，裝載 EventFlow 的服務應該包含適當的套件。 請新增下列套件： 

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` 
    * (用來從服務的 EventSource 類別以及從標準 EventSources (例如 *Microsoft-ServiceFabric-Services* 和 *Microsoft-ServiceFabric-Actors*) 擷取資料)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` 
    * (我們將把記錄檔傳送給 Azure Application Insights 資源)  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * (允許從 Service Fabric 服務組態將 EventFlow 管線初始化，並以 Service Fabric 健康情況報告的形式傳送診斷資料來回報任何問題)

> [!NOTE]
> `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` 套件會要求服務專案的目標必須是 .NET Framework 4.6 或更新版本。 安裝此套件之前，請先確定您已在專案屬性中設定適當的目標架構。 

安裝好所有套件之後，下一個步驟就是設定及啟用服務中的 EventFlow。

## <a name="configuring-and-enabling-log-collection"></a>設定及啟用記錄檔收集
EventFlow 管線 (負責傳送記錄檔) 是從儲存在組態檔中的規格建立的。 `Microsoft.Diagnostics.EventFlow.ServiceFabric` 套件會在 `PackageRoot\Config` 方案資料夾底下安裝一個起始的 EventFlow 組態檔。 檔案名稱是 `eventFlowConfig.json`。 此組態檔必須經過修改，才能從預設服務 `EventSource` 類別擷取資料，然後將資料傳送給 Application Insights 服務。

> [!NOTE]
> 我們假設您已熟悉 **Azure Application Insights** 服務，並且具有要用來監視 Service Fabric 服務的 Application Insights 資源。 如需詳細資訊，請參閱[建立 Application Insights 資源](../application-insights/app-insights-create-new-resource.md)。

在編輯器中開啟 `eventFlowConfig.json` 檔案，並依照以下所示變更其內容。 請務必根據註解取代 ServiceEventSource 名稱和 Application Insights 檢測金鑰。 

```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

> [!NOTE]
> 服務的 ServiceEventSource 名稱是套用至 ServiceEventSource 類別之 `EventSourceAttribute` 的 Name 屬性值。 全部都在 `ServiceEventSource.cs` 檔案中指定，此檔案是服務程式碼的一部分。 例如，在下列程式碼片段中，ServiceEventSource 的名稱是 *MyCompany-Application1-Stateless1*：
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

請注意，`eventFlowConfig.json` 檔案是服務組態封裝的一部分。 對此檔案所做的變更可以包含在完整或僅限組態的服務升級中，並受到 Service Fabric 升級健康情況檢查和自動復原 (如果發生升級失敗) 控管。 如需詳細資訊，請參閱 [Service Fabric 應用程式升級](service-fabric-application-upgrade.md)。

最後一個步驟是在服務的啟動程式碼 (位於 `Program.cs` 檔案中) 中將 EventFlow 管線具現化。 在下列範例中，EventFlow 相關的新增皆有開頭為 `****` 的註解標示：

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    
                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

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
}
```

當作 `ServiceFabricDiagnosticsPipelineFactory` 之 `CreatePipeline` 方法的參數來傳遞的名稱是「健康情況實體」的名稱，此實體代表 EventFlow 記錄檔收集管線。 當 EventFlow 發生錯誤並透過 Service Fabric 健康情況子系統回報錯誤時，就會使用此名稱。

## <a name="verification"></a>驗證
啟動您的服務並觀察 Visual Studio 中的 [偵錯] 輸出視窗。 啟動服務之後，您應該會開始看到服務傳送「Application Insights 遙測」記錄的證據。 開啟網頁瀏覽器，然後瀏覽至您的 Application Insights 資源。 開啟 [搜尋] 索引標籤 (在預設的 [概觀] 刀鋒視窗頂端)。 在短暫延遲之後，您應該會開始在 Application Insights 入口網站中看到您的追蹤：

![顯示來自 Service Fabric 應用程式之記錄檔的 Application Insights 入口網站][2]

## <a name="next-steps"></a>後續步驟
* [深入了解診斷和監視 Service Fabric 服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [EventFlow 文件](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png

