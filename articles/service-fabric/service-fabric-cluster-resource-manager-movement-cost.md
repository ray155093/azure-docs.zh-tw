---
title: "Service Fabric 叢集 Resource Manager：移動成本 | Microsoft Docs"
description: "Service Fabric 服務的移動成本概觀"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: f85365a36aea39b4179805e728c7ddafa140f08b
ms.lasthandoff: 01/07/2017


---
# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>影響叢集資源管理員選擇的服務移動成本
「Service Fabric 叢集資源管理員」在嘗試判斷要對叢集進行哪些變更時會考量一個重要因素，就是達成該解決方案所需的整體成本。 「成本」的概念與可達成的平衡多寡之間會有權衡取捨。

移動服務執行個體或是複本會花費最少的 CPU 時間和網路頻寬。 就具狀態服務而言，它也會在您需要於關閉舊複本之前建立狀態複本的磁碟和記憶體中產生一些空間成本。 顯然您想要將所有 Azure Service Fabric 叢集資源管理員帶來的任何解決方案的成本降到最低。 但也不想忽略可大幅改善叢集中資源配置的解決方案。

「叢集資源管理員」有兩種計算及限制成本的方式，即使在它依據其他目標來嘗試管理叢集時亦是如此。 第一種方式是當叢集資源管理員為叢集規劃新的配置時，它會計算叢集所進行的每個移動。 如果產生兩個平衡值 (分數) 幾乎相同的解決方案，就會採用成本最低 (移動總數) 的那一個。

此策略效果不錯。 但是若使用預設或靜態負載時，不太可能在任何複雜的系統中所有的移動都相等。 有些可能會昂貴許多。

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>變更複本的移動成本和考量因素
如同使用報告負載 (「叢集資源管理員」的另一個功能) 時一樣，服務可以隨時動態地自我報告移動所需的成本。

程式碼：

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

建立服務時，也可以指定預設移動成本。

MoveCost 有四個層級：零、低、中和高。 除了零之外，MoveCosts 彼此具有相對性。 零移動成本表示移動是免費的，因此不應計入解決方案的分數。 將移動成本設定為 [高]，並「不」保證複本會待在一個地方。

<center>
![移動成本作為選取要移動之複本的因素][Image1]
</center>

MoveCost 可協助您在達成對等的平衡時，尋找整體導致最少中斷且最容易達成的解決方案。 服務的成本概念可相對於許多事項。 計算您的移動成本時最常見的因素為：

* 服務必須移動的狀態或資料量。
* 用戶端中斷連線的成本。 移動主要複本的成本通常高於移動次要複本的成本。
* 中斷執行中作業的成本。 某些資料存放區層級的作業，或是執行以回應用戶端呼叫的作業，它們的成本都很高。 在某個特定點之後，除非必要否則您不會想要停止它們。 因此，在作業持續期間，您可以提高此服務物件的移動成本以降低其移動的可能性。 當作業完成之後，您可以將成本設定回正常。

## <a name="next-steps"></a>後續步驟
* Service Fabric 叢集資源管理員會使用度量來管理叢集中的耗用量和容量。 若要深入了解度量及設定度量的方式，請參閱 [在 Service Fabric 中使用度量管理資源耗用量和負載](service-fabric-cluster-resource-manager-metrics.md)。
* 若要深入了解叢集資源管理員如何在叢集中進行管理和負載平衡，請查看 [平衡 Service Fabric 叢集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

