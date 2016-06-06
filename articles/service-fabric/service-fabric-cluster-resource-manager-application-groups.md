<properties
   pageTitle="Service Fabric 叢集 Resource Manager - 應用程式群組 | Microsoft Azure"
   description="Service Fabric 叢集 Resource Manager 中應用程式群組功能的概觀"
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
   ms.date="05/20/2016"
   ms.author="masnider"/>

# 應用程式群組簡介
Service Fabric 的叢集 Resource Manager 通常會藉由將負載 (透過度量表示) 平均分配到整個叢集，來管理叢集資源。Service Fabric 也會管理叢集中節點的容量，以及透過容量的概念管理整個叢集。這很適合用於許多不同類型的工作負載，但大量使用不同 Service Fabric 應用程式執行個體的模式還有其他需求。其他需求通常是：

- 將應用程式執行個體的服務的容量保留在部分數量節點上的能力
- 限制應用程式內允許執行之指定服務集合的節點總數的能力
- 定義應用程式執行個體本身的容量，以限制其中的服務的資源總耗用量

為了符合這些需求，我們開發了對於我們稱為應用程式群組的支援。

## 管理應用程式容量
應用程式容量可以用來限制應用程式跨越的節點數目，以及在個別節點上應用程式的執行個體的度量負載總計。它也可以用來保留應用程式的叢集中的資源。

應用程式容量可以針對新的應用程式在建立時設定；它也可以針對已建立但未指定應用程式容量的現有應用程式更新。

### 限制節點數目上限
應用程式容量的最簡單使用案例，是在需要將應用程式具現化限制為特定的節點數目上限時。如果未指定應用程式容量，Service Fabric 叢集 Resource Manager 會根據一般規則 (平衡或重組) 具現化複本，這通常表示其服務會跨越叢集中的可用節點分配，或者重組是否在任意但較少數目的節點上開啟。

下圖顯示應用程式執行個體的潛在位置，該執行個體沒有定義的節點數目上限，然後顯示已設定節點數目上限的相同應用程式。請注意，不保證哪些服務的哪些複本或執行個體將會放在一起。

![應用程式執行個體定義節點數目上限][Image1]

在左邊的範例中，應用程式沒有應用程式容量設定，而且它有三項服務。CRM 做了邏輯決策，將所有複本分散到六個可用的節點以達到叢集中的最佳平衡。在右邊的範例中，我們會看到限制在三個節點上的相同應用程式，其中 Service Fabric CRM 已達成應用程式服務複本的最佳平衡。

控制此行為的參數稱為 MaximumNodes。這個參數可以在應用程式建立期間設定，或針對已在執行中的應用程式執行個體更新，若為後者，Service Fabric CRM 會將應用程式的服務的複本限制為定義的節點數目上限。

Powershell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## 應用程式度量、負載和容量
應用程式群組也可讓您定義與特定應用程式執行個體相關聯的度量，以及這些度量相關的應用程式的容量。因此舉例來說，您可以定義您想要建立的任意數量服務

對於每個度量，有 2 個值可以設定來說明該應用程式執行個體的容量︰

-	應用程式容量總計 – 代表特定度量的應用程式容量總計。Service Fabric CRM 會嘗試將此應用程式的服務的度量負載加總限制為指定值，此外，如果應用程式的服務已耗用超過這個限制的負載，Service Fabric 叢集 Resource Manager 將不允許建立任何新的服務或資料分割，這會造成總負載超出此限制。
-	節點容量上限 – 指定單一節點上應用程式的服務複本的總負載上限。如果節點上的總負載超過這個容量，Service Fabric CRM 會嘗試將複本移至其他節點，以便遵守容量限制。

## 保留容量
應用程式群組的另一個常見用途是確保針對指定的應用程式執行個體保留叢集內的資源，即使應用程式執行個體尚未在其內具有服務，或者它們尚未耗用資源。讓我們來看一下如何運作。

### 指定節點和資源保留的數目下限
保留應用程式執行個體的資源需要指定額外參數：MinimumNodes 和 NodeReservationCapacity

- MinimumNodes - 就像指定應用程式內的服務可以在上面執行的目標節點數目上限，您也可以指定應用程式應該執行的節點數目下限。此設定有效地定義資源應該保留的節點數目下限，在建立應用程式執行個體時保證叢集中的容量。
- NodeReservationCapacity - NodeReservationCapacity 可以針對應用程式中的每個度量定義。這會定義針對任何節點上的應用程式保留的度量負載，在該節點中放置服務的任何複本或執行個體。

讓我們看看容量保留的範例︰

![應用程式執行個體定義保留容量][Image2]

在左邊的範例中，應用程式並沒有定義任何應用程式容量。Service Fabric 叢集 Resource Manager 會平衡應用程式的子服務複本和執行個體，以及來自其他服務 (在應用程式外部) 的複本和執行個體，以確保叢集中的平衡。

在右邊的範例中，讓我們假設已建立的應用程式的 MinimumNodes 設為 2、MaximumNodes 設為 3，且應用程式度量定義為保留 20、節點容量上限 50 和應用程式容量總計 100，Service Fabric 將會為藍色應用程式在兩個節點上保留容量，並且不允許叢集中的其他複本使用該容量。此保留的應用程式容量會被視為針對剩餘的叢集容量耗用和計數。

當建立的應用程式具有保留時，叢集 Resource Manager 會在叢集中保留等於 MinimumNodes * NodeReservationCapacity 的容量，但是不會保留特定節點上的容量，直到建立和放置應用程式的服務的複本。這樣可以有彈性，因為只有在新複本建立時針對它們選擇節點。當至少一個複本放置在該特定節點上時會保留容量。

## 取得應用程式負載資訊
對於具有定義的應用程式容量的每個應用程式，您可以取得其服務的複本報告的彙總負載的相關資訊。Service Fabric 會針對此目的提供 PowerShell 和 Managed API 查詢。

例如，可以使用下列 PowerShell Cmdlet 擷取負載：

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

此查詢的輸出包含已針對應用程式指定的應用程式容量的基本資訊，例如節點下限和節點上限。另外也有應用程式目前使用的節點數目的相關資訊。因此，對於每個負載度量，會有以下相關資訊︰
- 度量名稱：度量的名稱。
-	保留容量：針對此應用程式保留在叢集中的叢集容量。
-	應用程式負載︰此應用程式的子複本的總負載。
-	應用程式容量︰許可的應用程式負載值上限。

## 移除應用程式容量
一旦針對應用程式設定應用程式容量參數，它們可以使用更新的應用程式 API 或 PowerShell Cmdlet 來移除。例如：

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

這個命令會從應用程式移除所有應用程式容量參數，Service Fabric 叢集 Resource Manager 會開始將此應用程式視為叢集中未定義這些參數的任何其他應用程式。此命令的效果會立即生效，叢集 Resource Manager 將會刪除此應用程式的所有應用程式容量參數；再次指定它們需要使用適當的參數呼叫更新應用程式 API。

## 應用程式容量的限制
應用程式容量參數有數個限制必須遵守。發生驗證錯誤時，建立或更新應用程式將會遭到拒絕並產生錯誤。所有整數參數必須為非負數。此外，對於個別參數，限制如下︰

-	MinimumNodes 不能大於 MaximumNodes。
-	如果已定義負載度量的容量，則它們必須遵守下列規則︰
  - 節點保留容量不能大於節點容量上限。例如，您無法將節點上的度量 “CPU” 的容量限制為 2 個單位，卻嘗試在每個節點上保留 3 個單位。
  - 如果已指定 MaximumNodes，則 MaximumNodes 和節點容量上限的乘積不能大於應用程式容量總計。例如，如果您將負載度量 “CPU” 的節點容量上限設為 8，並且將節點上限設為 10，則此負載度量的應用程式容量總計必須大於 80。

在應用程式建立 (用戶端上) 和應用程式更新 (伺服器端上) 期間都會強制執行限制。在建立期間，這是違反需求的明顯範例，因為 MaximumNodes < MinimumNodes，甚至在要求傳送至 Service Fabric 叢集之前，用戶端中的命令就會失敗：

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

無效的更新範例如下所示。如果我們採用現有的應用程式，並且將節點上限更新為某個值，則會傳遞更新︰

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

接下來，我們可以嘗試更新節點下限︰

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

用戶端並沒有足夠的應用程式內容，因此它會允許將更新傳遞至 Service Fabric 叢集。不過，在叢集中，Service Fabric 會驗證新的參數與現有的參數，而且會因為節點下限的值大於節點上限的值而使得更新作業失敗。在此情況下，應用程式容量參數將維持不變。

這些限制會按照順序放置，讓叢集 Resource Manager 能夠為應用程式的服務複本提供最佳放置。

## 如何不使用應用程式容量

-	請勿使用應用程式容量將應用程式限制為特定的節點子集：雖然 Service Fabric 會確保對於已指定應用程式容量的每個應用程式遵守節點上限，使用者無法決定它具現化所在的節點。這可以使用適用於服務的放置條件約束來達成。
-	請勿使用應用程式容量來確保相同應用程式的兩個服務一律放在彼此左右。這可透過使用服務之間的同質關聯性來達成，同質可以限制為僅限應該實際放在一起的服務。

## 後續步驟
- 如需可用來設定服務的其他選項的詳細資訊，請查看[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)中提供的其他叢集 Resource Manager 組態的相關主題
- 若要了解叢集 Resource Manager 如何管理並平衡叢集中的負載，請查看關於[平衡負載](service-fabric-cluster-resource-manager-balancing.md)的文章
- 從頭開始，並[取得 Service Fabric 叢集 Resource Manager 的簡介](service-fabric-cluster-resource-manager-introduction.md)
- 如需度量通常如何運作的詳細資訊，請繼續閱讀 [Service Fabric 負載度量](service-fabric-cluster-resource-manager-metrics.md)
- 叢集資源管理員有許多描述叢集的選項。若要深入了解這些選項，請查看關於[描述 Service Fabric 叢集](service-fabric-cluster-resource-manager-cluster-description.md)的這篇文章


[Image1]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

<!---HONumber=AcomDC_0525_2016-->