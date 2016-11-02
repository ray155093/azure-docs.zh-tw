<properties
   pageTitle="Service Fabric 和容器的概觀 | Microsoft Azure"
   description="Service Fabric 及使用容器來部署微服務應用程式的概觀。 本文提供如何使用容器及 Service Fabric 所提供之功能的概觀"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>


# <a name="preview:-service-fabric-and-containers"></a>預覽︰Service Fabric 和容器 

>[AZURE.NOTE] 這項功能在 Linux 上處於預覽階段，在 Windows Server 上目前無法使用。 在 Windows Server 2016 GA 之後的下一版 Service Fabric 上，這將於 Windows Server 中進入預覽階段，之後在後續版本中就會開始支援。

## <a name="introduction"></a>簡介
Service Fabric 是跨電腦叢集的服務 [協調者](service-fabric-cluster-resource-manager-introduction.md) 。 開發服務有許多的方式，從 [Service Fabric 程式設計模型](service-fabric-choose-framework.md)，到部署[來賓可執行檔](service-fabric-deploy-existing-app.md)。 根據預設，Service Fabric 會以處理序形式部署和啟動這些服務。 處理序能以最快速度和最密集的程度使用叢集的資源。 Service Fabric 也可以將服務部署在容器映像中，重要的是，您可以在相同應用程式中混合使用處理序中的服務和容器中的服務。 視您的情況而定，兩者都可兼得。

## <a name="what-are-containers?"></a>什麼是容器？
容器是封裝成可個別部署的元件，在相同核心上以隔離的執行個體執行，並利用作業系統層級虛擬化。 這表示，每個應用程式、其執行階段、相依性及系統程式庫在容器內執行時，在其各自於作業系統建構中的獨立範圍內，都具有完整的專屬存取權。 再加上可攜性，這種程度的安全性和資源隔離是容器與 Service Fabric 一起使用的主要優點 (否則是在處理序中執行服務)。 

容器是從應用程式中將基礎作業系統虛擬化的一種虛擬化技術。 它們提供不變的環境讓應用程式以不同的隔離程度執行。 容器直接執行於核心之上，而且在檔案系統和其他資源中有其各自隔離的範圍。 相較於 VM，容器的優點如下︰

1.  **小型**︰容器使用單一儲存空間，每一層的差異較小，因此更有效率。

2.  **快速啟動**︰容器不需要啟動作業系統，因此，比 VM 更快啟動和運作，通常只在幾秒內。

3.  **可攜性**︰容器化應用程式映像可以移植到雲端或內部部署中，而在 VM 內或直接在實體機器上執行。

4.  **提供資源管理** ，可限制容器在其主機上可取用的實體資源。

## <a name="container-types"></a>容器類型
Service Fabric 支援下列類型的容器︰

### <a name="docker-containers-on-linux"></a>Linux 上的 Docker 容器
Docker 提供更高層級的 API，以建立和管理 Linux 核心容器之上的容器，並提供 Docker 中樞作為中央儲存機制來儲存和擷取容器映像。 

### <a name="windows-server-containers"></a>Windows Server 容器
Windows Server 2016 提供兩種不同的容器，所提供的隔離程度有所不同。 Windows Server 容器類似於 Docker 容器，都有命名空間和檔案系統隔離，但與它們執行所在的主機共用核心。 在 Linux 上，傳統上由控制群組和命名空間提供這種隔離，而 Windows Server 容器具有類似的行為。 

Windows Hyper-V 容器提供更高程度的隔離和安全性，因為每個容器彼此之間或與主機之間並不共用作業系統核心。 由於有如此高程度的安全性隔離，Hyper-V 容器在惡意多租用戶的情況下特別重要。

下圖顯示作業系統中可用的各種不同的虛擬化及隔離等級。
![Service Fabric 平台][Image1]

## <a name="scenarios-for-using-containers"></a>容器使用案例
容器使用時機的一些常見例子包括

1. **IIS 隨即轉移**。 如果您有一些想要繼續使用的現有 ASP.NET MVC 應用程式，不必移轉到 ASP.NET 核心，將它們放到容器中即可。 這些 ASP.NET MVC 應用程式都依賴 IIS。 您可以從預先建立的 IIS 映像將這些應用程式封裝成容器映像，然後與 Service Fabric 一起部署。 請參閱 [Windows Server 上的容器映像](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) ，以了解如何建立 IIS 映像。


2. **混合容器和 Service Fabric 微服務**。 對應用程式的一部分使用現有的容器映像。 例如，對於應用程式的 Web 前端系統，您可以使用 [NGINX 容器](https://hub.docker.com/_/nginx/) ，而對於更密集的後端運算，則可以使用以 Reliable Services 建置的具狀態服務。 此案例的範例包括遊戲應用程式


3. **減少「吵雜芳鄰」服務的影響**。 容器的資源控管能力可讓您限制服務在主機上所使用的資源。 如果有些服務可能會耗用大量資源，因而影響其他服務的效能 (例如，像作業一樣長時間執行的查詢)，請考慮將這些服務放到可控管資源的容器中。

## <a name="service-fabric-support-for-containers"></a>Service Fabric 的容器支援
目前，Service Fabric 支援將 Docker 容器部署在 Linux，也支援將 Windows Server 容器部署在 Windows Server 2016。 未來版本將增加支援 Hyper-V 容器。 

在 Service Fabric [應用程式模型](service-fabric-application-model.md)中，容器代表多個服務複本所在的應用程式主機。 支援下列容器案例︰

1.  **來賓容器**︰和[來賓可執行檔案例](service-fabric-deploy-existing-app.md)相同，可讓您部署容器中的現有應用程式。 例如 nodejs、javascript 或任何程式碼 (ExE)。


2.  **容器內的無狀態服務**︰這些是使用 Reliable Services 和 Reliable Actors 程式設計模型的無狀態服務。 目前只有 Linux 上支援此案例。 未來版本中將增加支援 Windows 容器中的無狀態服務。

 
3.  **容器內的具狀態服務**︰這些是 Linux 上使用 Reliable Actors 程式設計模型的具狀態服務。 Linux 上目前不支援 Reliable Services。  未來版本中將增加支援 Windows 容器中的具狀態服務。

Service Fabric 有數個容器功能可協助您建置由容器化微服務組成的應用程式。 這些稱為容器化服務。 功能包括：

- 容器映像部署和啟用
- 資源管理
- 儲存機制驗證
- 容器連接埠至主機連接埠的對應
- 容器至容器的探索及通訊
- 能夠設定環境變數

## <a name="next-steps"></a>後續步驟
在本文中，您已了解容器、Service Fabric 是容器協調者，以及 Service Fabric 提供來支援容器的功能。 接下來，我們將示範每一項功能來展示其用法。 

[將容器部署至 Service Fabric](service-fabric-deploy-container.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png




<!--HONumber=Oct16_HO2-->


