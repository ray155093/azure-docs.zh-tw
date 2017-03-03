---
title: "資源平衡器叢集描述 | Microsoft Docs"
description: "將容錯網域、升級網域、節點屬性和節點容量指定給叢集資源管理員，以描述 Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: a595a2e24b4c2582998d88f74ca13daba5fe618d
ms.lasthandoff: 02/17/2017

---

# <a name="describing-a-service-fabric-cluster"></a>描述 Service Fabric 叢集
Service Fabric 叢集資源管理員提供數種機制，來描述叢集。 在執行階段，叢集資源管理員會使用此資訊，以確保叢集中執行之服務的高可用性。 在強制執行這些重要規則時，它也會嘗試將叢集的資源消耗量最佳化。

## <a name="key-concepts"></a>重要概念
叢集資源管理員支援數個描述叢集的功能︰

* 容錯網域
* 升級網域
* 節點屬性
* 節點容量

## <a name="fault-domains"></a>容錯網域
容錯網域是任何連鎖故障區域。 單一電腦是一個容錯網域 (因為它本身可能因各種原因而失敗，例如電源供應器故障、磁碟機故障或 NIC 韌體不正確)。 連接至相同乙太網路交換器的電腦位於相同的容錯網域，共用單一電源的電腦也是如此。 由於硬體故障天生就會重疊，容錯網域本質上就具備階層性，且在 Service Fabric 中以 URI 表示。

設定您自己的叢集時，必須仔細考慮這些不同的故障區域。 必須正確設定容錯網域，因為 Service Fabric 會使用這項資訊來安全地放置服務。 Service Fabric 放置服務時會注意不要因為遺失容錯網域 (某個元件失敗所造成) 而導致服務中斷。 在 Azure 環境中，Service Fabric 會使用環境所提供的容錯網域資訊，代替您正確地設定叢集中的節點。

下圖中，我們將所有參與容錯網域的實體塗上顏色，並列出形成的所有不同容錯網域。 在此範例中，我們有資料中心 ("DC")、機架 ("R") 和刀鋒伺服器 ("B")。 如果每個刀鋒伺服器包含一個以上的虛擬機器，可以預料，容錯網域階層中還會有另一層。

<center>
![透過容錯網域組織的節點][Image1]
</center>

在執行階段，Service Fabric 叢集資源管理員會考慮叢集中的容錯網域，並規劃配置。  它會嘗試為某個服務分散具狀態複本或無狀態執行個體，讓它們位在不同的容錯網域中。 如果有任何一個容錯網域失敗 (在階層中任何層級)，此程序有助於確保不會危及該服務的可用性。

Service Fabric 的叢集資源管理員不在乎容錯網域階層中有多少層。 不過，當階層的任何一部分遺失時，它會嘗試確保這不會影響上方執行的服務。 因此，在容錯網域階層中每個深度上，最好有相同的節點數目。 保持層級平衡可避免某個階層部分所包含的服務比其他部分更多。 否則會導致個別節點的負載失衡，使得某些網域的失敗情形比其他網域更嚴重。

如果叢集中的容錯網域「樹狀結構」不平衡，叢集資源管理員會很難找出服務的最佳配置。 不平衡的容錯網域配置表示遺失特定網域時，叢集可用性受影響的程度可能大於其他網域。 因此，叢集資源管理員在兩個目標之間掙扎︰希望將服務放在該「重負荷」網域中的電腦上以使用這些電腦，也希望放置服務時不要因為遺失網域而造成問題。 這樣看起來像什麼？

<center>
![兩個不同的叢集配置][Image2]
</center>

上圖中，我們顯示兩個不同的叢集配置範例。 在第一個範例中，節點平均分散至容錯網域。 在另一個範例中，一個容錯網域最後會有更多節點。 如果您想要在內部部署或另一個環境中建立自己的叢集，則必須考慮一些事項。

Azure 會替您選擇哪一個容錯網域包含節點。 不過，根據您佈建的節點數目而定，某些容錯網域的節點最後仍可能比其他容錯網域更多。 例如，假設您有五個容錯網域，但佈建特定 NodeType 的七個節點。 在此情況下，前兩個容錯網域最後會有較多節點。 如果您繼續部署多個 NodeType，但只有幾個執行個體，則問題會更糟。

## <a name="upgrade-domains"></a>升級網域
升級網域是另一項功能，可協助 Service Fabric 叢集資源管理員了解叢集的配置，以提前規劃來因應失敗狀況。 升級網域定義一組同時升級的節點。

升級網域非常類似容錯網域，但是有幾個主要的差異。 首先，雖然容錯網域是依連鎖硬體故障的區域而嚴格定義，但升級網域是依原則來定義。 使用升級網域時，您應該決定想要多少個節點，而不是取決於環境。 另一個差異是 (至少到目前為止) 升級網域不是階層式，比較像簡單標記。

下圖顯示三個升級網域等量分佈在三個容錯網域上。 其中也顯示不具狀態服務的三個不同複本有一個可能的位置，而每一個最後都在不同的容錯網域和升級網域。 在服務升級期間，即使遺失容錯網域，這個位置可讓我們仍然保有一份程式碼和資料。

<center>
容錯網域和升級網域的位置![][Image3]
</center>

大量升級網域有其利弊。 升級網域越多，每個升級步驟會更輕微，因此受影響的節點或服務較少。 因為必須一次移動的服務較少，對系統的影響較小。 這樣也較容易改善可靠性 (因為升級期間發生的任何問題會影響較少的服務)。 升級網域越多，也表示其他節點上需要較少的額外負荷來處理升級的影響。 例如，若您有五個升級網域，則每個網域的節點可處理大約 20% 的流量。 如果您需要關閉升級網域以進行升級，則負載必須移至別處。 升級網域越多，表示在叢集的其他節點上必須維護的負荷較少。

擁有許多升級網域的缺點是升級通常需要較長的時間。 升級網域完成後，Service Fabric 會稍待片刻再繼續。 此延遲是為了有機會浮現並偵測到升級所引起的問題。 缺點是可接受的，因為它會防止不良的變更一次影響到太多服務。

升級網域太少有其副作用 - 當個別升級網域關閉來升級時，整體容量會有一大部分無法使用。 例如，若您只有三個升級網域，則一次就關閉大約 1/3 的整體服務或叢集容量。 服務不宜一下子就關閉這麼多，因為叢集的剩餘部分必須有足夠容量來處理工作負載。 維持該緩衝比例表示在正常情況下，這些節點的負載偏低，以致於服務的執行成本上升。

環境中的容錯網域或升級網域總數沒有實際限制，它們的重疊方式也不受約束。 我們看到的一般結構如下︰

* 1:1 對應的容錯網域和升級網域
* 每個節點 (實體或虛擬作業系統執行個體) 一個升級網域
* 「等量」或「矩陣」模型，其中的容錯網域和升級網域形成一個矩陣，而電腦通常沿著對角線排列。

<center>
![容錯網域和升級網域配置][Image4]
</center>

對於選擇哪個配置並沒有最佳答案，每個答案都各有優缺點。 例如，1FD:1UD 模型相當容易設定。 例如，「每個節點 1 個 UD」模型最接近以往習慣管理少量電腦的情況，亦即每部電腦獨立關閉。

最常見的模型 (和 Azure 中使用的模型) 是 FD/UD 矩陣，其中 FD 和 UD 形成一個表格，而節點沿著對角線開始放置。 最後會變得稀疏還是擁擠，取決於節點總數與 FD 和 UD 數目的相互比較。 換言之，如果叢集夠大，幾乎最後都會形成密集矩陣模式，如上圖的右下方選項所示。

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容錯網域和升級網域的條件約束和導致的行為
為了讓服務在容錯網域和升級網域之間保持平衡，叢集資源管理員會將這種期望當成一種條件約束。 您可以在 [這篇文章](service-fabric-cluster-resource-manager-management-integration.md)中深入了解條件約束。 容錯網域和升級網域的條件約束表示：「針對特定的服務資料分割，兩個網域之間的服務物件數目 (無狀態服務或具狀態服務副本) 差異決不能「大於一」。  實際上，這表示針對特定的服務，某些移動或安排可能無效，因為違反容錯網域和升級網域的條件約束。

讓我們來看看一個範例。 假設我們的叢集有六個節點，且已設定五個容錯網域和五個升級網域。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

現在，假設我們建立 TargetReplicaSetSize 為&5; 的服務。 複本將會落在 N1-N5 上。 事實上，不論建立多少服務，都不會用到 N6。 原因為何？ 讓我們看看目前的配置和選擇 N6 時情況如何之間的差異。

以下是我們的配置，以及每個容錯網域和升級網域的複本總數。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

就每個容錯網域和升級網域的節點數而論，在此配置達到平衡。 在每個容錯網域和升級網域的複本數目方面也達到平衡。 每個網域都擁有相同數量的節點，以及相同數量的複本。

現在，讓我們看看改用 N6 而不使用 N2 的情況。 複本將會如何散佈？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

有注意到什麼嗎？ 此配置違反容錯網域條件約束的定義。 FD0 有兩個複本，FD1 沒有複本，所以 FD0 和 FD1 之間的差異合計為&2;。 叢集資源管理員不允許這種安排。 同樣地，如果挑選 N2 和 N6 (而不是 N1 和 N2)，則會得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

雖然就容錯網域而言，此配置達到平衡，但現在違反升級網域條件約束 (因為 UD0 沒有複本，而 UD1 有&2; 個複本)。 此配置也無效

## <a name="configuring-fault-and-upgrade-domains"></a>設定容錯網域和升級網域
Azure 託管的 Service Fabric 部署中會自動定義容錯網域和升級網域。 Service Fabric 會從 Azure 中取用環境資訊。

如果想要建立您自己的叢集 (或想要在開發環境中執行特定拓撲)，您必須自行提供容錯網域和升級網域資訊。 在此範例中，我們定義本機開發叢集，具有九個節點，且跨越三個「資料中心」(各有三個機架)。 此叢集也有三個升級網域等量分散於這些三個資料中心。 在叢集資訊清單範本中，它看起來將會如下︰

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

獨立部署透過 ClusterConfig.json

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> 在 Azure 部署中，容錯網域和升級網域由 Azure 指派。 因此， Azure 基礎結構選項內的節點和角色定義不包含容錯網域或升級網域資訊。
>
>

## <a name="placement-constraints-and-node-properties"></a>放置條件約束和節點屬性
有時候 (事實上是大部分的情況下) 您會想要確保工作負載只在叢集中的特定節點或一組特定的節點上執行。 例如，某些工作負載可能需要 GPU 或 SSD，而有些則不用。 將硬體專用於特定工作負載的最佳例子幾乎都是多層式架構。 在這些架構中，特定電腦作為應用程式的前端/介面供應端 (因此可能接觸到網際網路)。 另一群電腦 (通常有不同的硬體資源) 處理計算層或儲存層的工作 (通常不接觸網際網路)。 Service Fabric 認為即使在微服務領域，特定的工作負載有時需要在特定硬體設定上執行，例如︰

* 現有的多層式架構應用程式已「提升並移轉」到 Service Fabric 環境
* 針對效能、級別或安全性隔離原因而想要在特定硬體上執行的工作負載
* 基於原則或資源耗用量的理由，工作負載應該彼此隔離

為了支援這種設定，Service Fabric 有一流的標籤概念可運用在節點上。 這些稱為放置條件約束。 放置條件約束可以用於指出特定服務應在何處執行。 條件約束集可延伸 - 任何索引鍵/值組都沒問題。

<center>
![叢集配置不同工作負載][Image5]
</center>

節點上的不同索引鍵/值標籤稱為節點放置「屬性」 (或簡稱節點屬性)。 節點屬性中指定的值可以是字串、bool，或帶正負號長值。 服務的陳述式稱為放置「條件約束」，因為它會限制服務在叢集中可執行的位置。 條件約束可以是任何在叢集中於不同節點上運作的布林值陳述式。 這些布林值陳述式中的有效選取器如下：

1) 建立特定陳述式的條件式檢查

| 陳述式 | 語法 |
| --- |:---:|
| 「等於」 | "==" |
| 「不等於」 | "!=" |
| 「大於」 | ">" |
| 「大於或等於」 | ">=" |
| 「小於」 | "<" |
| 「小於或等於」 | "<=" |

2) 分組和邏輯運算的布林值陳述式

| 陳述式 | 語法 |
| --- |:---:|
| 「和」 | "&&" |
| 「或」 | "&#124;&#124;" |
| 「否」 | "!" |
| 「組成單一陳述式」 | "()" |

以下是基本條件約束陳述式的一些範例。

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

服務只能放置在整體陳述式評估為 "True" 的節點上。 未定義屬性的節點不符合包含該屬性的任何放置條件約束。

Service Fabric 定義一些預設節點屬性，可自動使用，而不需要由使用者定義。 截至本文撰寫為止，每個節點上定義的預設屬性是 **NodeType** 和 **NodeName**。 例如，您可以將放置條件約束撰寫成 `"(NodeType == NodeType03)"`。 我們大致上發現 NodeType 是其中一個最常用的屬性。 實用的原因在於，它與一種電腦保持 1:1 的對應關係，而這又對應於傳統多層式應用程式架構中的一種工作負載。

<center>
![放置條件約束和節點屬性][Image6]
</center>

假設某個節點類型已定義下列節點屬性︰

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

獨立部署透過 ClusterConfig.json，Azure 託管叢集透過 Template.json。 在叢集的 Azure Resource Manager 範本中，節點類型名稱之類的項目很可能參數化，看起來就像 "[parameters('vmNodeType1Name')]"，而不是 "NodeType01"。

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

您可以針對服務建立服務放置「條件約束」，如下所示︰

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

如果您確定 NodeType01 的所有節點都有效，您也可以選取該節點類型。

服務放置條件約束其中很棒的一點是它們可以在執行階段動態更新。 所以如果您需要，可以在叢集中移動服務、加入和移除需求等等。Service Fabric 會負責確保服務保持執行且可用，即使這類變更持續進行。

C#：

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell：

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

放置條件約束 (以及我們即將討論的許多其他 Orchestrator 控制項) 是針對每個不同的已命名服務執行個體指定。 更新一律會取代 (覆寫) 先前指定的項目。

節點上的屬性是透過叢集定義來定義，因此必須升級叢集才能更新。 升級節點的屬性時，每個受影響的節點必須關閉，之後再恢復運作。

## <a name="capacity"></a>容量
任何 Orchestrator 的其中一個最重要的作業是協助管理叢集中的資源耗用量。 如果想要有效率地執行服務，最重要的是不能有一群節點很忙碌，但其他節點卻閒置。 忙碌節點會導致資源爭用和效能不佳，而閒置節點代表浪費資源/增加成本。 在談及平衡之前，首先要確保節點不會耗盡資源。

Service Fabric 以 `Metrics` 表示資源。 度量是您想要向 Service Fabric 描述的任何邏輯或實體資源。 度量的範例是例如「WorkQueueDepth」或「MemoryInMb」的項目。 如需設定計量及其用法的相關資訊，請參閱[這篇文章](service-fabric-cluster-resource-manager-metrics.md)

計量不同於放置條件約束和節點屬性。 節點屬性是節點本身的靜態描述項，而計量是指節點所擁有及服務在節點上執行時所耗用的資源。 節點屬性可能是 "HasSSD"，可設為 true 或 false。 該 SSD 上可用 (和服務所耗用) 的空間數量是像 "DriveSpaceInMb" 之類的計量。 節點的 "DriveSpaceInMb" 容量以磁碟機的非保留空間總數為上限。 服務會報告它們在執行階段使用該計量的多寡。

必須注意，就像放置條件約束和節點屬性一樣，Service Fabric 叢集資源管理員也不了解計量名稱的意義。 計量名稱只是字串。 如果您建立的計量名稱可能引起歧義，最好宣告單位。

如果您關閉所有資源「平衡」，Service Fabric 的叢集資源管理員仍會嘗試確保節點不超出其容量。 這通常沒問題，除非整個叢集太滿。 容量是另一個「條件約束」，可讓叢集資源管理員了解節點使用某項資源的多寡。 另外也會追蹤整個叢集的剩餘容量。 服務層級的容量和耗用量均以度量來表示。 例如，計量可能是 "MemoryInMb"，而某個節點的 "MemoryInMb" 容量可能是 2048。 該節點上執行的某些服務可能表示它目前耗用的 "MemoryInMb" 是 64。

在執行階段，叢集資源管理員會追蹤每個節點上的每個資源共有多少和剩餘多少。 從該節點的容量中，減去節點上執行的每個服務所宣告的任何使用量，即可算出。 利用此資訊，Service Fabric 叢集資源管理員即可決定將複本放置或移至何處，不會讓節點超出容量。

C#：

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```
<center>
![叢集節點和容量][Image7]
</center>

您可以看到叢集資訊清單中定義的容量︰

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="DiskInMb" Value="512000"/>
      </Capacities>
    </NodeType>
```

獨立部署透過 ClusterConfig.json，Azure 託管叢集透過 Template.json。 在叢集的 Azure Resource Manager 範本中，節點類型名稱之類的項目很可能參數化，看起來就像 "[parameters('vmNodeType2Name')]"，而不是 "NodeType02"。

```json
"nodeTypes": [
    {
        "name": "NodeType02",
        "capacities": {
            "MemoryInMb": "2048",
            "DiskInMb": "512000"
        }
    }
],
```

服務的負載也可能 (事實上很常見) 動態變更。 假設複本的負載從 64 變成 1024，但它執行所在的節點只剩下 512 (在 "MemoryInMb" 計量中)。 現在，該複本或執行個體的放置已無效，因為該節點上沒有足夠的空間。 如果該節點上的複本和執行個體使用量合計超出該節點的容量，也可能發生這種情形。 在任一情況下，叢集資源管理員必須介入讓節點回到容量以下。 它會將該節點上的一或多個複本或執行個體移至不同的節點。 移動複本時，叢集資源管理員會嘗試以最低成本來進行這些移動。 [這篇文章](service-fabric-cluster-resource-manager-movement-cost.md)討論移動成本。

## <a name="cluster-capacity"></a>叢集容量
那麼，我們要如何防止整體叢集太滿？ 透過動態負載，叢集資源管理員不需要做太多工作。 服務的負載可能突然增加，而無視於叢集資源管理員所採取的動作。 因此，您的叢集今天有充足的空餘空間，明天就可能因為出名而後繼無力。 話雖如此，但有一些現成的控制可防止基本問題。 我們可以做的第一件事是防止建立新的工作負載，該工作負載會導致叢集空間變滿。

假設您要建立無狀態服務，而且它有一些相關聯的負載 (稍後再詳述預設和動態負載)。 假設服務很注重 "DiskSpaceInMb" 計量。 另外也假設服務的每個執行個體會耗用五個單位的 "DiskSpaceInMb"。 您想要建立服務的三個執行個體。 太棒了！ 這表示叢集中需要有 15 個單位的 "DiskSpaceInMb"，我們才能建立這些服務執行個體。 叢集資源管理員會持續計算每個計量的整體容量和耗用量，因此可以輕易地判斷叢集中是否有足夠的空間。 如果沒有足夠的空間，叢集資源管理員會拒絕建立服務呼叫。

由於只是要求有 15 個單位可用，有許多不同方式可配置此空間。 例如，15 個不同節點上可能剩餘一個單位的容量，或五個不同節點上剩餘三個單位的容量。 只要叢集資源管理員能夠重新安排讓三個節點上有五個單位可用，最後就會放置此服務。 這種重新安排幾乎都沒問題，除非整個叢集幾乎全滿、服務全都非常「龐大」，或同時有這兩種情況。

## <a name="buffered-capacity"></a>緩衝處理的容量
叢集資源管理員還有一項特色可協助管理整體叢集容量的，就是對每個節點指定的容量加入一些保留緩衝量的概念。 「緩衝容量」可保留整體節點容量的一部分，只在升級和節點失敗期間用來放置服務。 現在都透過叢集定義，為所有節點整體指定每個計量的緩衝區。 您挑選的保留容量值取決於叢集中的容錯網域和升級網域數目，以及您想要多少額外負荷。 容錯網域和升級網域越多，表示您可以挑選較小的緩衝容量。 如果您有較多網域，則在升級和失敗期間，無法使用的叢集部分當然會比較少。 指定緩衝區百分比時，必須同時指定計量的節點容量，這樣才有意義。

以下是指定緩衝處理容量的方式：

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

獨立部署透過 ClusterConfig.json，Azure 託管叢集透過 Template.json：

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "DiskSpace",
          "value": "0.10"
      },
      {
          "name": "Memory",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

當叢集耗盡計量的緩衝容量時，建立新的服務會失敗。 這可確保叢集保留足夠的備用額外負荷，不會因為升級和失敗而造成節點超出容量。 緩衝容量是選擇性，但建議用在已定義計量容量的任何叢集。

叢集資源管理員會透過 PowerShell 和查詢 API 公開這項資訊。 這可讓您查看叢集內使用中的每個計量的緩衝容量設定、總容量和目前的耗用量。 我們可以看到該輸出的範例如下︰

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>後續步驟
* 如需叢集資源管理員內的架構和資訊流程的相關資訊，請參閱[這篇文章](service-fabric-cluster-resource-manager-architecture.md)
* 定義重組度量是合併 (而不是擴增) 節點上負載的一種方式。 若要了解如何設定重組，請參閱 [這篇文章](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 從頭開始，並 [取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)
* 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於 [平衡負載](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

