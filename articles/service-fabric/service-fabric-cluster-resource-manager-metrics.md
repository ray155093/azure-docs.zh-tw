<properties
   pageTitle="使用 Azure Service Fabric 叢集資源管理員管理度量 | Microsoft Azure"
   description="深入了解如何在 Service Fabric 中設定及使用度量。"
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

# 在 Service Fabric 中使用度量管理資源耗用量和負載
度量是 Service Fabric 中您的服務所關切的資源的通用詞彙，且是由叢集中的節點提供。一般而言，度量就是任何您想要管理，以便處理您的服務效能的項目。

像記憶體、磁碟、CPU 使用率等項目 – 這些都是度量的範例。這些是實體度量，也就是對應至節點上需要管理之實體資源的資源。度量也可以 (且通常是) 邏輯度量，例如應用程式定義的 “MyWorkQueueDepth”，其對應於某個層級的資源耗用量 (但應用程式並沒有真正了解或不知道如何測量它)。我們看到使用者使用的度量大部分是邏輯度量。這有各種原因，但是最常見的原因是現今我們的許多客戶會使用受管理的程式碼撰寫他們的服務，很難從指定的無狀態服務執行個體或具狀態服務複本物件測量和報告實際實體資源的耗用量。報告您自己的度量的複雜度也是為什麼我們要提供現成的一些預設度量。

## 預設度量
假設您只想要開始使用，但不知道您即將取用哪些資源，或甚至哪些資源對您很重要。所以您進行實作，然後建立您的服務，但未指定任何度量。沒關係！ 我們將為您挑選一些度量。如果您未指定任何自己的度量，我們今天為您使用的預設度量就稱為 PrimaryCount、ReplicaCount 和 (我們知道有點模糊) Count。下表顯示這些度量各有多少負載與每個服務物件有關聯︰

| 度量 | 無狀態執行個體負載 |	具狀態次要負載 |	具狀態主要負載 |
|--------|--------------------------|-------------------------|-----------------------|
| PrimaryCount | 0 |	0 |	1 |
| ReplicaCount | 0 | 1 | 1 |
| Count |	1 |	1 |	1 |

好了，透過這些預設度量，您得到什麼？ 結果就是，對於基本工作負載，您得到相當不錯的工作分配。在下列範例中，我們來看看當我們建立一個包含三個資料分割且目標複本集大小為 3 的具狀態服務，以及一個執行個體計數為 3 的無狀態服務時，會發生什麼狀況 - 您會得到如下的內容！

![使用預設度量的叢集配置][Image1]

在此範例中，我們看到：
-	具狀態服務的主要複本並未堆疊於單一節點上
-	相同資料分割的複本不在相同節點上
-	主要複本與次要複本的總數均勻地分佈於叢集中
-	服務物件 (無狀態與具狀態) 的總數平均配置於每個節點

相當不錯！

這非常適合，直到您開始思考︰您選擇的資料分割配置導致經過一段時間讓所有資料分割完全平均使用的可能性？ 此外，指定服務的負載在一段時間內保持不變的機率為何，或只是現在相同？ 結果，對於任何嚴重的工作負載，所有複本都對等的可能性實際上相當低，因此如果您對於獲得叢集最大使用量感興趣，可能會想要開始考慮自訂度量。

實際上，您絕對可以只搭配預設度量執行，但這麼做通常意味著您的叢集使用率低於您的期望 (因為報告不具調適性且假設所有項目都對等)；在最糟的情況下，也可能導致過度排程的節點，因而造成效能問題。使用自訂度量和動態負載報告 ，我們可以處理得比較好 (我們接下將會探討)。

## 自訂度量
我們已經討論過可以有實體和邏輯度量，而且使用者可以定義自己的度量。太棒了！ 但是怎麼做？ 其實很簡單！ 只要在建立服務時設定度量和預設初始負載，就大功告成！ 當您建立服務時，可以為每個具名服務執行個體設定任何一組代表預期取用多少服務的度量和預設值。

請注意，當您開始定義自訂度量時，如果您希望我們也將預設度量用於平衡您的服務，則必須明確地加回預設度量。這是因為我們希望您能清楚了解預設度量與自訂度量之間的關聯性 – 相較於主要分配，您或許比較在意記憶體耗用量或 WorkQueueDepth。

假設您想要設定一項服務，該服務會報告名為「記憶體」的度量 (除了預設度量以外)。對於記憶體，我們假設您已完成一些基本測量，並且知道該服務的主要複本通常佔用 20Mb 的記憶體，而該相同服務的次要複本則佔用 5Mb。您知道就管理這項特定服務的效能而論，記憶體是最重要的度量，但您仍希望主要複本達到平衡，某個節點或容錯網域的遺失才不會取用過多的主要複本。除此之外，您將採用預設值。

以下是您要執行的動作：

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(提醒您，如果您只想要使用預設度量，您就完全不必碰觸度量集合，或在建立服務時執行任何特殊動作。)

我們已示範如何定義您自己的度量，接著來討論度量可具有的各種屬性。我們已向您展示過這些屬性，現在讓我們來討論它們的實質意義！ 一個度量目前可有四個不同的屬性

-	度量名稱︰這是度量的名稱。從資源管理員的觀點來看，這是叢集內度量的唯一識別碼。
- 預設負載：預設負載會依據服務是無狀態或具狀態服務來以不同方式表示。
  - 對於無狀態服務，每個度量都只會有名為「預設負載」的單一屬性
  - 對於具狀態服務，您可以定義
    -	PrimaryDefaultLoad：此服務將為此度量 (當作主要度量) 行使的預設負載量
    -	SecondaryDefaultLoad：此服務將為此度量 (當作次要度量) 行使的預設負載量
-	權數︰這是度量對於此服務的重要程度 (相對於其他設定的度量)。

## 載入
負載是特定節點上的某個服務執行個體或複本取用多少特定度量的一般概念。

## 預設負載
預設負載是叢集 Resource Manager 從實際的服務執行個體或複本收到任何更新之前，應假設每個服務執行個體或複本將取用多少負載。對於較簡單的服務，這就是永遠不會動態更新的靜態定義，因此將用於服務的存留期。這很適合用於簡單的容量規劃，因為這正好是我們習慣的做法 – 將某些資源投注於某些工作負載，好處是至少我們現在是以微服務的心態運作，其中的資源實際上不會靜態指派給特定工作負載，而其中的人員也不在决策圈內。

我們允許具狀態服務指定其主要複本和次要複本的預設負載 – 實際上對許多服務而言，這些數字都不同，因為主要複本和次要複本會執行不同的工作負載，而且主要複本通常可用於讀取和寫入 (以及大部分的運算負荷)，而主要複本的預設負載會大於次要複本。

現在假設您已執行您的服務一段時間，而您已注意到服務的某些執行個體或複本耗用比其他執行個體或複本更多的資源，或其耗用量會隨時間改變 – 或它們許與特定客戶相關聯，或許只是對應至會在一天內產生變化的工作負載，例如傳訊流量、電話通話或股票交易。無論如何，您會注意到，若沒有至少在部分時間中依照一個有意義的數量進行區隔，您就沒有「單一數字」可用於此負載。您也會注意到，在初始估計值中「進行區隔」會導致叢集資源管理員將過多或過少的資源配置給您的服務，以致您節點的使用量過高或過低。

怎麼辦？ 您的服務可能正在報告負載！

## 動態負載
動態負載報告可讓複本或執行個體在其存留期中調整其在叢集中的配置/報告的度量使用量。閒置且未執行任何工作的服務複本或執行個體通常會回報它使用少量的指定度量，而忙碌的複本或執行個體則會回報他們使用較多的資源。叢集中的這個一般流失層級可讓我們迅速重新組織叢集中的服務複本和執行個體，以確保取得它們所需的資源 – 實際上，忙碌的服務能夠從目前閒置或執行較少工作的其他複本或執行個體回收資源。透過 ReportLoad 方法 (可在 ServicePartition 上取得，並可透過 Reliable Services 程式設計模型做為基底 StatefulService 或 StatelessService 類別的屬性使用) 可以迅速完成負載報告。在您的服務中，程式碼如下所示︰

程式碼：

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("metric1", 42) });
```

服務複本或執行個體只能針對它們設定要使用的度量報告負載。建立每個服務時會設定度量清單，且之後可以更新。如果服務複本或執行個體嘗試針對目前未設定要使用的度量報告負載，Service Fabric 會記錄此報告但予以忽略，這表示我們在計算或報告叢集的狀態時不會使用該度量。這很完美，因為它能夠進行更大規模的實驗 – 程式碼可以測量並報告它知道作法的所有項目，而運算子可以迅速設定、調整及更新該服務的資源平衡規則，但不必變更程式碼。比方說，這可能包括停用有錯誤報告的度量、根據行為重新設定度量的權數，或僅在透過其他機制部署及驗證程式碼之後啟用新的度量。

## 混用預設負載值和動態負載報告
為即將以動態方式報告負載的服務指定預設負載，是否合理？ 當然！ 在此情況下，預設負載可做為估計值，直到真正的報告開始從實際的服務複本或執行個體顯示為止。這真的很棒，因為它提供一些項目讓叢集資源管理員使用 - 預設負載評估可讓它從一開始就將服務執行處理或複本放置在最佳位置。在沒有提供預設負載資訊時，服務的位置會在建立時隨機設定，因此如果之後負載有所變更，叢集資源管理員幾乎都必須移動項目。

所以讓我們採用先前的範例，看看當我們加入一些自訂負載時會發生什麼狀況，然後在建立服務之後，它會以動態方式更新。在此範例中，我們將以「記憶體」為例，並假設我們一開始使用下列命令建立具狀態服務︰

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

我們之前討論過此語法 (MetricName、MetricWeight、PrimaryDefaultLoad、SecondaryDefaultLoad)，但我們稍後會詳細討論權數的特定值有何意義。

讓我們看看可能的叢集配置看起來如何︰

![使用預設和自訂度量平衡的叢集][Image2]

有幾件事值得一提：

-	由於複本或執行個體在報告自己的負載前，會使用服務的預設負載，我們知道具狀態服務的資料分割 1 內的複本尚未自行報告負載
-	資料分割內的次要複本可以有自己的負載
-	整體度量看起來很不錯，節點上最大和最小負載之間的差異 (對於記憶體 – 我們說過是最在意的自訂度量) 比率只有 1.75 (具有最多記憶體負載的節點是 N3，最少是 N2，而 28/16 = 1.75) – 相當平衡！

我們仍必須說明下列事項：

-	何者決定 1.75 的比率是否合理？ 我們如何知道這是否夠好，或者還需要再做些什麼？
-	何時發生平衡？
-	記憶體的權數「很高」是什麼意思？

## 度量權數
度量權數允許兩個不同的服務報告相同的度量，但不得以不同方式看待平衡該度量的重要性。例如，請考慮記憶體內部分析引擎和持續性資料庫；兩者可能都很在意「記憶體」度量，但記憶體中服務可能不太在意「磁碟」度量 – 它可能會少量使用，但並非服務效能的關鍵，因此可能甚至不會報告它。能夠跨越不同的服務來追蹤相同的度量真的很棒，因為此功能可讓叢集資源管理員追蹤叢集中的實際耗用量，確保節點不會超過容量等。

度量權數也可讓叢集資源管理員在沒有完美解答時，決定如何平衡叢集 (這是很多時間)。度量可以有四個不同的權數層級︰零、低、中和高。在考慮是否平衡時，權數為「零」的度量沒有任何貢獻，但其負載對於容量測量仍有所貢獻。

叢集中不同度量權數的實際影響是我們達到不同的服務排列方式，因為叢集資源管理員被告知某些度量比其他度量更加重要。因為它知道這個情況，所以當具有不同權術的度量和其他度量衝突時，叢集資源管理員就可以選擇能夠更有效平衡較高權數度量的解決方案。

讓我們看看簡單的負載報告範例，以及不同的度量權數如何在叢集中造成不同的配置。在此範例中，我們看到切換度量的相對權數會導致資源管理員藉由建立不同的服務排列，而偏好某些解決方案。

![度量權數範例及其對平衡解決方案的影響][Image3]

在此範例中，有四個不同的服務，全部都是報告兩個不同度量 A 和 B 的不同值。在所有服務定義的其中一個案例中，度量 A 最為重要 (權數 = 高) 而 MetricB 則相對不重要 (權數 = 低)，我們確實看到叢集資源管理員放置這些服務，所以 MetricA 比 MetricB 更加平衡 (標準偏差較小)。在第二個案例中，我們反轉度量權數，我們看到叢集資源管理員可能會交換服務 A 與 B，以便產生 MetricB 比 MetricA 更加平衡的配置。

### 全域度量權數
如果 ServiceA 將 MetricA 定義為最重要，而且 ServiceB 完全不在意 MetricA，則最終使用的實際權數為何？

我們實際會為每個度量追蹤兩個權數 – 服務本身定義的權數，以及所有在意該度量的服務所適用的全域平均權數。我們會使用這兩個權數來計算我們所產生之解決方案的分數，因為務必要確保服務與它自己的優先順序達到平衡，且叢集整體上配置正確。

如果我們不在意全域和區域平衡，會發生什麼狀況？ 建構全域平衡的解決方案雖然微不足道，但這會導致個別服務的平衡和資源配置極為不佳。在下列範例中，請考慮用來設定具狀態服務的預設度量 PrimaryCount、ReplicaCount 和 Count，並了解當我們只考慮全域平衡時會發生什麼狀況︰

![全域唯一解決方案的影響][Image4]

在最上面的範例中，我們只探討全域平衡，叢集整體上的確達到平衡 – 所有節點的主要複本和總複本的計數都相同。不過，如果您查看此配置的實際影響，就不是那麼理想︰遺失任何節點會對特定工作負載帶來不成比例的影響，因為這會除取其所有的主要複本。舉例來說，如果我們遺失第一個節點時。如果發生這種情形，圓形服務的三個不同資料分割的三個主要複本會全部同時遺失。相反地，其他兩個服務 (三角形和六邊形) 的資料分割遺失一個複本，這不會導致中斷 (除了必須復原已關閉的複本以外)。

在最下方的範例中，我們根據全域和個別服務平衡來散發複本。在計算解決方案的分數時，我們會將大部分的權數提供給全域解決方案，但是 (可設定) 的部分會確保服務本身儘可能平衡。因此，如果我們遺失相同的第一個節點，我們會看到遺失的主要複本 (和次要複本) 分散於所有服務的所有資料分割，其對每個資料分割的影響都一樣。

將度量權數納入考量，全域平衡會根據針對每項服務所設定度量權數的平均值計算。我們會平衡服務與其自己的已定義度量權數。

## 後續步驟
- 如需可用來設定服務的其他選項的詳細資訊，請查看[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)中提供的其他叢集資源管理員組態的相關主題
- 定義重組度量是合併 (而不是擴增) 節點上負載的一種方式。若要了解如何設定重組，請參閱[這篇文章](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於[平衡負載](service-fabric-cluster-resource-manager-balancing.md)的文章
- 從頭開始，並[取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)
- 移動成本是向叢集資源管理員發出訊號，表示移動某些服務會比較貴的其中一種方式。若要深入了解移動成本，請參閱[這篇文章](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0824_2016-->