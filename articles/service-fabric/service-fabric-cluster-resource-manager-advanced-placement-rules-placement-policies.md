---
title: "Service Fabric 叢集 Resource Manager - 放置原則 | Microsoft Docs"
description: "Service Fabric 服務的其他放置原則和規則的概觀"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f4daec67cd35de60568ffd22010164f10785a5d4


---
# <a name="placement-policies-for-service-fabric-services"></a>Service Fabric 服務的放置原則
有許多不同的額外規則，可能會在下列情況下獲得您的重視：您的 Service Fabric 叢集會跨越廣泛的地理距離 (例如多個資料中心或 Azure 區域)，或您的環境會跨越多個地緣政治控制區域 (或是某些其他情況，例如您所注重的法律或政策界線，或是距離會造成效能/延遲的影響)。 這其中大部分規則都能透過節點屬性和放置條件約束來設定，但有一些規則比較複雜。 為了簡化，我們提供了這些額外的命令。 放置原則和其他放置條件約束一樣，都可以根據個別具名服務執行個體來設定。

## <a name="specifying-invalid-domains"></a>指定無效的網域
InvalidDomain 放置原則可讓您指定特定的容錯網域對此工作負載是無效的。 此原則可確保特定的服務絕對不會在特定的區域中執行，例如，基於地緣政治或公司政策的緣故。 您可以透過個別原則指定多個無效的網域。

![無效的網域範例][Image1]

程式碼：

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>指定所需的網域
所需的網域放置原則要求服務的所有具狀態複本或無狀態服務執行個體都存在於指定的網域中。 您可以透過個別原則指定多個所需的網域。

![必要的網域範例][Image2]

程式碼：

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>指定主要複本的慣用網域
慣用的主要網域是一個有趣的控制項，因為它允許在可行的情況下選取應放置主要複本的容錯網域。 若一切狀況良好，主要複本最終將會在這個網域中。 如果網域或主要複本失敗，或基於某些原因必須關機，則主要複本將會移轉至某些其他位置。 如果此位置並非慣用的網域，叢集資源管理員便會在可行的情況下將它移回慣用的網域。 當然，此設定僅適用於具狀態服務。 此原則最適合用於會跨越 Azure 區域或多個資料中心的叢集。 在這些情況中，您會使用所有位置進行備援，但希望主要複本可以放置在特定的位置，來為傳輸到主要複本的作業提供較低的延遲 (寫入及預設所有讀取都是由主要複本提供)。

![慣用的主要網域和容錯移轉][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>要求複本在所有網域之間散佈，且不允許封裝
您可以指定的另一個原則，是要求一律在可用的容錯網域之間散佈複本。 根據預設，這將會在多數叢集狀況良好的情況下發生，不過仍然可能會有特定資料分割最後被暫時封裝成單一容錯或升級網域的例外狀況。 例如，假設叢集在 3 個容錯網域 (0、1 及 2) 中具有 9 個節點，且您的服務具有 3 個複本。假設位於容錯網域 1 和 2 中，用於那些複本的節點關閉了，而且因為容量問題，使得那些網域中沒有任何其他節點是有效的。 如果 Service Fabric 要為那些複本建置取代項目，叢集資源管理員便需要將它們置於容錯網域 0 中，但那將會違反容錯網域條件約束。 那也會增加遺失整個複本集的機會 (如果永久遺失 FD 0 的話)。 (如需一般的條件約束和條件約束優先順序的詳細資訊，請參閱[本主題](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities))

如果您曾經見過像是「負載平衡器已偵測到此複本的條件約束違規：fabric:/<some service name> Secondary Partition <some partition ID> 已違反條件約束：FaultDomain」，便代表您已觸發此條件，或是類似它的條件。 這些情況通常是暫時性的 (節點不會長時間關閉，或是在節點持續關閉並需要建置取代的情況下，在正確的容錯網域中還是有其他有效的節點)，但仍然有某些工作負載願意以失去所有複本的風險去交換可用性。 我們可以透過指定 "RequireDomainDistribution" 原則來達到這個目的，該原則會保證相同的容錯或升級網域中，永遠不會存在兩個來自相同資料分割的複本。

某些工作負載會偏好隨時都具有目標數目的複本 (也就是狀態的複本)，並針對完全網域失敗做出賭注，因為他們知道通常可以復原本機狀態，而其他工作負載則會偏好提早停機，而不願承受正確性和資料遺失等問題的風險。 由於大多數的生產工作負載都會執行超過 3 個複本，因此，預設值是不需要網域發佈，並讓平衡與容錯移轉能夠正常處理這些情況，即使這表示網域暫時會具有多個要封裝到其中的複本。

程式碼：

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

現在，是否可針對未跨越地理區域的叢集中的服務使用這些組態？ 當然可以！ 但也沒有什麼好理由要這樣做，特別是除非您正在執行跨越地理區域的叢集，否則您應該要避免使用必要的、無效的，及慣用的網域設定。強制特定工作負載在單一軌道上執行，或是偏好本機叢集上某些區段並沒有太大的意義，除非存在不同的硬體或工作負載分割，而那些狀況也可以透過標準的放置條件約束來處理。

## <a name="next-steps"></a>後續步驟
* 如需可用來設定服務的其他選項的詳細資訊，請查看 [深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Dec16_HO2-->


