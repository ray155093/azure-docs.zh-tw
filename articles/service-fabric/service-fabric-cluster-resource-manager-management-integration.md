---
title: "Service Fabric 叢集 Resource Manager - 管理整合 | Microsoft Docs"
description: "叢集資源管理員和 Service Fabric 管理之間的整合點概觀。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: b2428f93680c12c76000fa8eb1a7138f72a8efe7
ms.openlocfilehash: 9d67f089f4aba03e846a8fe020a91b6b1ac6ea48
ms.lasthandoff: 02/11/2017


---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>叢集資源管理員與 Service Fabric 叢集管理整合
Service Fabric 叢集 Resource Manager 不是負責處理管理作業 (如應用程式升級) 的主要 Service Fabric 元件，但它會涉入其中。 叢集資源管理員協助管理的第一種方法是追蹤所需的叢集狀態及其內部的服務。 當叢集資源管理員無法讓叢集處於所需的設定時，它會送出健全狀況報告。 例如，容量不足，或有關將服務放在何處的規則發生衝突。 整合的另一方面必定與升級方式有關。 在升級期間，叢集資源管理員會稍微改變其行為。 以下我們將討論這兩個整合點。

## <a name="health-integration"></a>健康狀態整合
叢集資源管理員會持續追蹤您為服務定義的規則，以及節點和叢集中可用的容量。 如果無法滿足這些規則，或容量不足，則會發出健全狀況警告和錯誤。 例如，若節點超出容量，叢集資源管理員會移動服務來嘗試解決情況。 如果無法解決情況，則會發出健全狀況警告，指出哪一個節點超出容量和涉及哪些計量。

Resource Manager 健全狀況警告的另一個例子是違反放置條件約束。 例如，若您已定義放置條件約束 (例如 `“NodeColor == Blue”`)，而 Resource Manager 偵測到該條件約束的違規情形時，它會發出健全狀況警告。 這適用於自訂條件約束和預設條件約束 (例如容錯網域和升級網域條件約束)。

以下是這類健康狀態報告的範例。 在此情況下，健全狀況報告是針對系統服務的其中一個資料分割。 健全狀況訊息指出該資料分割的複本暫時封裝至太少的升級網域。

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

以下是此健康狀態訊息要告訴我們的事情︰

1. 所有複本本身的狀態都很好 (這是 Service Fabric 的首要之務)
2. 目前違反升級網域發佈條件約束 (意味者特定升級網域擁有過多的磁碟分割複本)
3. 哪個節點包含造成違規的複本 (具有下列識別碼的節點︰3d1a4a68b2592f55125328cd0f8ed477)
4. 報告時間 (2015 年 8 月 10 日下午 7:13:02)

這種資訊會引出在生產環境中出現的警示，讓您知道已發生問題。 在此情況下，我們需要查明 Resource Manager 為何一定要將複本封裝至升級網域。 例如，這可能是因為其他升級網域中的節點已關閉。

假設您想要建立服務，或 Resource Manager 嘗試找出位置來放置某些服務，但沒有任何可行的解決方案。 原因有很多，但通常是由於下列兩種情況之一︰

1. 某個暫時性情況導致無法正確地放置此服務執行個體或複本
2. 此服務的需求設定錯誤，以致無法滿足其需求。

在上述每一種情況下，您會看到叢集資源管理員提供的健全狀況報告，其中的資訊可協助您判斷目前的狀況及無法放置服務的原因。 我們將此程序稱為「條件約束消除序列」。 在此期間，系統會逐步解說會影響服務與記錄的已設定條件約束，以及它們所消除的項目。 如此一來，當項目無法放置時，您可以看到哪些節點已經消除及消除原因。

## <a name="constraint-types"></a>條件約束類型
讓我們討論您在這些健全狀況報告中可看見的每一個不同的條件約束。 這些條件約束通常不會消除節點，因為它們預設為軟式或最佳化層級。 但是，如果這些條件約束設定為硬型條件約束，或在罕見情況下確實導致消除節點，則您會看到相關的健全狀況訊息。

* **ReplicaExclusionStatic** 和 **ReplicaExclusionDynamic**：此條件約束指出來自相同分割區的兩個具狀態複本或無狀態執行個體，必須放在相同節點上 (不允許這麼做)。 ReplicaExclusionStatic 和 ReplicaExclusionDynamic 幾乎是完全相同的規則。 ReplicaExclusionDynamic 條件約束表示「我們無法將這個複本放在這裡，因為唯一提議的解決方案已在這裡放置一個複本」。 這與 ReplicaExclusionStatic 條件約束不同，它指出不是提議的衝突，而是實際的衝突。 在此情況下，已經有一個複本的節點上。 這會令人混淆嗎？ 是。 是否很重要？ 否。 如果您看到的條件約束消除序列包含 ReplicaExclusionStatic 或 ReplicaExclusionDynamic 條件約束，叢集資源管理員就會認為沒有足夠的節點。 進一步的條件約束通常會告訴我們節點太少會有何結果。
* **PlacementConstraint**︰如果您看到此訊息，就表示我們已消除一些節點，因為它們不符合服務的放置條件約束。 我們會在此此訊息中描繪出目前所設定的放置條件約束。 如果您已定義放置條件約束，則這是正常情形。 不過，如果放置條件約束中有問題，造成消除太多節點，您就應該注意。
* **NodeCapacity**︰這個條件約束表示叢集資源管理員無法將複本放在指出的節點上，因為這樣會導致節點超出容量。
* **Affinity**︰這個條件約束表示我們無法將複本放在受影響的節點上，因為這會導致違反同質性條件約束。 如需同質性的詳細資訊，請參閱[這篇文章](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** 和 **UpgradeDomain**︰如果將複本放在指出的節點上會導致複本封裝在特定的容錯網域或升級網域中，此條件約束就會消除節點。 [容錯與升級網域條件約束及產生的行為](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**︰您通常不會看到這個條件約束導致節點從解決方案中移除，因為它依預設會設為最佳化層級。 此外，慣用的位置條件約束通常出現在升級期間。 在升級期間，它用於將複本移回它們在升級開始時所在的位置。

### <a name="constraint-priorities"></a>條件約束優先順序
所有這些條件約束會讓您覺得：「嘿 – 我認為放置條件約束是我的系統中最重要的事。 我願意違反其他條件約束，甚至是像同質和容量的事項，如果它可確保不會違反放置條件約束。」

其實我們可以這麼做！ 可以使用幾個不同層級的強制執行來設定條件約束，但是會歸結為「硬式」(0)、「軟式」(1)、「最佳化」(2) 和「關閉」(-1)。 我們已定義的大部分條件約束都預設為硬式。 例如，大部分使用者通常不會將容量視為可以寬鬆的事項，幾乎全都不是硬式就是軟式。

不同的條件約束優先順序是某些條件約束違反警告比其他警告更常出現的原因：我們願意暫時放寬 (違反) 某些條件約束。 這些層級並不真的表示將會違反指定的條件約束，只是強制執行時有其優先順序。 這可讓叢集資源管理員在無法滿足所有條件約束時，做出正確的取捨。

在進階的情況中，您可以變更條件約束優先順序。 例如，假設您想要確保以違反同質性來解決節點容量問題。 為了達到此目的，您可以將同質性條件約束的優先順序設為「軟式」(1)，並將容量條件約束維持設為「硬式」(0)。

此組態檔中指定不同條件約束的預設優先順序值：

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

獨立部署透過 ClusterConfig.json，Azure 託管叢集透過 Template.json：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>容錯網域和升級網域的條件約束
如果想要保持服務分散於容錯網域和升級網域之間，叢集資源管理員會在 Resource Manager 引擎內以條件約束來模擬這個想法。 如需使用方式的詳細資訊，請參閱[叢集設定](service-fabric-cluster-resource-manager-cluster-description.md)一文。

在某些情況下，我們需要嚴格限制容錯網域和升級網域，以避免發生問題。 也有一些情況我們需要完全忽略它們 (雖然很短暫！)。 大致上，條件約束優先順序基礎結構的彈性可以運作的非常好，但不會經常需要用到。 在大部分情況下，一切都按照預設優先順序安排。 升級網域維持為軟式條件約束。 叢集資源管理員可能需要將一些複本封裝至升級網域，以處理升級、失敗或條件約束違規情形。 通常，只有當系統中發生許多失敗或其他問題，導致無法正確放置時，才會有這種情形。 如果已正確設定環境，則會完全遵守所有條件約束，包括容錯和升級條件約束，甚至在升級期間也是如此。 關鍵在於叢集資源管理員會監看您的條件約束，在偵測到違規時立即回報。

## <a name="the-preferred-location-constraint"></a>慣用的位置條件約束
PreferredLocation 條件約束有點不同，因此它是唯一設為「最佳化」的條件約束。 我們在升級過程中使用這個條件約束，以主動將服務放回它們在升級之前的位置。 這在實務上不可行有各種原因，但卻是很不錯的最佳化。

## <a name="upgrades"></a>升級
在應用程式和叢集升級過程中，叢集資源管理員也有所幫助，它在這期間有兩項作業︰

* 確保不影響叢集的規則和效能
* 嘗試協助順利地進行升級

### <a name="keep-enforcing-the-rules"></a>繼續強制執行規則
規則是需要注意的重點 – 在升級期間仍會強制執行一些嚴格條件約束 (如放置條件約束)。 放置條件約束可確保工作負載只在允許的位置執行，即在升級期間也一樣。 如果您的環境受到嚴格的條件約束，則升級可能需要很長的時間。 這是因為如果服務 (或它所在的節點) 必須關閉以利升級，則服務可選擇的去處不多。

### <a name="smart-replacements"></a>聰明取代
升級開始時，Resource Manager 會取得叢集目前配置方式的快照集。 隨著每個升級網域完成，它會嘗試讓事件回到原始配置方式。 因此，升級期間最多只有兩次轉換 (移出和移回受影響的節點)。 讓叢集回到升級之前的狀態也可確保升級不會影響叢集配置。 如果叢集在升級之前已妥善配置，則升級後也會妥善配置，或至少不會變差。

### <a name="reduced-churn"></a>減少流失
升級期間發生還會發生另一件事，就是叢集 Resource Manager 對正在升級的實體關閉平衡作業。 這表示如果您有兩個不同的應用程式執行個體，並升級其中一個，則暫停平衡該應用程式執行個體，而不是另一個。 阻止平衡可避免對升級本身做出不必要的反應，例如將服務移入已清空而不必升級的節點。 如果此次升級是「叢集」升級，則整個叢集在升級期間不會處於平衡狀態。 條件約束檢查 (確保強制執行規則) 會保持作用中，只是重新平衡會停用。

### <a name="relaxed-rules"></a>寬鬆的規則
通常，即使叢集整體受條件約束或已滿，您也希望升級完成。 在升級期間，必須能夠管理叢集的容量，這比平常更重要。 這是因為當升級在叢集內展開時，通常一次會關閉 5% 到 20% 的容量。 工作通常必須移至別處。 [緩衝容量](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity)的概念在此真正派上用場。 雖然在正常作業期間會遵守緩衝容量 (保持一些額外負荷)，但叢集資源管理員在升級期間會填滿總容量 (佔用緩衝區)。

## <a name="next-steps"></a>後續步驟
* 從頭開始，並 [取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)

