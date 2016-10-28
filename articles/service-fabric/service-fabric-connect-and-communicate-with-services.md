<properties
   pageTitle="連接至 Azure Service Fabric 中的服務並與其進行通訊 | Microsoft Azure"
   description="了解如何解析、連接至 Service Fabric 應用程式中的服務並與其進行通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="msfussell"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="vturecek"/>

# 連接至 Service Fabric 中的服務並與其進行通訊
在 Service Fabric 中，服務會在 Service Fabric 叢集中的某處執行，通常是分散到多個 VM。它可以由服務擁有者或是 Service Fabric 自動從某個位置移到其他位置。服務無法以靜態方式繫結至特定電腦或位址。
 
Service Fabric 應用程式通常是由許多不同的服務組成，每一個服務用來執行特定的工作。這些服務可能會彼此進行通訊以形成完整的函式，例如轉譯 Web 應用程式的不同部分。有些用戶端應用程式會連接到服務並與其通訊。本文件討論如何設定您在 Service Fabric 中的服務之間的通訊。

## 引進您自己的通訊協定
Service Fabric 可以幫助管理您的服務的生命週期，但是它不會決定您的服務要執行的動作。這包含通訊：當您的服務是由 Service Fabric 開啟時，就是您的服務設定連入要求端點的機會，使用您想要的任何通訊協定或通訊堆疊。您的服務會接聽一般 **IP:port** 位址，使用任何定址配置，例如 URI。多個服務執行個體或複本可能共用主機處理序，在此情況下，它們必須使用不同的連接埠，或使用連接埠共用機制，如 Windows 的 http.sys 核心驅動程式。在任一情況下，主機處理序中的每個服務執行個體或複本必須可以唯一定址。

![服務端點][1]

## 服務探索和解析
在分散式系統中，服務可能會隨著時間從一部電腦移動到另一部電腦。發生這種情形的原因有很多，包括資源平衡、升級、容錯移轉或向外延展。這表示服務端點位址會在服務移至具有不同 IP 位址的節點時變更，而且如果服務使用動態選取的連接埠，可能會在不同的連接埠上開啟。

![服務的分佈][7]

Service Fabric 提供稱為「命名服務」的探索和解析服務。「命名服務」會維護資料表，該資料表將已命名服務執行個體對應至它們接聽的端點位址。Service Fabric 中的所有已命名服務執行個體，皆如 URI 擁有唯一的名稱，例如，`"fabric:/MyApplication/MyService"`。服務的名稱不會隨著服務的存留期變更，當服務移動時，只有端點位址可以變更。這類似於具有常數 URL 的網站，但 IP 位址可能會變更。並且類似於 Web 上的 DNS，可解析連接至 IP 位址的網站 URL，Service Fabric 具有註冊機構，會將服務名稱對應至其端點位址。

![服務端點][2]

解析和連接到服務牽涉到在迴圈中執行的下列步驟︰

* **解析**︰取得端點，該端點是由服務從「命名服務」所發佈。

* **連接**︰透過在該端點上所使用的任何通訊協定連接至服務。

* **重試**︰連接嘗試可能會因為各種原因而失敗，例如，如果服務自從上次端點位址解析之後已經移動。在此情況下，則必須重試先前的解析和連接步驟，且此循環會重複執行直到連接成功為止。

## 從外部用戶端連接

叢集內彼此連接的服務通常可以直接存取其他服務的端點，因為叢集中的節點通常是在相同的本機網路上。但是，在相同的環境中，叢集可能會位於負載平衡器後方，該負載平衡器會透過有限制的一組連接埠路由傳送外部輸入流量。在這些情況下，服務仍然可以使用「命名服務」，彼此進行通訊及解析位址，但是必須採取額外的步驟，讓外部用戶端連接至服務。

## Azure 中的 Service Fabric

Azure 中的 Service Fabric 叢集位於 Azure 負載平衡器後方。到叢集的所有外部流量必須經過負載平衡器。負載平衡器會自動將指定連接埠上的輸入流量轉送至具有相同的開啟連接埠的隨機「節點」。Azure 負載平衡器只會知道「節點」上開啟的連接埠，它不知道由個別「服務」開啟的連接埠。

![Azure 負載平衡器和 Service Fabric 拓撲][3]

例如，若要在連接埠 **80** 上接受外部流量，必須設定下列項目︰

1. 寫入在連接埠 80 上接聽的服務。在服務的 ServiceManifest.xml 中設定連接埠 80，並且在服務中開啟接聽程式，例如自我裝載的 Web 伺服器。
 
    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...
            
            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint = 
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

                return Task.FromResult(this.publishUri);
            }
            
            ...
        }
        
        class WebService : StatelessService
        {
            ...
            
            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] {new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }
            
            ...
        }
    ```
  
2. 在 Azure 中建立 Service Fabric 叢集，並指定連接埠 **80** 做為將裝載服務的節點類型的自訂端點連接埠。如果您有一個以上的節點類型，您可以在服務上設定「放置條件約束」，以確保它只會在具有已開啟的自訂端點連接埠的節點類型上執行。

    ![開啟節點類型上的連接埠][4]

3. 一旦建立叢集，在叢集的資源群組中設定 Azure 負載平衡器，以轉送連接埠 80 上的流量。透過 Azure 入口網站建立叢集時，會針對每個已設定的自訂端點連接埠設定這個項目。

    ![轉送 Azure 負載平衡器的流量][5]

4. Azure 負載平衡器使用探查，來決定是否要將流量傳送到特定節點。探查會定期檢查每個節點上的端點，以判斷節點是否有回應。如果探查在設定的次數之後無法接收到回應，負載平衡器會停止將流量傳送到該節點。透過 Azure 入口網站建立叢集時，會針對每個已設定的自訂端點連接埠自動設定探查。

    ![轉送 Azure 負載平衡器的流量][8]

請務必記住，Azure 負載平衡器和探查只知道「節點」，不知道在節點上執行的「服務」。Azure 負載平衡器一律會將流量傳送到回應探查的節點，因此必須小心以確保可以在能夠回應探查的節點上使用服務。

## 內建通訊 API 選項
Reliable Services 架構隨附數個預先建置的通訊選項。最適合您選項的決定取決於如何選擇程式設計模型、通訊架構以及用來撰寫您服務的程式語言。

* **沒有特定通訊協定**：如果您沒有特定的通訊架構選擇，但您想要快速啟動並執行，則適合您的理想選項為[遠端服務](service-fabric-reliable-services-communication-remoting.md)，允許 Reliable Services 和 Reliable Actors 的強型別遠端程序呼叫。若要開始使用服務通訊，這是最簡單且快速的方式。遠端服務會處理服務位址、連接、重試和錯誤處理的解析。請注意，遠端服務僅適用於 C# 應用程式。

* **HTTP**︰對於無從驗證語言的通訊，HTTP 提供業界標準的選擇，具有工具與許多不同語言的 HTTP 伺服器，都受到 Service Fabric 的支援。服務可以使用任何可用的 HTTP 堆疊，包括 [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md)。以 C# 撰寫的用戶端可以針對服務解析、HTTP 連接和重試迴圈利用 [`ICommunicationClient` 和 `ServicePartitionClient` 類別](service-fabric-reliable-services-communication.md)。

* **WCF**：若您有現有的程式碼且使用 WCF 作為通訊架構，則您可以針對伺服器端使用 `WcfCommunicationListener` 類別，並針對用戶端使用 `WcfCommunicationClient` 和 `ServicePartitionClient` 類別。如需詳細資訊，請參閱本文中[通訊堆疊的 WCF 式實作](service-fabric-reliable-services-communication-wcf.md)。

## 使用自訂通訊協定以及其他通訊架構
服務可以使用任何通訊協定或架構進行通訊，無論是透過 TCP 通訊端的自訂二進位通訊協定，或透過 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)或 [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub/)的串流事件。Service Fabric 提供通訊 API，您可以將通訊堆疊插入其中，同時讓您免於探索和連接的所有工作。如需詳細資訊，請參閱本文中 [Reliable Service 通訊模型](service-fabric-reliable-services-communication.md)。

## 後續步驟

您可以在 [Reliable Services 通訊模型](service-fabric-reliable-services-communication.md) 中深入了解概念和可用的 API，然後快速地開始使用[遠端服務](service-fabric-reliable-services-communication-remoting.md)或深入了解如何使用 [Web API 與 OWIN 自我裝載](service-fabric-reliable-services-communication-webapi.md)撰寫通訊接聽程式。

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png

<!---HONumber=AcomDC_0713_2016-->