---
title: "Service Fabric 叢集 Resource Manager 中的節流 | Microsoft Docs"
description: "了解如何設定 Service Fabric 叢集 Resource Manager 提供的節流。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a
ms.lasthandoff: 01/07/2017


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>節流 Service Fabric 叢集資源管理員的行為
即使您已經正確設定叢集資源管理員，也可以中斷叢集。 例如，可能會同時發生節點或容錯網域失敗 - 升級時如果發生的話會怎麼樣？ 叢集資源管理員會嘗試修復一切問題，但可能在叢集中造成混亂。 節流有助於提供最後防線，讓叢集利用資源來穩定本身 - 回復節點、修復網路磁碟分割、部署已更正的程式碼。

為了協助達成這幾種情況，Service Fabric 叢集資源管理員包含幾種節流。 這些節流是相當強大的工具。 除非仔細計算過叢集可以平行執行的工作量，否則不應該變更這些設定的預設值。

Service Fabric 小組從經驗上發現節流的預設值都沒問題。 如果需要變更，應該根據您預期的實際負載來調整。 您可能認為需要備妥一些節流，但這表示叢集在重大情況下需要更長的時間才會穩定。

## <a name="configuring-the-throttles"></a>設定節流
以下是預設包含的節流：

* GlobalMovementThrottleThreshold – 這項設定會控制一段時間內叢集中移動的總數 (已定義為 GlobalMovementThrottleCountingInterval，以秒為單位的值)
* MovementPerPartitionThrottleThreshold – 這項設定會控制一段時間內針對任何服務分割區的移動總數 (MovementPerPartitionThrottleCountingInterval，以秒為單位的值)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

獨立部署透過 ClusterConfig.json，Azure 託管叢集透過 Template.json：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

在大部分情況下，我們看到客戶會使用這些節流，這是因為他們都已處於資源受限的環境。 例如，這種環境可能是個別節點的網路頻寬有限，或磁碟由於輸送量限制而無法同時建置許多複本。 這幾種限制表示為了回應失敗而觸發的作業不會成功，或會變慢，即使沒有節流也一樣。 在這些情況下，客戶知道叢集會花費很長的時間才能達到穩定狀態。 客戶也了解進行節流時，最後可能會在整體可靠性降低的情況下運作。

## <a name="next-steps"></a>後續步驟
* 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於 [平衡負載](service-fabric-cluster-resource-manager-balancing.md)
* 叢集資源管理員有許多描述叢集的選項。 若要深入了解這些選項，請參閱關於[描述 Service Fabric 叢集](service-fabric-cluster-resource-manager-cluster-description.md)一文

