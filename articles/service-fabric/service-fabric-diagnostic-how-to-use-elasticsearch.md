---
title: 使用 Elasticsearch 做為 Service Fabric 應用程式追蹤存放區 | Microsoft Docs
description: 描述 Service Fabric 應用程式如何使用 Elasticsearch 和 Kibana 來儲存、檢索和搜尋應用程式追蹤 (記錄檔)
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: adegeo
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2016
ms.author: karolz@microsoft.com

---
# 把 Elasticsearch 當做 Service Fabric 應用程式追蹤存放區來使用
## 簡介
本文將說明 [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric/) 應用程式如何使用 **Elasticsearch** 和 **Kibana** 來儲存、檢索和搜尋應用程式追蹤。[Elasticsarch](https://www.elastic.co/guide/index.html) 是開放原始碼、分散式和可調整的即時搜尋和分析引擎，很適合執行這項工作。它可以安裝在 Microsoft Azure 中執行的 Windows 和 Linux 虛擬機器上。Elasticsearch 可有效率地處理使用「Windows 事件追蹤 (ETW)」之類的技術所產生的「結構化」追蹤。

Service Fabric 執行階段會使用 ETW 來取得診斷資訊 (追蹤)。它也是 Service Fabric 應用程式取得其診斷資訊的建議方法。使用相同的機制，可讓執行階段提供和應用程式提供的追蹤之間相互關聯，讓您更輕鬆地執行疑難排解作業。Visual Studio 中的 Service Fabric 專案範本包含記錄 API (根據.NET **EventSource** 類別)，依預設會發出 ETW 追蹤。如需使用 ETW 的 Service Fabric 應用程式追蹤的一般概觀，請參閱[監視和診斷本機開發設定中的服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。

需要在 Service Fabric 叢集節點上即時 (當應用程式正在執行時) 擷取追蹤並傳送至 Elasticsearch 端點，Elasticsearch 中才會顯示追蹤。追蹤擷取有兩個主要選項：

* **同處理序追蹤擷取** 應用程式 (更準確來說是服務處理序) 負責將診斷資料送出到追蹤存放區 (Elasticsearch)。
* **跨處理序追蹤擷取** 另外的代理程式擷取來自一或多個服務處理序的追蹤，並傳送至追蹤存放區。

以下說明如何在 Azure 上設定 Elasticsearch、討論這兩種擷取選項的優缺點，並說明如何設定 Service Fabric 服務將資料傳送至 Elasticsearch。

## 在 Azure 上設定 Elasticsearch
若要在 Azure 上設定 Elasticsearch 服務，最直接的方法是透過 [**Azure 資源管理員範本**](../resource-group-overview.md)。Azure 快速入門範本儲存機制提供完整的 [Elasticsearch 快速入門 Azure 資源管理員範本](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch)。這個範本會針對縮放單位使用不同的儲存體帳戶 (節點群組)。它也可以佈建具有不同組態和連接各種數量的資料磁碟的個別用戶端與伺服器節點。

此處我們會使用 [Azure diagnostic tools repository (Azure 診斷工具儲存機制)](https://github.com/Azure/azure-diagnostics-tools) 中的另一個範本，稱為 **ES-MultiNode**。這個範本比較容易使用，其會建立由 HTTP 基本驗證所保護的 Elasticsearch 叢集。繼續之前，請從 GitHub 將儲存機制下載到您的電腦 (透過複製儲存機制或下載 ZIP 檔案)。ES-MultiNode 範本位於具有相同名稱的資料夾中。

### 準備電腦以執行 Elasticsearch 安裝指令碼
若要使用 ES-MultiNode 範本，最簡單的方式是透過提供的 Azure PowerShell 指令碼，稱為 `CreateElasticSearchCluster`。若要使用這個指令碼，您需要安裝 PowerShell 模組和名為 **openssl** 的工具。需要後者，才能建立可用來從遠端管理 Elasticsearch 叢集的 SSH 金鑰。

`CreateElasticSearchCluster` 指令碼主要是為了從 Windows 電腦輕鬆使用 ES-MultiNode 範本所設計。可以在非 Windows 電腦上使用此範本，但這已超出本文的範圍。

1. 如果您尚未安裝它們，請安裝 [**Azure PowerShell 模組**](http://aka.ms/webpi-azps)。出現提示時，請按一下 [執行]，然後按一下 [安裝]。需要 Azure PowerShell 1.3 或更新版本。
2. [**Git for Windows**](http://www.git-scm.com/downloads) 的散發中包含 **openssl** 工具。如果您尚未安裝，請立即安裝 [Git for Windows](http://www.git-scm.com/downloads)。(預設安裝選項是 [確定])。
3. 假設 Git 已安裝，但未包含在系統路徑中，請開啟 Microsoft Azure PowerShell 視窗並執行下列命令：
   
    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```
   
    以您電腦上的 Git 位置取代 `<Git installation folder>`，預設值為 **"C:\\Program Files\\Git"**。請注意第一個路徑開頭的分號字元。
4. 確定您已登入 Azure (透過 [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) Cmdlet)，且已選取應該用來建立 Elasticsearch 叢集的訂用帳戶。您可以使用 `Get-AzureRmContext` 和 `Get-AzureRmSubscription` Cmdlet，確認選取的是正確的訂用帳戶。
5. 如果您尚未將目前目錄切換至 ES-MultiNode 資料夾，請切換。

### 執行 CreateElasticSearchCluster 指令碼
執行指令碼之前，開啟 `azuredeploy-parameters.json` 檔案，確認或提供指令碼參數的值。提供下列參數：

| 參數名稱 | 說明 |
| --- | --- |
| dnsNameForLoadBalancerIP |此名稱用來為 Elasticsearch 叢集建立公開可見的 DNS 名稱 (將 Azure 區域網域附加至提供的名稱)。例如，如果此參數值為 "myBigCluster"，而選擇的 Azure 區域是美國西部，則為叢集產生的 DNS 名稱會是 myBigCluster.westus.cloudapp.azure.com。<br /><br />對於 Elasticsearch 叢集相關聯的許多構件名稱，例如資料節點名稱，此名稱也會成為這些名稱的根。 |
| adminUsername |用於管理 Elasticsearch 叢集的系統管理員帳戶名稱 (對應的 SSH 金鑰會自動產生)。 |
| dataNodeCount |Elastic Search 叢集中的節點數目。目前版本的指令碼不會區分資料與查詢節點。所有節點會同時扮演這兩種角色。預設為 3 個節點。 |
| dataDiskSize |配置給每個資料節點的資料磁碟大小 (以 GB 為單位)。每個節點會收到 4 個資料磁碟，專供 Elasticsearch 服務使用。 |
| region |應該放置 Elastic Search 叢集的 Azure 區域的名稱。 |
| esUserName |此使用者名稱適用於設定為可存取 ES 叢集的使用者 (受限於 HTTP 基本驗證)。密碼不是參數檔案的一部分，而且必須在叫用 `CreateElasticSearchCluster` 指令碼時提供。 |
| vmSizeDataNodes |Elastic Search 叢集節點的 Azure 虛擬機器大小。預設為 Standard\_D2。 |

您現在可以開始執行指令碼。發出以下命令：

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

其中

| 指令碼參數名稱 | 說明 |
| --- | --- |
| `<es-group-name>` |將包含所有 Elasticsearch 叢集資源的 Azure 資源群組名稱。 |
| `<azure-region>` |應該建立 Elasticsearch 叢集的 Azure 區域名稱。 |
| `<es-password>` |Elasticsearch 使用者的密碼。 |

> [!NOTE]
> 如果您從 Test-AzureResourceGroup Cmdlet 收到 NullReferenceException，表示您忘記登入 Azure (`Add-AzureRmAccount`)。
> 
> 

如果執行指令碼發生錯誤，而且您判斷錯誤起因於錯誤的範本參數值，請更正參數檔案，然後以不同資源群組名稱再次執行指令碼。您也可以將 `-RemoveExistingResourceGroup` 參數加入至指令碼引動過程，以重複使用相同的資源群組名稱，並讓指令碼清除舊的資源群組。

### 執行 CreateElasticSearchCluster 指令碼的結果
執行 `CreateElasticSearchCluster` 指令碼之後會建立下列主要構件。針對此範例，我們假設您已使用 "myBigCluster" 作為 `dnsNameForLoadBalancerIP` 參數的值，而且您建立叢集的區域為美國西部。

| 構件 | 名稱、位置及備註 |
| --- | --- |
| 用於遠端系統管理的 SSH 金鑰 |myBigCluster.key 檔 (在執行 CreateElasticSearchCluster 的目錄中)。<br /><br />此金鑰檔案可用於連線至叢集中的管理節點，以及 (透過管理節點) 連線至資料節點。 |
| 管理節點 |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />用於遠端 Elasticsearch 叢集管理的專用 VM，也是唯一允許外部 SSH 連線的 VM。其與所有 Elasticsearch 叢集節點一樣在相同的虛擬網路上執行，但不會執行 Elasticsearch 服務。 |
| 資料節點 |myBigCluster1 … myBigCluster*N* <br /><br />執行 Elasticsearch 和 Kibana 服務的資料節點。您可以透過 SSH 連接至每個節點，但只能透過管理節點。 |
| Elasticsearch 叢集 |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Elasticsearch 叢集的主要端點 (請注意 /es 尾碼)。它是由基本 HTTP 驗證保護 (由 ES-MultiNode 範本的 esUserName/esPassword 參數指定認證)。叢集也已安裝前端外掛程式 (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) 來進行基本叢集管理。 |
| Kibana 服務 |http://myBigCluster.westus.cloudapp.azure.com <br /><br />將 Kibana 服務設為顯示已建立的 Elasticsearch 叢集資料。其受與叢集本身相同的驗證認證保護。 |

## 同處理序與跨處理序追蹤擷取
在簡介中，我們曾提過收集診斷資料的兩種基本方法：同處理序和跨處理序。各有優缺點。

**同處理序追蹤擷取**的優點包括：

1. *輕鬆設定及部署*
   
   * 診斷資料收集組態只是應用程式組態的一部分。將其與應用程式的其餘部分始終保持「同步」很簡單。
   * 輕鬆就可達成個別應用程式或個別服務的組態。
   * 跨處理序追蹤擷取通常需要另外部署和設定診斷代理程式，這是額外的系統管理工作，也是可能的錯誤來源。特定代理程式的技術通常只允許每個虛擬機器 (節點) 有一個執行個體。這表示該節點上執行的所有應用程式和服務會共用診斷組態集合的組態。
2. *彈性*
   
   * 只要用戶端程式庫支援目標資料儲存系統，應用程式可以將資料傳送至任何需要的地方。可以依需要加入新的來源。
   * 可以實作複雜的擷取、篩選和資料彙總規則。
   * 跨處理序追蹤擷取通常受限於代理程式支援的資料來源。有些代理程式是可擴充的。
3. *存取內部應用程式資料與內容*
   
   * 應用程式/服務處理序內執行的診斷子系統可以輕鬆地隨著內容資訊而擴大追蹤。
   * 在跨處理序方法中，必須透過 Windows 事件追蹤之類的處理序間通訊機制，將資料傳送至代理程式。此機制可能會造成額外的限制。

**跨處理序追蹤擷取**的優點包括：

1. *能夠監視應用程式並收集損毀傾印*
   
   * 如果應用程式啟動失敗或損毀，同處理序追蹤擷取可能不會成功。獨立代理程式有更好的機會可擷取重要的疑難排解資訊。<br /><br />
2. *成熟度、穩健性和已證實的效能*
   
   * 平台廠商所開發的代理程式 (例如 Microsoft Azure 診斷代理程式) 都經過嚴格的測試和實戰鍛鍊。
   * 執行同處理序追蹤擷取時，必須確保從應用程式程序傳送診斷資料的活動不會干擾應用程式的主要工作，或引起計時或效能問題。獨立執行的代理程式比較不容易發生這些問題，且亦特別設計為限制其對系統的影響。

您可以結合這兩種方法的優點。事實上，它可能是許多應用程式的最佳解決方案。

此處我們將使用 **Microsoft.Diagnostic.Listeners 程式庫**和同處理序追蹤擷取，將資料從 Service Fabric 應用程式傳送到 Elasticsearch 叢集。

## 使用 Listeners 程式庫將診斷資料傳送至 Elasticsearch
Microsoft.Diagnostic.Listeners 程式庫是 PartyCluster 範例 Service Fabric 應用程式的一部分。使用方式：

1. 從 GitHub 下載 [PartyCluster 範例](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster)。
2. 從 PartyCluster 叢集範例目錄中，將 Microsoft.Diagnostics.Listeners 和 Microsoft.Diagnostics.Listeners.Fabric 專案 (整個資料夾)，複製到應該會將資料傳送到 Elasticsearch 的應用程式的方案資料夾。
3. 開啟目標方案，在方案總管中的方案節點上按一下滑鼠右鍵，然後選擇 [加入現有專案]。將 Microsoft.Diagnostics.Listeners 專案加入至方案。針對 Microsoft.Diagnostics.Listeners.Fabric 專案重複相同的步驟。
4. 新增從服務專案至兩個已加入之專案的專案參考。 (每個應該會將資料傳送至 Elasticsearch 的服務，都應該參考 Microsoft.Diagnostics.EventListeners 和 Microsoft.Diagnostics.EventListeners.Fabric)。
   
    ![Microsoft.Diagnostics.EventListeners 和 Microsoft.Diagnostics.EventListeners.Fabric 程式庫的專案參考][1]

### Service Fabric 公開上市版本和 Microsoft.Diagnostics.Tracing Nuget 封裝
以 Service Fabric 公開上市版本 (2.0.135，2016 年 3 月 31 日發行) 建置的應用程式會以 **.NET Framework 4.5.2** 為目標。這是 Azure 在 GA 版本支援的最新 .NET Framework 版本。可惜，這個版本的架構缺少 Microsoft.Diagnostics.Listeners 程式庫所需的某些 EventListener API。因為 EventSource (Fabric 應用程式中形成記錄 API 基礎的元件) 和 EventListener 緊密結合，每個使用 Microsoft.Diagnostics.Listeners 程式庫的專案都必須使用替代的 EventSource 實作。此實作由 Microsoft 撰寫的 **Microsoft.Diagnostics.Tracing NuGet 封裝**所提供。此封裝完全與架構中包含的舊版 EventSource 相容，除了變更參考的命名空間，應該不需要變更任何程式碼。

若要開始使用 Microsoft.Diagnostics.Tracing 的 EventSource 類別實作，請針對需要將資料傳送至 Elasticsearch 的每個服務專案，執行下列步驟：

1. 以滑鼠右鍵按一下服務專案，然後選擇 [管理 Nuget 封裝]。
2. 切換到 nuget.org 封裝來源 (如果尚未選取)，搜尋「**Microsoft.Diagnostics.Tracing**」。
3. 安裝 `Microsoft.Diagnostics.Tracing.EventSource` 封裝 (及其相依項目)。
4. 開啟服務專案中的 **ServiceEventSource.cs** 或 **ActorEventSource.cs** 檔案，並將檔案頂端的 `using System.Diagnostics.Tracing` 指示詞取代為 `using Microsoft.Diagnostics.Tracing` 指示詞。

當 Microsoft Azure 支援 **.NET Framework 4.6** 時，就不需要這些步驟。

### Elasticsearch 接聽程式具現化和組態
將診斷資料傳送至 Elasticsearch 所需的最後一個步驟，就是建立 `ElasticSearchListener` 的執行個體，並以 Elasticsearch 連線資料來設定它。接聽程式會自動擷取所有透過服務專案中定義的 EventSource 類別所引發的事件。它必須在服務的存留期間運作，所以服務初始化程式碼中是建立它的最佳位置。以下是無狀態服務的初始化程式碼在完成必要變更後的樣子 (以 `****` 開頭的註解指出新增的部分)：

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

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
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Elasticsearch 連接資料應該放在服務組態檔 (**PackageRoot\\Config\\Settings.xml**) 中的個別區段。區段的名稱必須對應至傳給 `FabricConfigurationProvider` 建構函式的值，例如：

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
`serviceUri`、`userName` 和 `password` 的值分別對應到 Elasticsearch 叢集端點位址、Elasticsearch 使用者名稱和密碼。`indexNamePrefix` 是 Elasticsearch 索引的首碼。Microsoft.Diagnostics.Listeners 程式庫會每天建立其資料的新索引。

### 驗證
就這麼簡單！ 現在每當服務執行時，就會開始將追蹤傳送至組態中指定的 ElasticSearch 服務。您可開啟與目標 Elasticsearch 執行個體關聯的 Kibana UI 進行驗證。在我們的範例中，網頁位址為 http://myBigCluster.westus.cloudapp.azure.com/。檢查是否已確實建立為`ElasticSearchListener`執行個體選擇名稱前置詞的索引，並已填入資料。

![顯示 PartyCluster 應用程式事件的 Kibana][2]

## 後續步驟
* [深入了解診斷和監視 Service Fabric 服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_0817_2016-->