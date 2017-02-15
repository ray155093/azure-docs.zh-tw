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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0


---
# <a name="placement-policies-for-service-fabric-services"></a>Service Fabric 服務的放置原則
在一些罕見的情況下，您可能需要設定其他許多不同的規則。 這些情況的一些例子如下︰
* 如果 Service Fabric 叢集跨越一段地理距離 (例如多個內部部署資料中心) 或跨越多個 Azure 區域
* 如果您的環境跨越多個地緣政治管制區 (或在其他一些情況下，您擔心法律或政策界限)
* 由於叢集中的通訊穿越很長的距離，或經過某些較慢或或較不可靠的網路，確實要注意效能/延遲。

在這幾種情況下，必須注意在某些區域中永遠執行或永不執行某個服務。 同樣地，也必須嘗試將「主要」複本放在特定區域，以儘可能避免使用者延遲。

進階放置原則如下︰

1. 無效的網域
2. 所需的網域
3. 慣用的網域
4. 不允許封裝複本

以下大部分控制可透過節點屬性和放置條件約束來設定，但其中有一些比較複雜。 為了簡化起見，Service Fabric 叢集資源管理員提供這些額外的放置原則。 就像其他放置條件約束一樣，放置原則可以依個別具名服務執行個體來設定，還可以動態更新。

## <a name="specifying-invalid-domains"></a>指定無效的網域
InvalidDomain 放置原則可讓您指定特定的容錯網域對此工作負載是無效的。 此原則可確保特定的服務絕對不會在特定的區域中執行，例如，基於地緣政治或公司政策的緣故。 您可以透過個別原則指定多個無效的網域。

<center>
![無效的網域範例][Image1]
</center>

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

<center>
![所需的網域範例][Image2]
</center>

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
「慣用的主要網域」是值得注意的控制項，它允許在可行的情況下選取應放置「主要」複本的容錯網域。 若一切狀況良好，「主要」複本最後會在這個網域中。 如果網域或「主要」複本失敗，或基於某些原因而關閉，則「主要」複本會移轉至某些其他位置。 如果這個新的位置並非慣用的網域，叢集資源管理員會儘快將它移回慣用的網域。 當然，此設定僅適用於具狀態服務。 如果叢集跨越 Azure 區域或多個資料中心，但希望「主要」複本位於特定位置中，此原則最有用。 讓「主要」複本靠近使用者有助於縮短延遲，特別是在讀取時。

<center>
![慣用的主要網域和容錯移轉][Image3]
</center>

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
叢集狀況良好時，複本「通常」會散佈在網域之間，但給定資料分割的複本有時會暫時封裝到單一網域。 例如，假設叢集有九個節點在三個容錯網域中 (fd:/0、fd:/1 和 fd:/2)，而您的服務有三個複本。 假設 fd:/1 和 fd:/2 中用於這些複本的節點停止運作。 現在，叢集資源管理員通常會選擇這些相同容錯網域中的其他節點。 在此情況下，假設因為容量問題，這些網域中的其他節點都無效。 如果叢集資源管理員為這些複本建立替代項目，則必須選擇 fd:/0 中的節點。 不過，「這樣做」會導致違反容錯網域條件約束。 還更可能造成整個複本集停止運作或遺失 (如果永久遺失 FD 0 的話)。 如需一般情況下條件約束和條件約束優先順序的詳細資訊，請參閱[本主題](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)。

如果您曾看過類似 `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` 的健全狀況警告，表示您已遇到此狀況或類似的情況。 這很罕見，但可能發生，這些情況通常是暫時性，因為節點會恢復。 如果節點持續關閉，而且叢集資源管理員需要建置替代項目，理想的容錯網域中通常會有其他節點可用。

某些工作負載即使封裝至更少的網域，也永遠都有達到目標數目的複本。 這些工作負載打賭會同時全面發生永久性網域故障，通常可以復原本機狀態。 其他工作負載會提早停工，而不想冒著更正或遺失資料的風險。 由於大部分生產工作負載執行時會使用三個以上的複本、三個以上的容錯網域，以及每個故障網域的許多有效節點，預設是不需要網域分佈。 這可讓一般的平衡與容錯移轉處理這些情況下，即使這意味著一個網域中暫時可能封裝多個複本也一樣。

如果您想要對指定的工作負載停用這樣封裝，您可以在服務上指定 "RequireDomainDistribution" 原則。 設定此原則，叢集資源管理員可確保在相同的容錯網域或升級網域，不允許有兩個來自相同資料分割的複本。

程式碼：

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

現在，是否可針對未跨越地理區域的叢集中的服務使用這些組態？ 當然可以！ 但也沒有充分的理由。 除非您實際上正在執行跨越地理距離的叢集，否則，應特別避免必要、無效及慣用的網域組態。 如果試著強制在單一機架中執行指定的工作負載，或偏好本機叢集的某些區段而不是其他區段，這樣並沒有任何意義。 不同的硬體組態應該分散至網域，以及那些透過一般放置條件約束和節點屬性所處理的網域。

## <a name="next-steps"></a>後續步驟
* 如需可用來設定服務的其他選項的詳細資訊，請[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Jan17_HO1-->


