---
title: "平衡 Azure Service Fabric 叢集 | Microsoft Docs"
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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: ee77a01b2bb17ab70099e891e00255d1cec676f6


---
# <a name="balancing-your-service-fabric-cluster"></a>平衡 Service Fabric 叢集
「Service Fabric 叢集資源管理員」支援動態負載變更、因應節點或服務的新增或移除、修正條件約束違規，以及重新平衡叢集。 但它執行這些工作的頻率為何？這些工作又是如何觸發的？

與平衡相關的第一組控制項是一組計時器。 這些計時器控管叢集資源管理員針對需要處理的事項來檢查叢集狀態的頻率。 一共有三種不同的作業類別，每一個都有相對應的計時器。 如下：

1. 放置 - 這個階段涉及安置任何遺漏的具狀態複本或無狀態執行個體。 這既涵蓋新服務，也涵蓋處理已失敗的具狀態複本或無狀態執行個體。 刪除和捨棄複本或執行個體都是在這裡處理。
2. 條件約束檢查 – 這個階段會檢查並更正系統內不同放置條件約束 (規則) 的違規情形。 規則範例包括像是確保節點不超出容量，以及符合服務的放置條件約束。
3. 平衡 - 這個階段會根據為不同計量設定的所需平衡層級，查看是否有必要使用主動式重新平衡。 如果有必要，就會嘗試在叢集中尋找更平衡的安排方式。

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>設定叢集資源管理員的步驟和計時器
「叢集資源管理員」可執行的每個不同類型修正，都是由控管其頻率的不同計時器所控制。 當每個計時器啟動時，便會排程工作。 根據預設，Resource Manager 會：

* 每 1/10 秒掃描一次其狀態並套用更新 (例如記錄某個節點已關閉)
* 每秒設定一次放置和條件約束檢查旗標
* 每&5; 秒設定一次平衡旗標。

我們可以看到這反映在下列組態資訊中：

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

透過 ClusterConfig.json (適用於獨立部署) 或 Template.json (適用於 Azure 裝載的叢集)：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

目前「叢集資源管理員」只會依序一次執行這些動作其中之一 (這就是為什麼我們將這些計時器稱為「最小間隔」)。 例如，「叢集資源管理員」在平衡叢集之前，會先處理要建立服務的擱置中要求。 如您所見，「叢集資源管理員」會依據指定的預設時間間隔，掃描和檢查它需要經常執行的一切項目，因此在每個步驟結束時所做的一組變更通常很小。 經常進行少量變更可讓「叢集資源管理員」快速因應叢集中發生的情況。 由於許多相同類型的事件易於同時發生，因此預設計時器提供一些批次處理。 「叢集資源管理員」預設不會將叢集中數小時的變更整個掃描一遍，然後嘗試一次處理所有變更。 這麼做會導致變換量激增。

「叢集資源管理員」也需要一些其他資訊，才能判斷叢集是否處於不平衡的狀態。 因此，我們有其他兩個的設定︰「平衡臨界值」和「活動臨界值」。

## <a name="balancing-thresholds"></a>平衡臨界值
平衡臨界值是觸發主動式重新平衡的主要控制項。 MinLoadBalancingInterval 計時器只能決定「叢集資源管理員」的檢查頻率 - 並不意謂著有發生任何事情。 平衡臨界值會以某個特定度量來定義叢集的不平衡程度，使資源管理員能夠考量它是否不平衡並觸發平衡作業。

平衡臨界值會根據每個度量定義為叢集定義的一部分。 如需有關計量的詳細資訊，請參閱[這篇文章](service-fabric-cluster-resource-manager-metrics.md)。

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

透過 ClusterConfig.json (適用於獨立部署) 或 Template.json (適用於 Azure 裝載的叢集)：

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

度量的平衡臨界值是一個比率。 如果負載最多之節點的負載量除以負載最少之節點的負載量超過此數字，此叢集就會被視為不平衡。 因此，下一次「叢集資源管理員」進行檢查時，就會觸發平衡作業。

<center>
![平衡臨界值範例][Image1]
</center>

在此範例中，每個服務皆取用一單位的某個計量。 在上半部的範例中，節點的負載上限為&5;，而下限為&2;。 假設此計量的平衡臨界值為&3;。 由於叢集中的比率是 5/2 = 2.5，小於指定的平衡臨界值 3，因此叢集處於平衡狀態。 當「叢集資源管理員」進行檢查時，不會觸發任何平衡作業。

在下半部的範例中，節點的負載上限為&10;，而下限為&2;，所以比率為&5;。 5 大於該計量的指定平衡臨界值&3;。 因此，下一次引發平衡計時器時，便會排定執行重新平衡。 在類似這樣的情況中，有些負載幾乎肯定會分散到 Node3。 由於「Service Fabric 叢集資源管理員」並不使用窮盡方法，因此有些負載也可能分散到 Node2。 此做法可將節點間的整體差異縮減到最小，這也是「叢集資源管理員」的其中一個目標。

<center>
![平衡臨界值範例動作][Image2]
</center>

使數據低於平衡臨界值並不是一個明確的目標。 「平衡臨界值」只是一個「觸發因子」，用來告訴「叢集資源管理員」應該深入檢查叢集，以判斷可以進行哪些可能的改進。 確實，就算開始進行平衡搜尋，也不代表會移動任何東西 - 有時雖然叢集處於不平衡狀態，但是卻無法改善情況。

## <a name="activity-thresholds"></a>活動臨界值
有時候，雖然節點處於相對的不平衡狀態，但叢集中的負載「總量」  卻很低。 缺乏負載可能是暫時性的下跌情況，或是因為叢集是新的且才剛啟動而已。 不論是上述哪一種情況，您可能都不想花時間平衡叢集，因為能獲得的好處很少。 如果叢集進行平衡作業，您將需要花費網路和計算資源將東西四處移動，但卻不會產生任何「絕對的」差異。 為了避免這種情況，出現了另一種控制方式，稱為「活動臨界值」。 「活動臨界值」可讓您為活動指定某種絕對下限。 如果沒有任何節點超出此臨界值，則即使達到「平衡臨界值」，也不會觸發平衡作業。

讓我們看看下圖的範例。 讓我們一併假設將此計量的「平衡臨界值」保持為 3，但現在我們也有「活動臨界值」1536。 在第一個案例中，根據「平衡臨界值」，叢集是處於不平衡狀態，但沒有任何節點達到「活動臨界值」，因此不會發生任何事情。 在下半部的範例中，Node1 遠遠超出「活動臨界值」。 由於同時超出該計量的「平衡臨界值」和「活動臨界值」，因此會排定平衡作業。

<center>
![活動臨界值範例][Image3]
</center>

如同平衡臨界值，活動臨界值會透過叢集定義根據每個度量進行定義︰

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

透過 ClusterConfig.json (適用於獨立部署) 或 Template.json (適用於 Azure 裝載的叢集)：

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

平衡臨界值和活動臨界值都與特定計量繫結 - 只有同時超出同一計量的「平衡臨界值」和「活動臨界值」時，才會觸發平衡作業。

## <a name="balancing-services-together"></a>一起平衡服務
值得一提的是，叢集是否不平衡是整個叢集的決策。 不過，我們修正此種情況的方法是將個別的服務複本和執行個體四處移動。 這很合理，對吧？ 如果記憶體堆疊在某一個節點上，可能是由多個複本或執行個體所造成。 若要修正不平衡的狀態，可能需要移動所有使用不平衡計量的具狀態複本或無狀態執行個體。

不過，偶爾也會有將平衡的服務移動的情況。 在有其他不平衡情形的時候，如果該服務的所有度量都已達到平衡 (即使全然如此)，怎麼會發生該服務被四處移動的情形？ 我們一起看看！

假設有四個服務：Service1、Service2、Service3 及 Service4。 Service1 會針對 Metric1 和 Metric2 度量進行報告、Service2 會針對 Metric2 和 Metric3 度量進行報告、Service3 會針對 Metric3 和 Metric4 度量進行報告，而 Service4 會針對 Metric99 度量進行報告。 您應該可以看出這個範例要表達什麼。 這是個串聯的狀況！ 我們並非實際上擁有&4; 個獨立的服務，而是有一系列相關的服務 (Service1、Service2 及 Service3)，以及一個獨立的服務。

<center>
![將服務一起平衡][Image4]
</center>

因此，Metric1 若發生不平衡，可能會導致將屬於 Service3 (不會回報 Metric1) 的複本或執行個體四處移動。 這些移動通常是有限的，但幅度也可能更大，這取決於 Metric1 確切的不平衡程度，以及必須在叢集中進行哪些變更才能更正它。 我們也可以肯定地說計量 1、2 或 3 若發生不平衡，並不會導致 Service4 中發生移動。 這麼做並沒有必要，因為將屬於 Service4 的複本或執行個體四處移動完全不會影響計量 1、2 或 3 的平衡。

由於服務可能會經過新增、移除或是發生計量組態變更，因此「叢集資源管理員」會自動找出哪些服務彼此相關。 例如，在兩次執行平衡作業之間，可能已經重新設定 Service2 來移除 Metric2。 這項變更會中斷 Service1 與 Service2 之間的鏈結。 現在，您擁有的是三個相關服務群組，而非兩個︰

<center>
![將服務一起平衡][Image5]
</center>

## <a name="next-steps"></a>後續步驟
* 度量是 Service Fabric 叢集資源管理員管理叢集中的耗用量和容量的方式。 若要深入了解度量及其設定方式，請參閱[這篇文章](service-fabric-cluster-resource-manager-metrics.md)
* 移動成本是向叢集資源管理員發出訊號，表示移動某些服務會比較貴的其中一種方式。 如需有關移動成本的詳細資訊，請參閱[這篇文章](service-fabric-cluster-resource-manager-movement-cost.md)
* 叢集資源管理員有數個為減緩叢集的流失而可以設定的節流。 這些節流通常不是必要的，但若有需要，您可以參閱 [這裡](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Jan17_HO4-->


