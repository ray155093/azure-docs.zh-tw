---
title: "使用 Azure Service Fabric 叢集 Resource Manager 平衡叢集 | Microsoft Docs"
description: "使用 Azure Service Fabric 叢集資源管理員平衡叢集的簡介。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1ca022d7412479e8e072ec319020b1091196bc04


---
# <a name="balancing-your-service-fabric-cluster"></a>平衡 Service Fabric 叢集
Service Fabric 叢集資源管理員允許報告動態負載、對叢集中的變更做出反應、修正條件約束違規，以及在必要時重新平衡叢集。 但它執行這些工作的頻率為何？這些工作又是如何觸發的？ 有數個與此相關的控制項。

與平衡相關的第一組控制項是一組計時器。 這些計時器控管叢集資源管理員針對需要處理的事項來檢查叢集狀態的頻率。 一共有三種不同的作業類別，每一個都有相對應的計時器。 如下：

1. 放置 - 這個階段負責安置任何遺漏的具狀態複本或無狀態執行個體。 這同時涵蓋新服務，以及處理已失敗且需要重建的具狀態複本或無狀態執行個體。 刪除與捨棄複本或執行個體也在這裡處理。
2. 條件約束檢查 – 這個階段會檢查並更正系統內不同放置條件約束 (規則) 的違規情形。 規則範例包括確保節點不會超出容量，以及符合服務的放置條件約束 (稍後會詳細說明)。
3. 平衡 - 這個階段會根據為不同度量設定的所需平衡層級查看是否需要主動式重新平衡，而如果需要，則會嘗試尋找叢集中更平衡的排列方式。

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>設定叢集資源管理員的步驟和計時器
叢集資源管理員可以執行的每個不同類型修正，都是由不同的計時器所控制，並能控管修正頻率。 比方說，如果您只想處理每小時在叢集中的放置新服務工作負載 (以進行分批處理)，但又想要每隔幾秒鐘定期執行平衡檢查，您可以設定該行為。 當每個計時器啟動時，便會排程工作。 根據預設，資源管理員會每 1/10 秒掃描其狀態並套用更新 (針對所有於上次掃描後所發生的變更進行批次處理，例如察覺到節點已關閉)、每秒設定放置和條件約束檢查旗標，並於每 5 秒設定平衡旗標。

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

今天我們只會依序一次執行這些動作的其中之一 (這就是為什麼我們將這些設定稱為「最小間隔」)。 這麼做的其中一個原因，是為了在繼續平衡叢集之前，針對所有要求建立複本的擱置要求做出回應。 如您所見，我們可以依照指定的預設時間間隔，掃描並檢查我們需要非常頻繁執行的任何事物，這表示我們在每個步驟結尾所做的變更組通常比較小：我們不是掃描叢集中數小時內的變更並嘗試一次全部更正，而是嘗試在事件發生時進行處理，並在有許多事件同時發生時利用批次處理。 這會使 Service Fabric 資源管理員對於叢集中發生的事情非常有反應。

雖然這些工作大部分都相當直覺 (有條件約束違規就修正它們，有需要建立的服務就建立它們)，叢集資源管理員也需要一些其他資訊以判斷叢集是否處於不平衡的狀態。 因此，我們有其他兩個的設定︰「平衡臨界值」和「活動臨界值」。

## <a name="balancing-thresholds"></a>平衡臨界值
平衡臨界值是觸發主動式平衡的主要控制項 (請記住，計時器只能決定叢集資源管理員的檢查頻率，並不代表一定會發生任何事情)。 平衡臨界值會以某個特定度量來定義叢集的不平衡程度，使資源管理員能夠考量它是否不平衡並觸發平衡作業。

平衡臨界值會根據每個度量定義為叢集定義的一部分。 (如需度量的詳細資訊，請參閱 [這篇文章](service-fabric-cluster-resource-manager-metrics.md))。

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

度量的平衡臨界值是一個比率。 如果負載最多之節點的負載量除以負載最少之節點的負載量超過此數目，此叢集就會被視為不平衡，並會在叢集資源管理員下次檢查時觸發平衡作業 (根據預設為每 5 秒，並由 MinLoadBalancingInterval 所控管，如上所示)。

![平衡臨界值範例][Image1]

在這個簡單的範例中，每個服務會取用一單位的某個度量。 在最上面的範例中，節點的最大負載為 5，而最小值為 2。 假設此度量的平衡臨界值為 3。 因此，在最上面的範例中，叢集會被視為平衡，且不會在叢集資源管理員檢查時觸發平衡作業 (因為叢集中的比率為 5/2 = 2.5，且該比例小於指定的平衡臨界值 3)。

在最下面的範例中，節點的最大負載為 10，而最小為 2，導致比率為 5。 這使叢集超過設計的平衡臨界值，也就是該度量的 3 個單位。 因此，下一次引發平衡計時器時，便會排程執行全域重新平衡。 請注意，就算開始平衡搜尋，也不代表會移動任何東西。有時候雖然叢集是處於不平衡的狀態，但是並無法改善。但在目前這個範例中，(至少以預設來說) 系統幾乎一定會將某些負載散佈到 Node3。 請注意，我們不是使用窮盡方法，因此有些負載也可能會被散佈到 Node2，因為那會使節點之間的整體差異縮減到最小，但是我們預期多數負載會流向 Node3。

![平衡臨界值範例動作][Image2]

請注意，使數據低於平衡臨界值並非是明確的目標，平衡臨界值只是個「觸發程序」  ，能夠告訴叢集資源管理員應該查看叢集，以判斷可以進行哪些可能的改進。

## <a name="activity-thresholds"></a>活動臨界值
有時候，雖然節點處於相對的不平衡狀態，但叢集中的負載「總量」  卻很低。 這可能只是因為當天時間，或是因為叢集是新的而且才剛開始啟動而已。 在任何一種情況下，您可能不需要花時間去進行平衡叢集，因為實際的收益很少，您基本上只是在花費網路和計算資源去將東西四處挪動，但卻沒有產生任何絕對的差異。 為了避免這樣做，資源管理員內有另一個稱為活動臨界值的控制項，可讓您指定活動的絕對下限 - 如果沒有任何節點有至少這麼多的負載，則即使達到平衡臨界值，也不會觸發平衡作業。

舉例來說，假設在我們的報告中，這些節點的使用量總數如下所示。 同時假設我們讓平衡臨界值保持為 3，但我們現在也有 1536 的活動臨界值。 在第一個案例中，根據平衡臨界值，叢集是處於不平衡狀態，但沒有任何節點符合活動臨界值下限，所以我們不會採取任何動作。 在最下面的範例中，Node1 已遠遠超過活動臨界值，因此將會執行平衡 (由於度量的平衡臨界值和活動臨界值都已超過)

![活動臨界值範例][Image3]

如同平衡臨界值，活動臨界值會透過叢集定義根據每個度量進行定義︰

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

請注意，平衡和活動臨界值都會繫結至度量，而且只有在同一個度量的平衡和活動臨界值都超過時才會觸發平衡。 因此，如果我們超過記憶體的平衡臨界值和 CPU 的活動臨界值，只要其餘臨界值未超過 (CPU 的平衡臨界值和記憶體的活動臨界值)，就不會觸發平衡。

## <a name="balancing-services-together"></a>一起平衡服務
值得一提的是，叢集是否不平衡是整個叢集的決策，但我們解決此種情況的方法是移動個別的服務複本和執行個體。 這很合理，對吧？ 如果記憶體堆疊於某一個節點上，可能是由多個複本或執行個體所造成，所以需要移動所有使用受影響、不平衡度量的具狀態複本或無狀態執行個體。

雖然有時候客戶會呼叫我們或提出票證，表示已移動不平衡的服務。 在有其他不平衡情形的時候，如果該服務的所有度量都已達到平衡 (即使全然如此)，怎麼會發生該服務被四處移動的情形？ 我們一起看看！

假設有四個服務：Service1、Service2、Service3 及 Service4。 Service1 會針對 Metric1 和 Metric2 度量進行報告、Service2 會針對 Metric2 和 Metric3 度量進行報告、Service3 會針對 Metric3 和 Metric4 度量進行報告，而 Service4 會針對 Metric99 度量進行報告。 您應該可以看出這個範例要表達什麼。 這是個串聯的狀況！ 就叢集資源管理員的觀點而言，我們實際上並沒有 4 個獨立的服務，而是有一系列相關的服務 (Service1、Service2 及 Service3)，以及一個獨立的服務。

![一起平衡服務][Image4]

因此，Metric1 若發生不平衡，可能會導致屬於 Service3 的複本或執行個體四處移動。 這些移動通常很有限，但是根據 Metric1 究竟有多不平衡以及必須在叢集中進行哪些變更才能進行更正，移動幅度可能會變大。 我們還可以肯定地說度量 1、2 或 3 不平衡絕不會導致 Service4 移動 – 沒有必要，因為四處移動屬於 Service4 的複本或執行個體完全不會影響度量 1、2 或 3 的平衡。

叢集資源管理員會自動找出彼此相關的服務，因為服務可能會新增、移除，或是其度量設定被變更。例如，在兩次執行平衡作業之間，Service2 可能已重新設定以移除 Metric2。 這會中斷 Service1 和 Service2 之間的鏈結。 您現在有三個服務群組 (而非兩個)︰

![一起平衡服務][Image5]

## <a name="next-steps"></a>後續步驟
* 度量是 Service Fabric 叢集資源管理員管理叢集中的耗用量和容量的方式。 若要深入了解度量及其設定方式，請查看 [這篇文章](service-fabric-cluster-resource-manager-metrics.md)
* 移動成本是向叢集資源管理員發出訊號，表示移動某些服務會比較貴的其中一種方式。 若要深入了解移動成本，請參閱 [這篇文章](service-fabric-cluster-resource-manager-movement-cost.md)
* 叢集資源管理員有數個為減緩叢集的流失而可以設定的節流。 這些節流通常不是必要的，但若有需要，您可以參閱 [這裡](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Nov16_HO3-->


