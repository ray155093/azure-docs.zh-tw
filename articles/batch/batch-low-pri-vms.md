---
title: "在符合成本效益的低優先順序 VM 上執行 Azure Batch 工作負載 (預覽) | Microsoft Docs"
description: "了解如何佈建低優先順序的 VM，降低 Azure Batch 工作負載的成本。"
services: batch
author: mscurrell
manager: timlt
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 07/21/2017
ms.author: markscu
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 9bf0ac322020d8a8453011c3207c1930175db6d3
ms.contentlocale: zh-tw
ms.lasthandoff: 07/24/2017

---

# <a name="use-low-priority-vms-with-batch-preview"></a>使用低優先順序的 VM 搭配 Batch (預覽)

Azure Batch 提供低優先權的虛擬機器 (VM)，可降低 Batch 工作負載的成本。 低優先順序的 VM 能提供大量的計算能力，同時也是經濟實惠的選擇，從而實現新的 Batch 工作負載類型。

低優先順序的 VM 能善用 Azure 中的剩餘容量。 當您指定集區中的低優先順序 VM 時，Azure Batch 就會在有多餘的容量時自動加以使用。

使用低優先順序 VM 的權衡取捨，是當 Azure 中沒有多餘的容量時，可以將這些 VM 優先佔用。 基於這個理由，低優先順序的 VM 最適合特定類型的工作負載。 低優先順序的 VM 是用於批次和非同步處理的工作負載，這種工作負載的作業完成時間很有彈性，且工作會分散於許多 VM。

低優先順序 VM 的成本遠低於專用 VM。 如需定價詳細資料，請參閱 [Batch 定價](https://azure.microsoft.com/pricing/details/batch/)。

如需低優先順序 VM 的其他討論，請參閱部落格文章公告：[依價格分數的 Batch 計算](https://azure.microsoft.com/blog/announcing-public-preview-of-azure-batch-low-priority-vms/)。

> [!IMPORTANT]
> 低優先順序的 VM 目前在預覽中，僅適用於 Batch 中執行的工作負載。 
>
>

## <a name="use-cases-for-low-priority-vms"></a>低優先順序 VM 的使用案例

如果有低優先順序的 VM 的特性，哪些工作負載可以使用和無法使用它們？ 一般情況下，批次處理工作負載就很適合，因為作業會區分成許多平行的工作，或是會將許多作業相應放大並分散於許多 VM。

-   若要充分使用 Azure 中的剩餘容量，可將適當的作業相應放大。

-   VM 偶爾可能會無法使用或被優先佔用，這會導致作業容量降低，且可能會導致工作中斷及重新執行。 因此，作業在可使用的時間內必須是有彈性的，才會將作業加以執行。

-   如果工作較長的作業受到中斷，可能就會影響較大。 如果長時間執行的工作在執行時實作檢查點來儲存進度，那麼中斷所造成的影響就會大幅降低。 時間執行較短的工作通常最適合低優先順序的 VM，因為中斷的影響會大幅降低。

-   利用多個 VM 長時間執行的 MPI 作業並不太適合使用低優先順序的 VM，因為一個優先佔用的 VM 有可能會導致整個作業必須重新執行一次。

適用於低優先順序 VM 的一些批次處理使用案例的範例如下︰

-   **開發與測試**︰特別是，如果您正在開發大規模的解決方案，就可實現可觀的成本節省。 所有的測試類型都能有所助益，但大規模的負載測試及迴歸測試都是很棒的用途。

-   **補充隨選容量**︰低優先順序的 VM 可用來補充一般的專用 VM - 在可使用時，作業就能加以調整並從而以較低的成本加速完成；在無法使用時，就能使用專用 VM 的基準。

-   **彈性的作業執行時間**︰如果作業完成所需的時間有彈性，就能容許可能的容量下降；但新增了低優先順序的 VM 後，作業就會經常更快速執行，且成本更低。

有幾種方法可將 Batch 集區設為使用低優先順序的 VM，視作業執行時間的彈性而定︰

-   低優先順序的 VM 可以單獨用於集區中，Batch 只會在容量可用時，將任何佔用的容量復原。 這是執行作業最便宜的方式，因為只會使用低優先順序的 VM。

-   低優先順序的 VM 可以用來搭配專用 VM 的固定基準。 固定的專用 VM 數目可確保一律會有一些容量可保持作業進度。

-   可以將專用 VM 和低優先順序 VM 動態混用，如此就能在可用時單獨使用低成本低優先順序的 VM，但會視需要將原定價格的專用 VM 相應增加，讓可用的容量維持在最低量，從而保持作業進度。

## <a name="batch-support-for-low-priority-vms"></a>Batch 支援低優先順序的 VM

Azure Batch 提供多項功能，能讓使用者輕鬆使用及受益於低優先順序的 VM：

-   Batch 集區可以同時包含專用 VM 和低優先順序的 VM。 您可以使用明確的調整大小作業或使用自動調整，在集區建立時指定每種類型的 VM 數目，或是針對現有集區隨時，將每種類型的 VM 數目加以變更。 作業和工作提交可維持不變，且無需擔心集區中的 VM 類型。 此外，也能盡可能以便宜的方式，讓集區完全使用低優先順序的 VM 來執行作業，但如果容量低於最小臨界值，就會運轉專用 VM，從而保持作業執行。

-   Batch 集區會自動搜尋低優先順序 VM 的目標數目。 如果 VM 被優先佔用，Batch 就會嘗試取代遺失的容量並回到目標。

-   在工作中斷的情況下，Batch 會偵測並自動將要再次執行的工作重新排入佇列。

-   低優先順序 VM 具有核心配額，不同於專用 VM 的核心配額。 
    低優先順序 VM 的配額高於專用 VM 的核心配額，因為低優先順序 VM 的成本較低。 如需詳細資訊，請參閱 [Batch 服務配額和限制](batch-quota-limit.md#resource-quotas)。    

> [!NOTE]
> Batch 帳戶中的集區配置模式是設定為[使用者訂用帳戶](batch-account-create-portal.md#user-subscription-mode)，目前不支援低優先順序的 VM。
>
>

## <a name="create-and-update-pools"></a>建立和更新集區

Batch 集區可以同時包含專用 VM 和低優先順序的 VM (亦稱為計算節點)。 您可以為專用 VM 和低優先順序的 VM 設定計算節點的目標數目。 節點的目標數目會指定您在集區中想要的 VM 數目。

例如，使用目標為 5 個專用 VM 和 20 個低優先順序 VM 的 Azure 雲端服務來建立集區：

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

使用目標為 5 個專用 VM 和 20 個低優先順序 VM 的 Azure 虛擬機器 (在此情況下為 Linux VM) 來建立集區：

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04.0-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard\_D2\_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

您可以取得專用 VM 和低優先順序 VM 目前的節點數目：

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

集區節點的屬性可表示節點為專用的還是低優先順序的 VM：

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

當集區中的一個或多個節點被優先佔用時，集區上的清單節點作業仍會傳回這些節點，而目前的低優先順序節點數目將保持不變，但這些節點會將其狀態設定為**優先佔用**狀態。 Batch 會嘗試尋找取代 VM，如果成功，節點在變成可供工作執行前，會逐步進行**建立**和**啟動**狀態，就像新的節點一樣。

## <a name="scale-a-pool-containing-low-priority-vms"></a>調整包含低優先順序 VM 的集區

如同由專用 VM 單獨組成的集區，可藉由呼叫調整大小方法或使用自動調整，將包含低優先順序 VM 的集區進行調整。

調整集區大小的作業會採用第二個選擇性參數，可更新 **targetLowPriorityNodes** 的值：

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

集區自動調整公式會支援低優先順序的 VM，如下所示︰

-   您可以取得或設定服務定義之 **$TargetLowPriorityNodes** 變數的值。

-   您可以取得服務定義之 **$CurrentLowPriorityNodes** 變數的值。

-   您可以取得服務定義之 **$PreemptedNodeCount** 變數的值。 
    此變數會傳回優先佔用狀態中的節點數目，並可依無法使用的優先佔用節點數目，將您的專用節點數目相應增加或相應減少。

## <a name="jobs-and-tasks"></a>工作 (Job) 和工作 (Task)

作業和工作幾乎都不需要低優先順序節點的支援；唯一的支援如下所示︰

-   作業的 JobManagerTask 屬性都有新的屬性，**AllowLowPriorityNode**。 
    當這個屬性為 true 時，就可以在專用節點或低優先順序的節點上排程作業管理員工作。 如果這個屬性為 false，就只會將作業管理員工作將排到專用節點。

-   [環境變數](batch-compute-node-environment-variables.md)可供工作應用程式使用，因此它可以判斷是在低優先順序還是專用節點上執行。 環境變數是 AZ_BATCH_NODE_IS_DEDICATED。

## <a name="handling-preemption"></a>處理優先佔用

VM 可能偶爾會被優先佔用；當這個情況發生時，Batch 會執行下列動作︰

-   優先佔用的 VM 都會將其狀態更新為**優先佔用**。
-   如果工作是在優先佔用的節點 VM 上執行，就會將這些工作重新排入佇列並再次執行。
-   VM 將會有效地刪除，導致本機儲存在上 VM 的任何資料遺失。
-   集區會繼續嘗試觸達可用的低優先順序節點之目標數目。 找到取代容量時，節點會保留其識別碼，但在可供工作排程使用之前，會先重新初始化，逐步進行**建立**和**啟動**狀態。
-   優先佔用計數會在 Azure 入口網站中作為計量提供使用。

## <a name="metrics"></a>度量

[Azure 入口網站](https://portal.azure.com)中有針對低優先順序節點提供的新計量。 這些計量包括：

- 低優先順序節點計數
- 低優先順序核心計數 
- 先占節點計數

若要檢視 Azure 入口網站中的計量：

1. 在入口網站中瀏覽至您的 Batch 帳戶，並檢視 Batch 帳戶的設定。
2. 從 [監視] 區段選取 [計量]。
3. 從 [可用的計量] 清單中選取您所需的計量。

![低優先順序節點的計量](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>後續步驟

* 請參閱 [適用於開發人員的 Batch 功能概觀](batch-api-basics.md)，這是任何準備使用 Batch 的人員不可或缺的資訊。 本文包含 Batch 服務資源 (例如集區、節點、作業和工作) 的詳細資訊，以及在建置 Batch 應用程式時可使用的許多 API 功能。
* 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。

