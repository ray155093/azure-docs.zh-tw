<properties
   pageTitle="Service Fabric 叢集資源管理員 - 管理整合 | Microsoft Azure"
   description="叢集資源管理員和 Service Fabric 管理之間的整合點概觀。"
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


# 叢集資源管理員與 Service Fabric 叢集管理整合
Service Fabric 叢集 Resource Manager 不是負責處理管理作業 (如應用程式升級) 的主要 Service Fabric 元件，但它會涉入其中。叢集資源管理員協助管理的第一個方法是從資源和平衡的角度，從中追蹤想要的叢集和服務的狀態，並且在它無法將叢集放入想要的組態時傳送出健康狀態報告 (範例為容量不足時，或服務應放置位置的規則衝突時)。整合的另一部分與升級運作方式有關：在升級期間，叢集資源管理員會改變其行為。我們將在下面討論這兩者。

## 健康狀態整合
叢集資源管理員會經常追蹤您為服務定義的規則，以及叢集中節點上可用的容量，並在不符合這些規則或容量不足時發出健康狀態警告和錯誤。比方說，如果節點超過容量且叢集資源管理員無法更正此情況，它就會發出健康狀態警告，指出哪一個節點超出容量以及針對哪些度量。

您將看見資源管理員發出健康狀態警告的另一個時間範例，就是如果您已定義放置條件約束 (例如 "NodeColor == Blue") 且資源管理員偵測到該條件約束的違規情形。對於自訂條件約束以及會自動強制執行的預設條件約束 (例如容錯和升級網域條件約束)，我們都會為您這麼做。

以下是這類健康狀態報告的範例。在此例中，健康狀態報告適用於系統服務的其中一個資料分割，因為該資料分割的複本暫時封裝成少量的升級網域，有可能是錯誤字串所致︰

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

以下是此健康狀態訊息要告訴我們的事情︰

1.	所有複本本身的狀態都很好 (這是 Service Fabric 的首要之務)
2.	目前違反升級網域發佈條件約束 (意味者特定升級網域擁有過多的磁碟分割複本)
3.	哪個節點包含造成違規的複本 (具有下列識別碼的節點︰3d1a4a68b2592f55125328cd0f8ed477)
4.	何時發生這種情形 (2015 年 8 月 10 日下午 7:13:02)

這是在生產環境中觸發之警示的絕佳資料，可讓您知道已發生問題，而您可能想要查看一下。比方說，在此情況下，我們會想查看是否可以找出 Resource Manager 為何不覺得除了將複本封裝到升級網域中，還有任何選擇。這可能是因為其他升級網域中的所有節點都已關閉，而且沒有足夠的其他備用網域，或者如果有足夠的網域開啟，但其他項目導致這些升級網域中的節點無效 (例如，服務上的一些放置原則或容量不足)。

不過，假設您想要建立服務，或資源管理員正嘗試尋找一個位置來放置某些服務，但似乎沒有任何可行的解決方案。原因有很多，但通常是由於下列兩種情況之一︰

1.	某個暫時性情況導致無法正確地放置此服務執行個體或複本
2.	此服務的需求設定錯誤，以致無法滿足其需求。

在每種情況中，您都會看見叢集資源管理員所提供的健康狀態報告，其中包含可協助您判斷目前的狀況以及為何無法放置服務的資訊。我們將此程序稱為「條件約束消除序列」。在此期間，系統會逐步解說會影響服務與記錄的已設定條件約束，以及它們所消除的項目。如此一來，當項目無法放置時，您可以看到哪些節點已經消除及消除原因。

## 條件約束類型
讓我們接著討論您可以在這些健康狀態報告中看見的各種條件約束，以及其所檢查的內容。請注意，大部分的情況下，您不會看到部分的這些條件約束消除節點，因為條件約束預設位於軟式或最佳化層級 (本文後面內容中有提供條件約束優先順序的相關詳細資訊)。但是，您可以看到和條件約束有關的健康情況訊息，了解它們是否是設定為硬式條件約束，或它們是否在極少見的情況下確實造成節點被消除，所以我們在這裡提供這些健康情況訊息以提供完整資訊：

-	ReplicaExclusionStatic 和 ReplicaExclusionDynamic – 這是內部條件約束，表示在搜尋過程中，我們遇到來自相同分割區的兩個具狀態複本，或無狀態執行個體必須放置在相同節點上 (不允許這麼做) 的情況。ReplicaExclusionStatic 和 ReplicaExclusionDynamic 幾乎是完全相同的規則。ReplicaExclusionDynamic 條件約束表示「我們無法將這個複本放在這裡，因為唯一提議的解決方案已在這裡放置一個複本」。這與 ReplicaExclusionStatic 排除不同，其指出不是提議的衝突，而是實際的衝突 – 節點上已經有一個複本。這會令人混淆嗎？ 是。是否很重要？ 否。您只要知道，如果您看到的條件約束消除序列包含 ReplicaExclusionStatic 或 ReplicaExclusionDynamic 條件約束，叢集資源管理員就會認為沒有足夠的節點可放置所有複本。進一步的條件約束通常會告訴我們，一開始節點太少會有何結果。
-	PlacementConstraint︰如果您看到此訊息，就表示我們已消除一些節點，因為它們不符合服務的放置條件約束。我們會在此此訊息中描繪出目前所設定的放置條件約束。如果您有提供任何放置條件約束，這通常就是正常的，但是，如果放置條件約束中有錯誤，造成排除太多節點，您就會在這裡看到該項結果。
-	NodeCapacity︰如果您看到這個條件約束，就表示我們無法將複本放在指定的節點上，因為這樣會導致節點超出容量。
-	Affinity︰這個條件約束表示我們無法將複本放在受影響的節點上，因為這會導致違反同質性條件約束。
-	FaultDomain 和 UpgradeDomain︰如果將複本放在指定的節點上會導致複本封裝在特定的容錯或升級網域中，此條件約束就會消除節點。[容錯與升級網域條件約束及產生的行為](service-fabric-cluster-resource-manager-cluster-description.md)中的主題有提供幾個討論此條件約束的範例
-	PreferredLocation︰您通常不會看到這個條件約束導致節點從解決方案中移除，因為該條件約束預設僅限用於最佳化。此外，慣用的位置條件約束通常只會出現在升級期間 (當它用於將複本移回到開始升級時的位置)，然而也有可能。

### 條件約束優先順序
在所有條件約束中，您可能會想：「嘿 – 我認為放置條件約束是我的系統中最重要的事。我願意違反其他條件約束，甚至是像同質和容量的事項，如果它可確保不會違反放置條件約束。」

其實我們可以這麼做！ 可以使用幾個不同層級的強制執行來設定條件約束，但是會歸結為「硬式」(0)、「軟式」(1)、「最佳化」(2) 和「關閉」(-1)。我們預設將大部分條件約束定義為硬式 (因為舉例來說，大部分使用者通常不會將容量視為可以寬鬆的事項)，大部分不是硬式就是軟式。不過在進階情況下有所變更。例如，假如您想要確保一律違反同質以便解決節點容量問題，您可以將同質條件約束的優先順序設為「軟式」(1)，並且保持容量條件約束設為「硬式」(0)。不同的條件約束優先順序也是為何您較常看到某些條件約束違反警告的原因 - 因為我們願意暫時讓特定條件約束變得寬鬆 (違反)。請注意，這些層級並不是真正表示一律或絕對不會違反指定的條件約束，只是有偏好的強制執行順序，因此當無法滿足所有條件約束時，我們可以做正確的權衡。

不同條件約束的組態和預設優先順序值如下所列：

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

您會在這裡注意到，有針對升級網域和容錯網域定義的條件約束，且升級網域條件約束為「軟式」。另外還有一個具有優先順序的怪異 “PreferredLocation” 條件約束。這是什麼？

首先，我們將想要服務分散於容錯和升級網域之間，作為 Resource Manager 的引擎內的條件約束這個想法模型化。過去曾經發生過幾次，我們必須對於與容錯和升級網域的相關放置真正嚴格，而也有一些情況下有一些問題，只需要完全略過它們 (雖然簡短！)，因此一般來說，我們很高興能有該設計選擇和條件約束基礎結構的彈性。大多數情況下，升級網域條件約束都是軟式條件約束，也就是說，如果 Resource Manager 需要暫時將一些複本封裝至升級網域以進行處理，例如升級，或一些並行失敗或其他條件約束違規 (從硬式條件約束)，那麼這是沒問題的。這通常不會發生，除非有許多失敗或系統中其他流失阻止正確放置，而且如果已正確設定環境，則穩定狀態一律會是升級網域。

PreferredLocation 條件約束有點不同，因此它是唯一設為「最佳化」的條件約束。我們在升級進行中使用這個條件約束，嘗試將服務放回升級之前發現它們的位置。在實務上不可行還有各種原因，但是它是不錯的最佳化，因此就這樣。我們會在談論叢集資源管理員如何協助升級時詳細討論它。

## 升級
在應用程式和叢集升級期間，叢集資源管理員也有所幫助，以確保順利升級，而且也會確保規則和叢集效能不會受到危害。

### 繼續強制執行規則
規則是需要注意的重點 – 在升級期間仍會強制執行一些嚴格條件約束 (如放置條件約束)。您會認為這是理所當然的，但我們仍要明確進行說明。這麼做的好處是如果您想要確定特定工作負載不會在特定的節點上執行，這些規則會在自動升級期間強制執行。如果您的環境受到高度約束，這會導致升級花費很長的時間，因為如果服務 (或它所在的節點) 需要關閉進行更新，則服務只有少數幾個選項可用。

### 聰明取代
開始升級時，資源管理員會取得目前叢集排列方式的快照，並嘗試傳回可陳述何時完成升級的資訊。背後的原因很簡單 – 首先它會確保在升級過程中每個服務複本或執行個體只有一些轉換 (將受影響的節點移出以及移回)。接著，它會確保升級本身對於叢集的配置沒有太大的影響；如果在升級前就已安排好叢集，則升級後也會安排妥當，至少不會更糟。

### 減少流失
升級期間發生還會發生另一件事，就是叢集 Resource Manager 對正在升級的實體關閉平衡作業。所以如果您有兩個不同的應用程式執行個體，而在其中一個執行個體上開始升級，則該應用程式執行個體的平衡作業會暫停，但另一個不會。阻止反應式平衡即可防止對升級本身的不必要反應 (「喔不！ 空的節點！ 最好以各式各樣的東西將它填滿！」)，因此導致叢集中的服務額外進行大量移動，而這些移動必須在完成升級後服務必須移回節點時進行復原。如果有問題的升級是叢集升級，則整個叢集會在升級期間暫停進行平衡 (條件約束檢查 – 確保會強制執行規則 – 持續作用中，只停用主動式重新平衡)。

### 寬鬆的規則
在升級期間，即使叢集整體相當受限或已滿，您通常還是會想完成升級。在升級期間能夠管理叢集容量甚至比叢集升級期間通常有 5% 到 20% 的容量關閉更為重要，而且該工作負載通常必須移到某個位置。這就是[緩衝處理的容量](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity)的概念真正派上用場的地方 – 雖然在正常作業期間會遵守緩衝容量 (保持一些額外負荷)，但叢集資源管理員將在升級期間填滿總容量 (佔用緩衝區)。

## 後續步驟
- 從頭開始，並[取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)

<!---HONumber=AcomDC_0824_2016-->