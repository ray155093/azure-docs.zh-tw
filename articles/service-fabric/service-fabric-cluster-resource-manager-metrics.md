---
title: "使用計量來管理 Azure 微服務負載 | Microsoft Docs"
description: "了解如何在 Service Fabric 中設定及使用計量，以管理服務資源耗用量。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c0e5418574920495bf277f4127b97ff5b07b56ef


---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>在 Service Fabric 中使用度量管理資源耗用量和負載
度量是 Service Fabric 中您的服務所關切的資源的通用詞彙，且是由叢集中的節點提供。 一般而言，計量就是任何您想要管理以處理您服務效能的項目。

像記憶體、磁碟 及 CPU 使用率等項目都是計量的範例。 這些是實體度量，也就是對應至節點上需要管理之實體資源的資源。 計量也可以是 (且通常是) 邏輯計量。 邏輯計量的範例是 “MyWorkQueueDepth”、"MessagesToProcess" 或 "TotalRecords" 之類的項目。 邏輯計量是由應用程式所定義且與某個實體資源耗用量對應，但應用程式實際上並不了解如何測量它。 這種情況很常見。 我們看到使用者使用的度量大部分是邏輯度量。 最常見的原因是現今許多服務都是以 Managed 程式碼來撰寫的。 Managed 程式碼意謂著很難從主機處理序內測量這類程式碼，也很難回報單一服務物件的實體資源耗用量。 測量及回報您自己的計量有點複雜，這也是為什麼我們會提供一些現成的預設計量。

## <a name="default-metrics"></a>預設度量
假設您想要開始使用，但不知道將會取用哪些資源，或甚至哪些資源對您很重要。 所以您進行實作，然後建立您的服務，但未指定任何度量。 沒關係！ 「Service Fabric 叢集資源管理員」會為您挑選一些簡單的計量。 現今我們在您未指定任何計量時所使用的預設計量稱為 PrimaryCount、ReplicaCount 及 Count。 下表顯示這些計量各有多少負載與每個服務物件有關聯︰

| 度量 | 無狀態執行個體負載 | 具狀態次要負載 | 具狀態主要負載 |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Count |1 |1 |1 |

好了，透過這些預設度量，您得到什麼？ 結果就是，針對基本工作負載，您得到適當的工作分配。 在下列範例中，讓我們看看建立兩個服務時會發生什麼情況。 第一個是帶有三個資料分割且目標複本集大小為三的具狀態服務。 第二個是帶有一個資料分割且執行個體計數為三的無狀態服務：

<center>
![使用預設計量的叢集配置][Image1]
</center>

以下是您將看到的情況：

* 具狀態服務的主要複本並未堆疊於單一節點上
* 相同資料分割的複本不在相同節點上
* 主要複本與次要複本的總數分佈在叢集中
* 服務物件的總數平均配置於每個節點

很好！

在您開始執行大量實際工作負載之前，此方式都能完美運作︰您選擇的資料分割配置導致所有資料分割完全平均使用的可能性為何？ 所指定服務的負載在一段時間內保持不變或只是現在相同的機率為何？

實際上，您可以只使用預設計量來執行，但這麼做通常意謂著您的叢集使用率低於您的期望。 這是因為預設計量報告不是調適性的，而是假設所有項目都相等。 在最糟的情況下，使用預設值也可能導致節點被過度排定，而造成效能問題。 使用自訂度量和動態負載報告 ，我們可以處理得比較好 (我們接下將會探討)。 就任何重要的工作負載而言，所有服務永遠保持相等的可能性很低。 如果您對充分利用叢集並避免效能問題感興趣，您就會想要開始了解自訂計量。

## <a name="custom-metrics"></a>自訂度量
當您建立服務時，可以為每個具名服務執行個體設定計量。

任何計量都有一些描述它的屬性：名稱、預設負載及權數。

* 計量名稱：計量的名稱。 從「資源管理員」的觀點來看，計量名稱是叢集內計量的唯一識別碼。
* 預設負載：預設負載會依據服務是無狀態或具狀態服務來以不同方式表示。
  * 就無狀態服務而言，每個計量都有名為 DefaultLoad 的單一屬性
  * 針對具狀態服務，您需定義：
    * PrimaryDefaultLoad：當此計量作為「主要」複本時，此服務將取用的預設量
    * SecondaryDefaultLoad：當此計量作為「次要」複本時，此服務將取用的預設量
* Weight︰計量權數定義了就此服務而言，此計量相對於其他計量的重要程度。

如果您定義自訂計量並同時也想要使用預設計量，您將必須明確地加回預設計量。 這是因為您想要釐清預設計量與自訂計量之間的關聯性。 例如，相較於「主要」分佈，您或許比較在意的是「記憶體」耗用量或 WorkQueueDepth。

### <a name="defining-metrics-for-your-service---an-example"></a>為您的服務定義計量 - 範例
假設您想要設定一個回報名為 “MemoryInMb” 之計量的服務，但同時也想要使用預設計量。 此外，假設您也已經完成一些測量，而知道該服務的主要複本通常佔用 20 單位的 "MemoryInMb"，而次要複本則佔用 5 單位。 您知道就管理這項特定服務的效能而言，「記憶體」是最重要的計量，但您仍希望主要複本達到平衡。 「平衡主要複本」是一個不錯的想法，這可讓某個節點或容錯網域的遺失不致於影響與其在一起的大部分複本。 除了這些調整之外，您還想要使用預設計量。

以下是您以該計量組態建立服務時將撰寫的程式碼：

程式碼：

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(提醒您，如果您只想要使用預設度量，您就完全不必碰觸度量集合，或在建立服務時執行任何特殊動作。)

既然我們已經獲得簡介並看過範例，讓我們更仔細地瀏覽每個這些設定，並探討您將獲得的行為。

## <a name="load"></a>載入
定義計量的整個重點在於代表某個負載。 「負載」係指特定節點上的某個服務執行個體或複本取用的特定計量數量。 您可以在建立服務時設定預期的負載、在建立服務之後更新該負載、依據每個服務物件回報該負載，或執行上述所有作業。

## <a name="default-load"></a>預設負載
預設負載係指此服務的每個服務物件 (執行個體或複本) 預設取用的度量數量。 就較簡單的服務而言，預設負載係指永不更新且在服務存留期使用的靜態定義。 預設負載非常適用於簡單的容量規劃案例，其中特定數量的資源是供不同的工作負載專用。

「叢集資源管理員」允許具狀態服務為其「主要」複本和「次要」複本指定不同的預設負載，而無狀態服務則只能指定一個值。 就具狀態服務而言，主要和次要複本的預設負載通常不同，因為複本在每個角色中是執行不同類型的工作。 例如，主要複本通常會同時為讀取和寫入 (以及大多數運算負擔) 提供服務，而次要複本則不會。 主要複本的預設負載預期會高於次要複本的預設負載，但實際數字應該會取決於您自己的測量。

## <a name="dynamic-load"></a>動態負載
假設您已經執行服務達一段時間。 藉由一些監視，您已注意到：

1. 所指定服務的某些資料分割或執行個體比其他資料分割或執行個體耗用更多資源
2. 某些服務的負載會隨著時間改變。

有很多原因可能造成這些類型的負載變動。 服務或資料分割可能與特定的客戶關聯，或它們可能是對應隨著日程進展而發生變化的工作負載。 不論是哪種原因，都沒有任何單一數字可供您用來作為預設負載。 您為預設負載挑選的任何值有時都會出錯。 這是一個問題，因為不正確的預設負載會導致「叢集資源管理員」為服務配置的資源不是太多就是不足。 結果就是即使「叢集資源管理員」認為叢集已達平衡狀態，但您的節點卻有使用率太高或太低的情況。 由於預設負載提供某些資訊，因此它們仍有其實用性，但大部分時候，它們並無法反映實際工作負載的完整面貌。 這就是為什麼「叢集資源管理員」允許每個服務物件在執行階段期間更新自己的負載。 這稱為動態負載報告功能。

動態負載報告可讓複本或執行個體在其存留期中調整其配置/回報的計量負載。 閒置且未執行任何工作的服務複本或執行個體通常會回報使用少量的指定計量。 忙碌的複本或執行個體則會回報使用較多的資源。

依據每一複本或執行個體進行回報可讓「叢集資源管理員」重新組織叢集內的個別服務物件，以確保服務獲得所需的資源。 忙碌的服務可有效地從其他閒置或執行較少工作的複本或執行個體「回收」資源。

在您的「可靠服務」中，動態回報負載的程式碼看起來會像這樣：

程式碼：

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

服務複本或執行個體可以回報計量的負載，但僅限於建立這些服務複本或執行個體時設定讓它們使用的計量。 服務可回報的計量清單即是建立服務時所指定的該組相同計量。 與服務關聯的計量清單也可經由動態方式更新。 如果服務複本或執行個體嘗試針對目前未設定成可使用的計量回報負載，Service Fabric 會記錄該報告但會予以忽略。 如果相同 API 呼叫中有其他回報的有效計量，系統就會接受並使用這些報告。 這樣很棒，因為這可允許進行更大規模的實驗。 程式碼可以測量並回報它知道的所有計量，而操作員則可以指定和更新該服務的的計量組態，而不需變更程式碼。 例如，系統管理員和操作員小組可以停用針對特定服務有錯誤報告的計量、根據行為重新設定計量的權數，或僅在已經透過其他機制部署及驗證程式碼之後才啟用新的計量。

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>混用預設負載值和動態負載報告
如果預設負載不敷使用，而系統建議採用動態方式回報負載，是否可同時使用兩者？ 可以！ 實際上，這就是建議使用的組態。 在既設定預設負載又使用動態負載報告的情況下，預設負載會作為預估值，直到動態報告出現為止。 此做法相當好，因為它提供一些可供「叢集資源管理員」使用的資訊。 預設負載可讓「叢集資源管理員」在建立服務物件時，就將它們放在適當的位置。 如果未提供任何預設負載資訊，就會以隨機方式安置服務。 當稍後傳入負載報告時，「叢集資源管理員」幾乎一律必須四處移動服務。

讓我們採用先前的範例，看看當我們加入一些自訂負載和動態負載報告功能時會發生什麼狀況。 在此範例中，我們使用「記憶體」作為範例計量。 讓我們假設一開始使用下列命令建立了具狀態服務︰

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

提醒您，此語法是 ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad")。

讓我們看看可能的叢集配置看起來如何︰

<center>
![使用預設和自訂計量平衡的叢集][Image2]
</center>

有幾件事值得一提：

* 由於複本或執行個體在回報自己的負載之前會先使用服務的預設負載，因此我們知道具狀態服務之資料分割 1 內的複本尚未動態回報負載
* 資料分割內的次要複本可以有自己的負載
* 整體計量看起來已達平衡。 就記憶體而言，最大和最小負載之間的比例為 1.75 (負載最高的節點是 N3，最低的是 N2，而 28/16 = 1.75)。

還有一些我們仍需說明的事項：

* 何者決定 1.75 的比率是否合理？ 「叢集資源管理員」如何知道已經做得夠好還是有待加強？
* 何時發生平衡？
* 記憶體的權數「很高」是什麼意思？

## <a name="metric-weights"></a>度量權數
能夠追蹤各個不同服務的相同計量相當重要。 該檢視可讓「叢集資源管理員」追蹤叢集中的耗用量、平衡各個節點之間的耗用量，以及確保節點不會超出容量。 不過，服務在相同計量的重要性方面可能有不同的檢視。 此外，在具有許多計量和許多服務的叢集中，可能並非所有計量都有完美平衡的解決方案。 「叢集資源管理員」應該如何處理這些情況？

計量權數可讓「叢集資源管理員」在沒有完美解答時，決定如何平衡叢集。 計量權數也可讓「叢集資源管理員」以不同的方式平衡特定服務。 度量可以有四個不同的權數層級︰零、低、中和高。 在考量項目是否平衡時，權數為「零」的計量並沒有任何貢獻，但其負載對容量之類的事項仍有所貢獻。

叢集中不同計量權數的實際影響在於「叢集資源管理員」會產生不同的解決方案。 計量權數會告訴「叢集資源管理員」某些計量比其他計量重要。 當沒有完美的解決方案時，「叢集資源管理員」可以偏好選擇能更有效平衡較高權數計量的解決方案。 如果一項服務認為某個計量不重要，它可能會發現對該計量的使用不平衡。 這可讓另一項服務取得對其重要的平均分配。

讓我們看看一些負載報告的範例，以及不同的計量權數如何在叢集中造成不同的配置。 在此範例中，我們看到切換計量的相對權數會導致「叢集資源管理員」偏好不同的解決方案，以及建立不同的服務佈局。

<center>
![計量權數範例及其對平衡解決方案的影響][Image3]
</center>

在此範例中，有四個不同的服務，全部都針對兩個不同的計量 A 和 B 回報不同的值。在其中一個案例中，所有服務都將 MetricA 定義為最重要 (Weight = High) 而將 MetricB 定義為不重要 (Weight = Low)。 在此情況下，我們看到「叢集資源管理員」是以讓 MetricA 比 MetricB 更加平衡 (標準差較小) 的方式來設置服務。 在第二個案例中，我們將計量權數反轉。 結果就是「叢集資源管理員」可能會交換服務 A 與 B，以便產生 MetricB 比 MetricA 更加平衡的配置。

### <a name="global-metric-weights"></a>全域度量權數
如果 ServiceA 將 MetricA 定義為最重要，而且 ServiceB 完全不在意 MetricA，則最終使用的實際權數為何？

實際上，針對每個計量都會追蹤多個權數。 第一個集合是每個服務針對計量定義的權數。 其他權數是全域權數，是回報該計量之所有服務的平均值。 「叢集資源管理員」在計算解決方案的分數時會同時使用這兩種權數。 這是因為不僅根據服務自己的優先順序來平衡服務相當重要，正確配置叢集整體也相當重要。

如果「叢集資源管理員」既不在意全域平衡也不在意區域平衡，會發生什麼狀況？ 建構全域平衡但會導致個別服務之資源配置不佳的解決方案並無價值。 在以下範例中，讓我們看看在具狀態服務設定的預設計量，並了解如果只考量全域平衡，會發生什麼狀況：

<center>
![全域唯一解決方案的影響][Image4]
</center>

在上半部的範例中，我們只考量了全域平衡，叢集整體上的確達到平衡。 所有節點的主要複本計數和複本總數都相同。 不過，如果您查看此配置的實際影響，就不是那麼理想︰遺失任何節點都會對特定工作負載帶來不成比例的影響，因為這會取出其所有主要複本。 例如，如果第一個節點發生失敗，圓形服務之三個不同資料分割的三個主要複本將會全部遺失。 相反地，其他兩個服務 (三角形和六邊形) 的資料分割遺失一個複本，這不會導致中斷 (除了必須復原已關閉的複本以外)。

在下半部的範例中，「叢集資源管理員」已同時根據全域和個別服務平衡來分配複本。 在計算解決方案的分數時，它會將大部分權數給予全域解決方案，而將一部分 (可設定) 給予個別的服務。 計算全域平衡時，是根據針對每項服務設定之計量權數的平均值來計算。 平衡每項服務時，是根據其自己已定義的計量權數來平衡。 這可確保儘可能根據服務本身的要求，在服務彼此之間達到平衡。 因此，如果相同的第一個節點發生失敗，主要 (和次要) 複本的遺失會分散在所有服務的所有資料分割。 對每個資料分割的影響都一樣。

## <a name="next-steps"></a>後續步驟
* 如需有關其他可用來設定服務之選項的詳細資訊，請查看[了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)中所提供之其他「叢集資源管理員」組態的相關主題。
* 定義重組度量是合併 (而不是擴增) 節點上負載的一種方式。 若要了解如何設定重組，請參閱 [這篇文章](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於 [平衡負載](service-fabric-cluster-resource-manager-balancing.md)
* 從頭開始，並 [取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)
* 移動成本是向叢集資源管理員發出訊號，表示移動某些服務會比較貴的其中一種方式。 若要深入了解移動成本，請參閱 [這篇文章](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png



<!--HONumber=Jan17_HO4-->


