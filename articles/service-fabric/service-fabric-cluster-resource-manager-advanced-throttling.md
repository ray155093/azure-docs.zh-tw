---
title: Service Fabric 叢集資源管理員中的節流 | Microsoft Docs
description: 了解如何設定 Service Fabric 叢集 Resource Manager 提供的節流。
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# 節流 Service Fabric 叢集資源管理員的行為
即使您已經正確設定叢集資源管理員，也可以中斷叢集。例如，可能會同時發生節點或容錯網域失敗 - 升級時如果發生的話會怎麼樣？ 資源管理員將盡力修正這一切，但像這種時候，您可能要考慮設定一個停止機制，讓叢集本身有機會能夠穩定下來 (即將返回的節點會返回、網路條件可使它們自行修復、可部署已更正的位元)。為了協助達成這幾種情況，Service Fabric 叢集資源管理員提供了數個節流。請注意，這些節流都是干擾性工具，通常不應使用，除非已進行一些謹慎的數學運算來解決實際上可在叢集中完成的平行工作量，以及經常需要回應這些種類的 (啊哈) 未經規劃且大型的重新設定事件 (也稱為：「非常糟糕的日子」)。

一般而言，我們建議您透過其他選項來避免非常糟糕的日子 (例如，一般程式碼更新，以及避免過度排程要啟動的叢集)，而不是為您的叢集設定節流，以防止它在嘗試修復其本身時使用資源)。節流確實有預設值，我們也已經透過經驗發現預設值適合使用，但是您或許應該看一下並依據您預期的實際負載來調整它們。雖然最佳作法是不要過度限制或載入叢集，但您可能判斷有些情況 (直到您解決它們為止) 需要您在適當的地方擁有數個節流，即使這表示叢集需要較長的時間才能穩定也一樣。

## 設定節流
以下是預設包含的節流：

* GlobalMovementThrottleThreshold – 這會控制一段時間內叢集中移動的總數 (已定義為 GlobalMovementThrottleCountingInterval，以秒為單位的值)
* MovementPerPartitionThrottleThreshold – 這會控制一段時間內針對任何服務分割區的移動總數 (MovementPerPartitionThrottleCountingInterval，以秒為單位的值)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

請注意，在大部分情況下，我們看到客戶使用這些節流的原因是因為他們已經處於資源受限的環境 (例如對個別節點的網路頻寬受到限制，或節點上已經置入不符合平行複本建置需求的磁碟)，這表示此類型作業不會成功或執行速度很慢。在這些情況下，客戶已充分了解可能需要延長叢集到達穩定狀態所需花費的時間量，包括了解在為其設定節流時，它們最終會以較低的整體可靠性來執行。

## 後續步驟
* 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於[平衡負載](service-fabric-cluster-resource-manager-balancing.md)的文章
* 叢集資源管理員有許多描述叢集的選項。若要深入了解這些選項，請查看關於[描述 Service Fabric 叢集](service-fabric-cluster-resource-manager-cluster-description.md)的這篇文章

<!---HONumber=AcomDC_0824_2016-->