---
title: "指定 Azure 微服務中的計量和放置設定 | Microsoft Docs"
description: "藉由指定計量、放置條件約束及其他放置原則來描述 Service Fabric 服務。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c1df3b77f3fa621a60d6ab73f6dc2c24abbc3366


---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>設定 Service Fabric 服務的叢集資源管理員設定
「Service Fabric 叢集資源管理員」可讓您細微地控制可管控每個個別具名服務的規則。 每個具名服務執行個體都可以指定規則來規定它在叢集中的配置方式。 每個具名服務還可以定義一組它想要報告的計量，包括它們對該服務的重要程度。 設定服務可細分成三個不同的工作︰

1. 設定放置條件約束
2. 設定計量
3. 設定進階放置原則及其他規則 (較不常見)

## <a name="placement-constraints"></a>放置條件約束
放置條件約束可用來控制服務實際可在叢集中的哪些節點上執行。 通常特定的具名服務執行個體或某個指定類型的所有服務會受到限制，只能在特定類型的節點上執行。 這表示，放置條件約束是可擴充的 - 您可以依據節點類型定義任何屬性集，然後在建立服務時為它們選取條件約束。 放置條件約束也會在服務存留時間內動態更新，讓您能夠回應叢集中的變更。 指定節點的屬性也可以在叢集中動態更新。 可以在 [這篇文章](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)

## <a name="metrics"></a>度量
計量是指定的具名服務執行個體所需的一組資源。 服務的計量組態包括該服務的每個具狀態複本或無狀態執行個體對該資源的預設耗用量。 計量也包含一個權數，用來指出平衡該計量對服務來說有多重要 (以因應需要取捨的情況)。

## <a name="other-placement-rules"></a>其他放置規則
還有其他類型的放置規則，這些規則適用於地理區域分散的叢集或其他較少見的案例。 其他放置規則是透過「相互關聯」或「原則」來設定。

## <a name="next-steps"></a>後續步驟
* 度量是 Service Fabric 叢集資源管理員管理叢集中的耗用量和容量的方式。 若要深入了解度量及其設定方式，請參閱[這篇文章](service-fabric-cluster-resource-manager-metrics.md)
* 親和性是您可以針對服務設定的一種模式。 它並不常見，但如果您需要，可以參閱 [這裡](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* 有許多不同的放置規則可以在您的服務上設定以處理其他案例。 您可以在 [這裡](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
* 從頭開始，並 [取得 Service Fabric 叢集 Resource Manager 的簡介](service-fabric-cluster-resource-manager-introduction.md)
* 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於 [平衡負載](service-fabric-cluster-resource-manager-balancing.md)
* 「叢集資源管理員」有許多描述叢集的選項。 若要深入了解這些選項，請查看這篇關於[描述 Service Fabric 叢集](service-fabric-cluster-resource-manager-cluster-description.md)的文章



<!--HONumber=Jan17_HO4-->


