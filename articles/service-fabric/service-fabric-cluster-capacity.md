---
title: "規劃 Service Fabric 叢集容量 | Microsoft Docs"
description: "Service Fabric 叢集容量規劃考量。 Nodetypes、持久性和可靠性層級"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e8d6f7c41287f5f785a52ae82bb156008d7e2699
ms.lasthandoff: 04/27/2017


---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric 叢集容量規劃考量
對於任何生產部署而言，容量規劃都是一個很重要的步驟。 以下是一些您在該程序中必須考量的項目。

* 您的叢集一開始所需的節點類型的數目
* 每個節點類型 (大小、主要、網際網路對向、VM 數目等) 的屬性
* 叢集的可靠性和持久性的特性

讓我們簡短地檢閱各個項目。

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>您的叢集一開始所需的節點類型的數目
首先，您必須了解您要建立的叢集將用於什麼用途，以及您要規劃哪些要部署到此叢集中的應用程式種類。 如果您不清楚叢集的用途，您很可能還未準備好進入容量規劃程序。

建立您的叢集一開始所需的節點類型的數目。  每個節點類型都會對應到虛擬機器調整集。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。 因此，節點類型數目的決定基本上可歸結為下列考量︰

* 您的應用程式是否有多個服務，而且其中是否有任何服務必須是公開或網際網路對向的服務？ 一般應用程式包含可接收用戶端輸入的前端閘道服務，以及一或多個與前端服務溝通的後端服務。 因此，在此情況下，您最終會有至少兩個節點類型。
* 您 (構成應用程式) 的服務是否有不同的基礎結構需求，例如，更多的 RAM 或更高的 CPU 週期？ 例如，讓我們假設您想要部署的應用程式包含前端服務和後端服務。 前端服務可以在容量較小 (D2 之類的 VM 大小)，且擁有可連線至網際網路之連接埠的 VM 上執行。  不過，需要大量計算的後端服務必須在容量較大 (D4、D6、D15 的 VM 大小)，且不連線至網際網路的 VM 上執行。
  
  在此範例中，您可以決定將所有服務都放在一個節點類型上，但我們建議您將它們放在包含兩個節點類型的叢集上。  這可讓每個節點類型都有不同的屬性，例如，網際網路連線或 VM 大小。 VM 的數目也可以單獨調整。  
* 您無法預測未來，因此請利用您所知道的事實，決定您的應用程式一開始所需的節點類型的數目。 您之後都可以新增或移除節點類型。 Service Fabric 叢集必須至少有一個節點類型。

## <a name="the-properties-of-each-node-type"></a>每個節點類型的屬性
**節點類型**就像是雲端服務中的角色。 可用來定義定義 VM 的大小、VM 的數目，以及 VM 的屬性。 在 Service Fabric 叢集中定義的每個節點類型，都會安裝為不同的虛擬機器擴展集 (VMSS)。 VMSS 是一種 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 如果定義為不同的 VMSS，則每個節點類型都可以獨立相應增加或相應減少、可以開放不同組的連接埠，而且可以有不同的容量計量。

請參閱[這份文件](service-fabric-cluster-nodetypes.md)，以更仔細了解 Nodetypes 與 VMSS 的關聯性、如何 RDP 至其中一個執行個體、開啟新連接埠等。

您的叢集可以多個節點類型，但主要節點類型 (您在入口網站定義的第一個節點類型) 必須至少有 5 個 VM 供叢集用於生產工作負載 (或至少有 3 個 VM 供測試叢集使用)。 如果您要使用 Resource Manager 範本建立叢集，您會在節點類型定義下找到 **is Primary** 屬性。 主要節點類型就是放置 Service Fabric 系統服務所在的節點類型。  

### <a name="primary-node-type"></a>主要節點類型
若是包含多個節點類型的叢集，您必須選擇其中一個做為主要節點類型。 以下是主要節點類型的特性︰

* 主要節點類型的 **VM 大小下限**取決於您選擇的**持久性層級**。 持久性層級的預設值為 Bronze。 如需有關持久性層級的定義以及可採用的值，請向下捲動。  
* 主要節點類型的 **VM 數目下限**取決於您選擇的**可靠性層級**。 可靠性層級的預設值為 Silver。 如需有關可靠性層級的定義以及可採用的值，請向下捲動。 

 

* Service Fabric 系統服務 (例如，叢集管理員服務或映像存放區服務) 會放在主要節點類型上，因此，叢集的可靠性和持久性取決於您為主要節點類型所選取的可靠性層級值與持久性層級值。

![有兩個節點類型的叢集螢幕擷取畫面 ][SystemServices]

### <a name="non-primary-node-type"></a>非主要節點類型
如果是包含多個節點類型的叢集，則會有一個主要節點類型，其餘則是非主要節點類型。 以下是非主要節點類型的特性︰

* 此節點類型的 VM 大小下限取決於您選擇的持久性層級。 持久性層級的預設值為 Bronze。 如需有關持久性層級的定義以及可採用的值，請向下捲動。  
* 此節點類型的 VM 數目下限可以是 1。 不過，您應該根據您想要在這個節點類型中執行的應用程式/服務的複本數目，選擇這個數目。 部署叢集之後，節點類型中的 VM 數目可能會增加。

## <a name="the-durability-characteristics-of-the-cluster"></a>叢集的持久性特性
持久性層級用來向系統指示您的 VM 對於基本 Azure 基礎結構所擁有的權限。 在主要節點類型中，此權限可讓 Service Fabric 暫停會影響系統服務及具狀態服務的仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像，或 VM 移轉)。 在非主要節點類型中，此權限可讓 Service Fabric 暫停會影響其中所執行之具狀態服務的仲裁需求的任何 VM 層級基礎結構要求，例如，VM 重新開機、VM 重新安裝映像、VM 移轉等等。

此權限會以下列值表示︰

* Gold - 每個 UD 可持續暫停基礎結構工作 2 小時。 Gold 持久性只能在完整節點 VM SKU (例如 D15_V2、G5 等) 上啟用。
* Silver - 每個 UD 可持續暫停基礎結構工作 30 分鐘 (此值目前未啟用。 啟用之後，就可在單一核心以上的所有標準 VM 上使用)。
* Bronze - 無權限。 這是預設值。

## <a name="the-reliability-characteristics-of-the-cluster"></a>叢集的可靠性特性
可靠性層級用來設定您想要在此叢集中的主要節點類型上執行的系統服務複本數目。 複本數目越多，叢集中的系統服務越可靠。  

可靠性層級可以採用以下的值。

* Platinum - 執行包含 9 個目標複本集的系統服務
* Gold - 執行包含 7 個目標複本集的系統服務
* Silver - 執行包含 5 個目標複本集的系統服務
* Bronze - 執行包含 3 個目標複本集的系統服務

> [!NOTE]
> 您選擇的可靠性層級會決定您的主要節點類型必須具備的節點數目下限。 可靠性層級與叢集大小上限沒有關係。 因此，您可以有 20 個節點叢集在 Bronze 可靠性層級執行。
> 
> 

 您可以選擇將叢集的可靠性從一個層級更新為另一個層級。 如此一來就會觸發變更系統服務複本集計數所需的叢集升級。 請先等候升級完成，再對叢集進行任何其他變更，例如新增節點等等。您可以在 Service Fabric Explorer 上或執行 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) 監視升級的進度


## <a name="primary-node-type---capacity-guidance"></a>主要節點類型 - 容量指引

以下是規劃主要節點類型容量的指引

1. **VM 執行個體數目︰**若在 Azure 中執行任何生產工作負載，您必須指定 Silver 或更高的可靠性層級，而這會轉譯為最小的主要節點類型大小 5。
2. **VM SKU：**主要節點類型是執行系統服務的地方，因此，您為此而選擇的 VM SKU，必須考量您打算投入叢集的整體尖峰負載。 我用比喻來闡明我的意思 - 將主要節點類型想像成您的「肺」，它供應氧氣給您的腦，如果腦沒有足夠的氧氣，您的身體就會出問題。 

叢集的容量需求完全取決於您打算在叢集中執行的工作負載，因此，我們無法為您特定的工作負載提供定性的指引，但以下是概括性的指引，可協助您開始進行

生產工作負載 


- 建議的 VM SKU 是標準 D3 或標準 D3_V2 或對等項目，並搭配至少 14 GB 的本機 SSD。
- 支援使用的最小 VM SKU 是標準 D1 或標準 D1_V2 或對等項目，並搭配至少 14 GB 的本機 SSD。 
- 局部核心 VM SKU 不支援生產工作負載，例如標準 A0。
- 基於效能理由，標準 A1 SKU 確定不支援生產工作負載。


## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>非主要節點類型 - 具狀態工作負載的容量指引

請參閱以下內容，以了解使用 Service Fabric 可靠集合或 Reliable Actors 的工作負載。 深入了解[程式設計模型](service-fabric-choose-framework.md)。

1. **VM 執行個體數目︰**對於具狀態生產工作負載，建議使用目標複本計數至少為 5 來執行工作負載。 這表示在穩定狀態下，最後每個容錯網域和升級網域中會有一個複本 (來自複本集)。 系統服務的整個可靠性層級概念，實際上只是為系統服務指定此設定。

因此，對於生產工作負載，如果您執行具狀態工作負載，建議的最小非主要節點類型大小為 5。

2. **VM SKU：**這是執行應用程式服務的節點類型，因此，您為此而選擇的 VM SKU，必須考量您打算投入每個節點的尖峰負載。 Nodetype 的容量需求完全取決於您打算在叢集中執行的工作負載，因此，我們無法為您特定的工作負載提供定性的指引，但以下是概括性的指引，可協助您開始進行

生產工作負載 

- 建議的 VM SKU 是標準 D3 或標準 D3_V2 或對等項目，並搭配至少 14 GB 的本機 SSD。
- 支援使用的最小 VM SKU 是標準 D1 或標準 D1_V2 或對等項目，並搭配至少 14 GB 的本機 SSD。 
- 局部核心 VM SKU 不支援生產工作負載，例如標準 A0。
- 基於效能理由，標準 A1 SKU 確定不支援生產工作負載。


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>非主要節點類型 - 無狀態工作負載的容量指引

請參閱以下內容，以了解無狀態工作負載

**VM 執行個體數目︰**對於無狀態的生產工作負載，支援的最小非主要節點類型大小為 2。 這可讓您執行應用程式的兩個無狀態執行個體，在遺失 VM 執行個體的情況下，您的服務仍可繼續運作。 

> [!NOTE]
> 如果您的叢集在小於 5.6 的 Service Fabric 版本上執行，由於執行階段的瑕疵 (已規劃在 5.6 中修正)，將非主要節點類型相應減少到小於 5 時，將會導致叢集健全狀況變得狀況不良，直到您以適當的節點名稱呼叫 [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) 為止。 如需詳細資訊，請參閱[放大或縮小執行 Service Fabric 叢集](service-fabric-cluster-scale-up-down.md)
> 
>

**VM SKU：**這是執行應用程式服務的節點類型，因此，您為此而選擇的 VM SKU，必須考量您打算投入每個節點的尖峰負載。 Nodetype 的容量需求完全取決於您打算在叢集中執行的工作負載，因此，我們無法為您特定的工作負載提供定性的指引，但以下是概括性的指引，可協助您開始進行

生產工作負載 


- 建議的 VM SKU 是標準 D3 或標準 D3_V2 或對等項目。 
- 支援使用的最小 VM SKU 是標準 D1 或標準 D1_V2 或對等項目。 
- 局部核心 VM SKU 不支援生產工作負載，例如標準 A0。
- 基於效能理由，標準 A1 SKU 確定不支援生產工作負載。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>後續步驟
一旦您完成容量規劃並設定叢集之後，請閱讀︰

* [Service Fabric 叢集安全性](service-fabric-cluster-security.md)
* [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)
* [Nodetypes 與 VMSS 的關聯性](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

