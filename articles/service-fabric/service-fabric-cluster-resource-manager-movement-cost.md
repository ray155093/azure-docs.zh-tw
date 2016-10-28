<properties
   pageTitle="Service Fabric 叢集資源管理員：移動成本 | Microsoft Azure"
   description="Service Fabric 服務的移動成本概觀"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# 影響叢集資源管理員選擇的服務移動成本
當我們在嘗試判斷要對叢集和解決方案的分數進行哪些變更時，要納入考量的一個重要因素是達成該解決方案的整體成本。

移動服務執行個體或是複本會花費最少的 CPU 時間和網路頻寬。而針對具狀態服務，它也會於您需要在關閉舊複本之前建立狀態複本的磁碟上，產生空間量的成本。顯然您想要將所有 Azure Service Fabric 叢集資源管理員帶來的任何解決方案的成本降到最低。但也不想忽略可大幅改善叢集中資源配置的解決方案。

叢集資源管理員有兩種方式可計算成本並限制成本，即使同時依據其他目標嘗試管理叢集也沒問題。第一種方式是當叢集資源管理員為叢集規劃新的配置時，它會計算叢集所進行的每個移動。有一個簡單的情況是，如果您最終取得整體平衡 (分數) 幾乎相同的兩個解決方案，就採用成本最低 (移動總數) 的那一個。

這非常實用。但是若使用預設或靜態負載時，不太可能在任何複雜的系統中所有的移動都相等。有些可能會昂貴許多。

## 變更複本的移動成本和考量因素
如同使用報告負載 (叢集資源管理員的另一個功能)，您可讓服務在特定的時間自我報告移動服務所需的成本。

程式碼：

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost 有四個層級：零、低、中和高。除了零之外，這些值彼此具有相對性。零表示移動複本沒有任何成本，且不應計入解決方案的分數。將您的移動成本設定為「高」，不保證複本不會移動，只是除非有移動的好理由，否則不會移動它。

![移動成本做為選取複本進行移動的因素][Image1]

MoveCost 可協助您在達成對等的平衡時，尋找整體導致最少中斷且最容易達成的解決方案。服務的成本概念可相對於許多事項。計算您的移動成本時最常見的因素為：

- 服務必須移動的狀態或資料量。
- 用戶端中斷連線的成本。移動主要複本的成本通常高於移動次要複本的成本。
- 中斷執行中作業的成本。某些資料存放區層級的作業，或是執行以回應用戶端呼叫的作業，它們的成本都很高。在某個特定點之後，除非必要否則您不會想要停止它們。因此，在作業持續期間，您可以提高成本以降低服務複本或執行個體移動的可能性。當作業完成之後，您可以讓它回復正常。

## 後續步驟
- Service Fabric 叢集資源管理員會使用度量來管理叢集中的耗用量和容量。若要深入了解度量及設定度量的方式，請參閱[在 Service Fabric 中使用度量管理資源耗用量和負載](service-fabric-cluster-resource-manager-metrics.md)。
- 若要深入了解叢集資源管理員如何在叢集中進行管理和負載平衡，請查看[平衡 Service Fabric 叢集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0831_2016-->