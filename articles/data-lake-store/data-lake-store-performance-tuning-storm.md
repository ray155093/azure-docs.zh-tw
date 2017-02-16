---
title: "Azure Data Lake Store Storm 效能微調方針 | Microsoft Docs"
description: "Azure Data Lake Store Storm 效能微調方針"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: ebf876f946eceddce9c8c990d8b28fcb969bec23
ms.openlocfilehash: 112226028c053cc91f9fb2bc0e5978f7cb2343ed


---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>HDInsight 和 Azure Data Lake Store 上的 Storm 效能微調方針

在微調 Storm 拓撲的效能時，有幾項必須考量的因素。  請務必了解由 Spout 和 Bolt 所完成之工作 (不論是 I/O 密集或記憶體密集工作) 的特性。

## <a name="prerequisites"></a>必要條件 

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。 
* **Azure 資料湖儲存區帳戶**。 如需有關如何建立帳戶的詳細指示，請參閱 [開始使用 Azure 資料湖儲存區](data-lake-store-get-started-portal.md) 
* **Azure HDInsight 叢集** 。 請參閱 [建立具有 Data Lake Store 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確實為叢集啟用遠端桌面。 
* **在 Azure Data Lake Store 上執行 Storm 叢集**。  如需詳細資訊，請參閱 [HDInsight 上的 Storm](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview) 
* **ADLS 的效能微調指導方針**。  如需一般的效能概念，請參閱 [Data Lake Store 效能微調指導方針](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

**調整拓撲的平行處理原則**

增加 Azure Data Lake Store 的往返 I/O 並行能力，或許能獲得更好的效能。  
Storm 拓撲有一組可決定平行處理原則的組態︰
* 背景工作處理序數目︰背景工作會平均分散給 VM。
* Spout 執行程式執行個體數目
* Bolt 執行程式執行個體數目
* Spout 工作數目
* Bolt 工作數目

例如，在具有 4 個 VM 和 4 個背景工作處理序、32 個 Spout 執行程式和 32 個 Spout 工作、256 個 Bolt 執行程式和 512 個 Bolt 工作的叢集上︰

每個監督員 (背景工作節點) 會有一個背景工作 JVM 處理序，負責管理 4 個 Spout 執行緒和 64 個 Bolt 執行緒。 每個執行緒中的工作會循序執行。 在上述組態中，每個 Spout 執行緒會有一個工作，而每個 Bolt 執行緒會有 2 個工作。

在 Storm 中，以下是所涉及的各種元件，以及它們會如何影響您所擁有的平行處理原則層級︰
* 前端節點 (在 Storm 中稱為 Nimbus) 可用來提交和管理作業。 這些節點不會影響平行處理原則的程度。
* 監督員節點 – 在 Azure HDInsight 中，這會對應至背景工作節點 Azure VM。
* 背景工作的工作是在 VM 中執行的 Storm 處理序。 每個背景工作的工作會對應至 Java JVM 執行個體。 Storm 會盡量地將您指定的背景工作處理序數目平均分散給背景工作節點。
* Spout 和 Bolt 執行程式執行個體︰每個執行程式執行個體會對應至在背景工作 (JVM) 內執行的執行緒
* Storm 工作︰這些工作是每個執行緒所執行的邏輯工作。 這不會變更平行處理原則的層級，因此您應該評估每個執行程式是否需要多個工作。

## <a name="guidance"></a>指引

在使用 Azure Data Lake 時，如果您執行下列作業，就能獲得最佳效能︰
* 將小型附加項目聯合成為較大的大小 (最佳大小是 4 MB)
* 盡可能提出最多的並行要求。 因為每個 Bolt 執行緒都會進行區塊讀取，您會想要讓每個核心擁有 8-12 個左右的執行緒，以便讓 NIC 和 CPU 受到充分利用。  較大的 VM 可允許更多的並行要求。  

## <a name="example"></a>範例

假設您擁有 8 個背景工作節點的叢集，叢集中具有 D13v2 Azure VM。  D13v2 VM 有 8 個核心，因此在 8 個背景工作節點之間總共會有 64 個核心。

假設我們讓每個核心執行 8 個 Bolt 執行緒。 若給定 64 個核心，這表示我們總共需要 512 個 Bolt 執行程式執行個體 (亦即執行緒)。 在此情況下，假設我們一開始讓每個 VM 擁有一個 JVM，並且大多在 JVM 內使用執行緒並行存取以達到並行效果。 這表示我們需要 8 個背景工作的工作 (每個 Azure VM 一個工作) 和 512 個 Bolt 執行程式。 若給定此組態，Storm 會嘗試將背景工作平均分散給背景工作節點 (也稱為監督員節點)，讓每個背景工作節點有一個 JVM。 現在在監督員內，Storm 會嘗試在監督員之間平均分散執行程式，讓每個監督員 (亦即 JVM) 各有 8 個執行緒。

## <a name="further-tuning"></a>進一步微調
在擁有基本拓撲之後，您可以考慮是否要調整任何參數︰
* **每個背景工作節點的 JVM 數目：**如果您擁有大型資料結構 (例如，您在記憶體中裝載的查閱資料表)，則每個 JVM 需要一份複本；相較之下，擁有較少的 JVM 可讓您使用跨多個執行緒的結構。 針對 Bolt 的 I/O，JVM 數目造成的差異，不如跨這些 JVM 所新增的執行緒數目。 為了簡單起見，我們建議每個背景工作一個 JVM，但根據 Bolt 所執行的工作或您所需的應用程式處理，您可能需要評估是否需要調整這個數字。
* **Spout 執行程式的數目︰**因為此處的範例使用 Bolt 來寫入至 Azure Data Lake，所以 Spout 的數目不會與 Bolt 的效能直接相關。 不過，根據 Spout 中發生的處理或 I/O 數量，您會想要微調 Spout 以獲得最佳效能。 至少，您會想要確定您有足夠的 Spout 能夠使 Bolt 保持忙碌，也就是說，Spout 的輸出速率應該符合 Bolt 的輸送量。 實際的組態取決於 Spout，而這並非本文的討論範圍。
* **工作數目︰**每個 Bolt 都會以單一執行緒的形式來執行。 每個 Bolt 的其他工作不會提供任何額外的並行能力。 如果認可 Tuple 的處理序會佔用 Bolt 大部分的執行時間，它們的優點才會浮現。 我們建議您先將多個 Tuple 群組為較大的附加項目，再傳送來自 Bolt 的認可，因此在大部分情況下，多個工作不會提供任何額外的好處。
* **本機或隨機群組︰**本設定啟用時，會將 Tuple 傳送至相同背景工作處理序內的 Bolt。 這可降低處理序間的通訊和網路呼叫。 這是大部分拓撲的建議作法。

做為起始的方法，我們建議您從基本案例著手，再以手上的資料進行測試來調整上述參數，以達到最佳效能。

## <a name="tuning-the-spout"></a>微調 Spout

**Tuple 逾時**

topology.message.timeout.secs – 此設定會決定訊息完成和接收認可的時間量，在此時間後，便會將訊息視為失敗。

**每個背景工作處理序的記憶體上限**

Worker.childopts - 此設定可讓您指定 Java 背景工作的其他命令列參數。 這裡最常使用的設定是 XmX，它會決定配置給 JVM 堆積的記憶體上限。

**Spout 暫止上限**

Topology.max.spout.pending - 此設定會決定任何時候每個 Spout 執行緒可以有的傳輸中 (尚未在拓撲中的所有節點上受到認可) Tuple 數目。

良好的計算方式是估計每個 Tuple 的大小。 然後找出一個 Spout 執行緒有多少記憶體。 配置給執行緒的記憶體總數除以這個值，應該就能得出最大 Spout 暫止參數的上限。

## <a name="tuning-the-bolt"></a>微調 Bolt
在寫入至 ADLS 時，建議將大小同步處理原則 (用戶端上的緩衝區) 設為 4 MiB。  當緩衝區大小是上述值時，才會接著執行排清或 hsync()。  背景工作 VM 上的 ADLS 驅動程式會自動執行此緩衝作業，除非使用者明確執行 hsync()。

預設 ADLS Storm Bolt 有一個大小同步處理原則參數 (fileBufferSize) 可用來調整此參數。

在 I/O 密集的拓撲中，建議每個 Bolt 執行緒寫入到自己的檔案中，並設定檔案輪替原則 (fileRotationSize)。  當檔案達到特定大小時，就會自動排清資料流，並在其中寫入新檔案。  建議的輪替檔案大小為 1 GB。

**何時認可︰**在 Storm 中，Spout 會控制住 Tuple，直到後者受到 Bolt 明確認可。  如果 Tuple 已被 Bolt 讀取，卻未受到認可，就表示無法保證 Bolt 已持續到 Azure Data Lake Store 後端。  一旦 Tuple 受到認可，Spout 就可由 Bolt 保證持續性，然後它就可以刪除來源資料，不論該資料是讀取自任何來源。  

**為了在 ADLS 上達到最佳效能：**建議您讓 Bolt 緩衝處理 4MB 的 Tuple 資料，然後再寫入至 ADLS 後端成為一個 4 MB 寫入。 一旦資料成功地寫入至存放區 (透過呼叫 hflush())，Bolt 就可以將資料認可回到 Spout。 此處提供的範例 Bolt 就是這種作法。 您也可以先保存較大量的 Tuple 再呼叫 hflush() 並認可 Tuple。 不過，這會增加 Spout 需要保存的傳輸中 Tuple 數目，因此會增加每個 JVM 需要的記憶體數量。

基於其他非效能理由，應用程式可能需要更加頻繁地認可 (在資料大小小於 4 MB 時)。 不過，這可能會影響儲存體後端的 I/O 輸送量，因此客戶應該針對此一缺點與 Bolt 的 I/O 效能進行仔細衡量。

如果 Tuple 的傳入速率不夠高，因此 4 MB 的緩衝區需要很久才能填滿，您或許可以考慮以幾種方式減輕此問題︰
* 減少 Bolt 數目，讓要填滿的 4 MB 緩衝區變少
* 設定以時間或計數為基礎的原則，其中 hflush() 每隔 x 次排清或每隔 y 毫秒就會觸發，並將累積到目前為止的 Tuple 認可回去。

請注意，輸送量在此情況下會降低，但在低事件速率下，反正最大輸送量不是我們的最大目標。  上述減輕措施可讓您減少 Tuple 流到存放區所需的端對端時間，如果即時面對低事件速率，您仍想要即時的管線，這可能就很重要。  也請注意，如果傳入的 Tuple 速率較低，您也會想要調整 topology.message.timeout_secs 參數，讓 Tuple 不會在進行緩衝處理或處理時逾時。

## <a name="interpreting-storm-ui"></a>解譯 Storm UI  
當您的拓撲在執行時，您可以在 Storm UI 中進行監視。 在查看 UI 時，以下是要查看的主要參數︰

* **總處理序執行延遲** – 這是一個 Tuple 由 Spout 發出、由 Bolt 處理並受到認可所花費的平均時間。

* **總 Bolt 處理序延遲** – 這是 Tuple 收到認可之前在 Bolt 所花費的平均時間。

* **總 Bolt 執行延遲** – 這是 Bolt 在 execute 方法所花費的平均時間。

* **失敗次數** – 這是指 Tuple 在逾時之前能夠無法完全處理的次數。

* **容量** – 這是系統忙碌程度的量值。 如果這個數字為 1，Bolt 會以它最快的速度工作。 如果小於 1，則會增加您的平行處理原則。 如果大於&1;，則會減少平行處理原則。

## <a name="common-troubleshooting-scenarios"></a>常見的疑難排解案例
* **大量 Tuple 逾時** – 查看拓撲中的每個節點，以判斷瓶頸所在。 最常見的原因是 Bolt 無法跟上 Spout，導致 Tuple 在等待處理時阻塞了內部緩衝區。 請考慮增加逾時值，或減少 Spout 暫止上限。

* **總處理序執行延遲很高，但 Bolt 處理序延遲卻很低** – 在此情況下，表示其中一個 Tuple 的速度不夠快。 請確認 acker 的數量足夠。 另一個可能的原因是，它們在佇列中等待很久之後，Bolt 才開始處理。 減少 Spout 暫止上限。

* **Bolt 執行延遲很高** – 這表示 Bolt 的 execute() 方法花費太久時間。 請將程式碼最佳化，或查看寫入大小/排清行為。

## <a name="limitation"></a>升級至 V12 
ADLS 節流︰如果您達到 ADLS 所提供的頻寬限制，您會開始看到工作失敗。 透過觀察工作記錄檔中的節流錯誤即可加以識別。  您可以藉由增加容器大小來減少平行處理原則。  如果您的作業需要更多並行能力，請與我們連絡。   
若要檢查您是否遭到節流，您必須在用戶端啟用偵錯記錄。 做法如下：

1. 在 [Ambari] > [Storm] > [設定] > [進階 storm-worker-log4j] 中變更下列項目。  將 &lt;root level="info"&gt; 變更為 &lt;root level=”debug”&gt;。  重新啟動所有節點/服務，以便讓設定生效。
2. 監視背景工作節點上的 Storm 拓撲記錄檔 (在 /var/log/storm/worker-artifacts/&lt;TopologyName&gt;/&lt;port&gt;/worker.log 下)，注意是否有 ADLS 節流例外狀況。

## <a name="additional-tuning"></a>其他微調
Storm 的其他效能微調請參考此[部落格](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/)。

## <a name="examples-to-run"></a>要執行的範例
請嘗試位於 [github](https://github.com/hdinsight/storm-performance-automation) 上的這個範例。



<!--HONumber=Jan17_HO2-->


