---
title: "Service Fabric 叢集 Resource Manager - 應用程式群組 | Microsoft Docs"
description: "Service Fabric 叢集 Resource Manager 中應用程式群組功能的概觀"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: eef9cbca0479801cbac53ae1ae5713d4dac6a4bf
ms.openlocfilehash: 57525cd41d518b12f3f465d5dea7247659e3c8b6
ms.lasthandoff: 02/16/2017


---
# <a name="introduction-to-application-groups"></a>應用程式群組簡介
Service Fabric 的叢集資源管理員通常會將負載平均分配到整個叢集 (透過[計量](service-fabric-cluster-resource-manager-metrics.md)表示)，以管理叢集資源。 Service Fabric 也透過[容量](service-fabric-cluster-resource-manager-cluster-description.md)的概念來管理管理叢集的節點容量及整個叢集的容量。 計量和容量很適用於許多工作負載，但過度使用不同 Service Fabric 應用程式執行個體的模式，有時會引起其他需求。 其他需求通常是：

* 能夠在叢集中保留容量給應用程式執行個體的服務
* 能夠限制執行應用程式內各項服務的節點總數
* 定義應用程式執行個體本身的容量，以限制其中的服務的資源總耗用量

為了符合這些需求，Service Fabric 叢集資源管理員支援應用程式群組。

## <a name="managing-application-capacity"></a>管理應用程式容量
應用程式容量可以用來限制應用程式跨越的節點數目。 還可限制該應用程式的服務在個別節點上的計量負載和總負載。 它也可以用來保留應用程式的叢集中的資源。

您可以在建立新的應用程式時設定應用程式容量，也可以針對現有的應用程式更新應用程式容量。

### <a name="limiting-the-maximum-number-of-nodes"></a>限制節點數目上限
應用程式容量的最簡單使用案例，是在需要將應用程式具現化限制為特定的節點數目上限時。 如果未指定應用程式容量，Service Fabric 叢集資源管理員會根據一般規則 (平衡或重組) 建立和放置服務。

下圖顯示已定義和未定義節點數目上限的應用程式執行個體。 不保證哪些服務的哪些複本或執行個體會放在一起，或會使用哪些特定的節點。

<center>
![定義節點數目上限的應用程式執行個體][Image1]
</center>

在左邊的範例中，應用程式沒有應用程式容量設定，而且它有三項服務。 叢集資源管理員已將所有複本分散到六個可用的節點，在叢集中達到最佳平衡狀態。 在右邊的範例中，我們看到相同的應用程式限制為三個節點。

控制此行為的參數稱為 MaximumNodes。 您可以在應用程式建立期間設定這個參數，也可以針對執行中的應用程式執行個體更新這個參數。

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

## <a name="application-metrics-load-and-capacity"></a>應用程式度量、負載和容量
應用程式群組也可讓您定義與特定應用程式執行個體相關聯的計量，以及應用程式在這些計量中的容量。 這可讓您追蹤、保留及限制該應用程式執行個體內各項服務的資源耗用量。

每一個應用程式計量可以設定兩個值︰

* **應用程式容量總計** – 這項設定代表應用程式在特定計量中的容量總計。 如果在這個應用程式執行個體內建立任何新的服務會導致總負載超過此值，則叢集資源管理員不允許建立這些服務。 例如，假設應用程式執行個體的容量為 10，而且已有五個負載。 在此情況下，不允許建立預設負載總計為 10 的服務。
* **節點容量上限** – 這項設定指定在單一節點上，應用程式內之服務複本的負載總計上限。 如果節點上的負載總計超過這個容量，叢集資源管理員會嘗試將複本移至其他節點，以遵守容量條件約束。

## <a name="reserving-capacity"></a>保留容量
應用程式群組的另一個常見用途是確保叢集內的資源保留給指定的應用程式執行個體。 即使應用程式執行個體內尚無任何服務，或尚未消耗資源，也是如此。 讓我們看一下這如何運作。

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>指定節點和資源保留的數目下限
保留應用程式執行個體的資源需要指定額外參數：MinimumNodes 和 NodeReservationCapacity

* **MinimumNodes** - 就像指定可執行應用程式內各項服務的節點數目上限，您也可以指定數目下限。 這項設定可定義應該保留資源的節點數目，以便在建立應用程式執行個體時保證叢集內的容量。
* **NodeReservationCapacity** - 您可以針對應用程式內的每個度量定義 NodeReservationCapacity。 針對應用程式內各項服務的任何複本或執行個體所在的任何節點，這項設定可定義該節點上保留給應用程式的計量負載數量。

讓我們看看容量保留的範例︰

<center>
![定義保留容量的應用程式執行個體][Image2]
</center>

在左邊的範例中，應用程式並沒有定義任何應用程式容量。 叢集資源管理員會根據一般規則來平衡一切事物。

在右邊範例中，假設已使用下列設定建立應用程式︰

* MinimumNodes 設為二
* MaximumNodes 設為三
* 應用程式計量定義下列值
  * NodeReservationCapacity 為 20
  * MaximumNodeCapacity 為 50
  * TotalApplicationCapacity 為 100

Service Fabric 保留兩個節點上的容量給藍色的應用程式，不允許叢集中其他應用程式執行個體的服務使用該容量。 此保留的應用程式容量是取自於該節點上和叢集內剩餘的容量。

以保留容量建立應用程式時，叢集資源管理員保留的容量等於 MinimumNodes * NodeReservationCapacity (針對每個計量)。 但是，特定節點上至少要放置一個複本，才會保留容量。 這後來的保留做法能夠更有彈性和更充分利用資源，因為只有在需要時才於節點上保留資源。

## <a name="obtaining-the-application-load-information"></a>取得應用程式負載資訊
對於具有定義的應用程式容量的每個應用程式，您可以取得其服務的複本報告的彙總負載的相關資訊。

例如，可以使用下列 PowerShell Cmdlet 擷取負載：

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

ApplicationLoad 查詢會傳回應用程式指定之「應用程式容量」的基本資訊。 這項資訊包含「節點下限」和「節點上限」資訊，以及應用程式目前佔用的數目。 也包含每個應用程式負載計量的相關資訊，包括︰

* 度量名稱：度量的名稱。
* 保留容量：針對此應用程式保留在叢集中的叢集容量。
* 應用程式負載︰此應用程式的子複本的總負載。
* 應用程式容量︰許可的應用程式負載值上限。

## <a name="removing-application-capacity"></a>移除應用程式容量
一旦針對應用程式設定應用程式容量參數，它們可以使用更新的應用程式 API 或 PowerShell Cmdlet 來移除。 例如：

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

此命令會從應用程式中移除所有「應用程式容量」參數。 此命令會立即生效。 此命令完成後，叢集資源管理員會還原為使用預設行為來管理應用程式。 您可以透過 Update-ServiceFabricApplication 再次指定「應用程式容量」參數。

### <a name="restrictions-on-application-capacity"></a>應用程式容量的限制
應用程式容量參數有數個限制必須遵守。 如果發生驗證錯誤，則會拒絕建立或更新應用程式並傳回錯誤，或維持不變。

* 所有整數參數必須為非負數。
* MinimumNodes 不能大於 MaximumNodes。
* 如果已定義負載度量的容量，則它們必須遵守下列規則︰
  * 節點保留容量不能大於節點容量上限。 例如，您不能在節點上將 “CPU” 計量的容量限制為兩個單位，卻嘗試在每個節點上保留三個單位。
  * 如果已指定 MaximumNodes，則 MaximumNodes 和節點容量上限的乘積不能大於應用程式容量總計。 例如，假設負載計量 “CPU” 的「節點容量上限」設定為八。 另外也假設您將「節點上限」設定為&10;。 在此情況下，此負載計量的「應用程式容量總計」必須大於 80。

在應用程式建立 (用戶端上) 和應用程式更新 (伺服器端上) 期間都會強制執行限制。

## <a name="how-not-to-use-application-capacity"></a>如何不使用應用程式容量
* 請勿嘗試使用「應用程式群組」功能將應用程式限制到「特定」的節點子集。 換句話說，您可以指定最多在五個節點上執行應用程式，但無法指定叢集中的哪五個特定節點。 您可以使用服務的放置條件約束，將應用程式限制到特定節點。
* 請勿使用「應用程式容量」來確保相同應用程式的兩個服務放在一起。 根據特定的需求而定，您可以使用同質性或放置約束限制，以確保在相同節點上執行服務。

## <a name="next-steps"></a>後續步驟
* 如需可用來設定服務的其他選項的詳細資訊，請參閱有關其他可用之叢集資源管理員設定的主題：[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)
* 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於 [平衡負載](service-fabric-cluster-resource-manager-balancing.md)
* 從頭開始，並 [取得 Service Fabric 叢集 Resource Manager 的簡介](service-fabric-cluster-resource-manager-introduction.md)
* 如需度量通常如何運作的詳細資訊，請繼續閱讀 [Service Fabric 負載度量](service-fabric-cluster-resource-manager-metrics.md)
* 叢集資源管理員有許多描述叢集的選項。 若要深入了解這些選項，請參閱關於[描述 Service Fabric 叢集](service-fabric-cluster-resource-manager-cluster-description.md)一文

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

