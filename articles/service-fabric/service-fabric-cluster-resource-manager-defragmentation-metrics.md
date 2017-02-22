---
title: "Azure Service Fabric 中度量的重組 | Microsoft Docs"
description: "使用重組或封裝作為 Service Fabric 中度量策略的概觀"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 5ef6381f7d182c818171eca3e3d32a00bc30268e


---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>度量的重組和 Service Fabric 中的負載
Service Fabric 叢集資源管理員主要與分散負載方面的平衡有關 - 確定會平均使用叢集中的節點。 就故障情況下幸存而言，分散工作負載是最安全的配置，因為這可確保不會因為故障而使指定的工作負載損失慘重。 Service Fabric 叢集 Resource Manager 也支援不同的策略 (重組)。 重組通常表示，我們應該實際嘗試合併計量，而不是嘗試將計量的使用量分散到叢集中。 合併完全逆轉我們的一般策略 – 叢集資源管理員以加大偏差為目標，而不是將計量負載的平均標準偏差最小化。 但是我們為什麼要用這項策略？

嗯，如果您已經在叢集中的節點間平均分散負載，則您已耗用了一些節點必須提供的資源。 不過，某些工作負載會建立極龐大且嚴重耗用節點的服務。 在這些情況下，單一服務物件最後可能佔用節點 75% 到 95% 的資源。 大型工作負載不是問題。 叢集資源管理員會在建立服務時決定需要重組叢集，以騰出空間給這個大型工作負載。 不過，同時此工作負載必須等待在叢集中排程。

如果要通過許多服務和狀態，則大型工作負載會經過很常的時間才放入叢集中。 如果叢集中的其他工作負載很大，因而移動時間很久，可能就會發生這種情形。 Service Fabric 小組是模擬此案例來測量建立時間。 我們發現，如果服務夠大，而且叢集使用率很高，則建立這些大型服務會變慢。 為了解決這種情況，我們引進重組當作平衡策略。 我們發現，對於大型工作負載，特別是建立時間很重要的工作負載，重組確實有助於將新的工作負載放入叢集中排程。

您可以設定重組計量，讓叢集資源管理員主動嘗試將服務的負載壓縮至較少的節點。 這有助於確保 (幾乎) 永遠有空間容納更大型的服務。 如此可以在必要時快速建立這類服務。

大部分的人不需要重組。 服務通常很小，因此在叢集中不難找到空間給它們。 不過，如果您有大型的服務且需要建速建立 (且願意接受其他利弊取捨)，則重組策略就適合您。

有什麼利弊取捨？ 重組主要是可放大故障的影響力 (因為在故障節點上執行的服務更多)。 此外，重組可確保叢集中的某些資源在等待工作負載排程時可供使用。

下圖提供兩個不同叢集的視覺表示法，其中一個已重組，另一個未重組。 在平衡情況下，請注意放置其中一個最大服務物件所需的移動次數。 對照已重組的叢集，其中，大型工作負載可立即放在節點&4; 或&5;。

<center>
![比較平衡和重組叢集][Image1]
</center>

## <a name="defragmentation-pros-and-cons"></a>重組的優缺點
因此，有哪些其他概念性的代價？ 我們建議先徹底測量您的工作負載，然後再開啟重組計量。 以下是要考慮事項的一覽表︰

| 重組優點 | 重組缺點 |
| --- | --- |
| 能夠更快速建立大型服務 |將負載集中到較少數的節點，提高爭用 |
| 在建立期間啟用較低的資料移動 |失敗會影響更多服務，並導致更多流失 |
| 能夠豐富描述需求和空間的回收 |較複雜的整體資源管理組態 |

您可以在相同叢集中混用重組計量和一般計量。 叢集資源管理員會嘗試儘可能合併重組計量，而分散其他計量。 如果沒有服務共用這些計量，則結果會很理想。 確切結果取決於與重組計量數目相比較的平衡計量數目、它們的重疊數目、權數、目前的負載等因素。 需要實驗來判斷所需的確切組態。

## <a name="configuring-defragmentation-metrics"></a>設定重組度量
設定重組計量是叢集中的全域決策，而您可以選取個別的計量進行重組︰

ClusterManifest.xml：

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

獨立部署透過 ClusterConfig.json，Azure 託管叢集透過 Template.json：

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Disk",
          "value": "true"
      },
      {
          "name": "CPU",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>後續步驟
* 叢集資源管理員有許多描述叢集的選項。 若要深入了解這些選項，請參閱關於[描述 Service Fabric 叢集](service-fabric-cluster-resource-manager-cluster-description.md)一文
* 度量是 Service Fabric 叢集資源管理員管理叢集中的耗用量和容量的方式。 若要深入了解計量及其設定方式，請參閱[這篇文章](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png



<!--HONumber=Feb17_HO3-->


