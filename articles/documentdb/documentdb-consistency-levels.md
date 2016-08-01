<properties
	pageTitle="DocumentDB 中的一致性層級 | Microsoft Azure"
	description="DocumentDB 具有四個一致性層級，有助於在最終一致性、可用性和延遲的取捨之間取得平衡。"
	keywords="最終一致性, eventual consistency, documentdb, azure, Microsoft azure"
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="mimig"/>

# DocumentDB 中的一致性層級

Azure DocumentDB 是考量到全球發佈的全新設計。它的設計目的是提供可預測的低延遲保證、99.99% 的可用性 SLA，以及多個定義完善且寬鬆的一致性層級模型。DocumentDB 目前提供四種一致性層級：強式、限定過期、工作階段和最終。除了**強式**和**最終一致性**模型通常是由其他 NoSQL 資料庫所提供，DocumentDB 也提供兩個已仔細編碼且可運作的一致性模型 (**限定過期**和**執行階段**)，並已根據真實世界的使用案例驗證過它們的實用性。總言之，這些四個一致性層級可讓您在一致性、可用性與延遲三者間做出合理取捨。

## 一致性的範圍

已將一致性的規模範圍限制為單一使用者要求。寫入要求可能會對應至插入、取代、更新插入或刪除交易 (不論是否會執行相關聯的前置或後置觸發程序)。或者，寫入要求可能會對應至 JavaScript 預存程序的交易執行，而該預設程序是在分割區內的多份文件上運作。如同寫入，讀取/查詢交易也會將範圍限制為單一使用者要求。使用者可能需要在大型結果集 (橫跨多個分割區) 上編頁，但是每一個讀取交易的範圍都會限制為單一頁面，並從單一分割區中提供服務。

## 一致性層級

您可以設定資料庫帳戶的「預設一致性層級」，以套用至資料庫帳戶底下 (所有資料庫) 的所有集合。所有對使用者定義的資源發出的讀取和查詢，預設都會使用資料庫帳戶上所指定的預設一致性層級。不過，您可以指定 [[x-ms-consistency-level]](https://msdn.microsoft.com/library/azure/mt632096.aspx) 要求標頭，來放寬特定讀取/查詢要求的一致性層級。DocumentDB 複寫通訊協定支援四種類型的一致性層級，可在特定一致性保證和效能之間明確進行取捨，如下所述。

![DocumentDB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇][1]

**重要事項**：

- 強式一致性利用保證傳回文件最新版本的讀取來提供[線性化](https://aphyr.com/posts/313-strong-consistency-models)保證。
- 「增強式」一致性保證只有在複本的多數仲裁持久認可寫入之後，才會顯示寫入。寫入不是會被主要和次要仲裁同步地持久認可，就是會被中止。讀取的認可一律是交由多數讀取仲裁來負責；用戶端絕不會看到未認可或不完整的寫入，而且一律保證會讀取最新認可的寫入。
- 設定為使用強式一致性的 DocumentDB 帳戶，無法將一個以上的 Azure 區域關聯至它們的 DocumentDB 帳戶。
- 具有強式一致性的讀取作業成本 (就取用的[要求單位](documentdb-request-units.md)而言) 會高於工作階段和最終，但與限定過期相同。
 

**限定過期**：

- 限定過期一致性保證讀取可能會落後寫入最多 *K* 個文件版本或首碼或是 *t* 個時間間隔。
- 因此，選擇限定過期時，可以兩種方式來設定「過期」：
    - 讀取落後寫入的文件版本數目 *K*
    - 時間間隔 *t*
- 限定過期提供全域訂單總數，但「過期期間」內的除外。請注意，在區域內，「過期期間」內外都有單純的讀取保證存在。
- 限定過期可提供比工作階段或最終一致性還要強大的一致性保證。針對全球發佈的應用程式，如果您想要強式一致性但也想要 99.99% 的可用性和低度延遲，我們建議您使用限定過期。
- 使用限定過期一致性設定的 DocumentDB 帳戶可以將任意數目的 Azure 區域關聯至它們的 DocumentDB 區域。
- 具有強式一致性的讀取作業成本 (就取用的 RU 而言) 會高於工作階段和最終一致性，但與強式一致性相同。

**工作階段**：

- 與強式和限定過期一致性層級所提供的全域一致性模型不同，工作階段一致性範圍會限制為用戶端工作階段。
- 工作階段一致性適用於所有牽涉到裝置或使用者工作階段的案例，因為它保證單純讀取、單純寫入和讀取您自己的寫入 (RYW) 保證。
- 工作階段一致性可為工作階段提供可預測的一致性，以及在提供最低延遲的寫入和讀取時提供最高的讀取輸送量。
- 使用工作階段一致性設定的 DocumentDB 帳戶可以將任意數目的 Azure 區域關聯至它們的 DocumentDB 區域。
- 具有工作階段一致性的讀取作業成本 (就取用的 RU 而言) 會低於強式和限定過期，但高於最終一致性。
 

**最終**：

- 最終一致性保證之後如果沒有再收到任何寫入，群組內的複本最後會只剩一個。
- 最終一致性是最差的一致性形式，用戶端可能會取得比之前所看到的值還要舊的值。
- 「最終一致性」提供最差的讀取一致性，但是讀取和寫入的延遲皆是最低。
- 使用最終一致性設定的 DocumentDB 帳戶可以將任意數目的 Azure 區域關聯至它們的 DocumentDB 區域。
- 具有最終一致性的讀取作業成本 (就取用的 RU 而言) 是所有 DocumentDB 一致性層級中最低的。


## 一致性保證

下表會擷取對應到這四個一致性層級的各種一致性保證。

| 保證 | 增強式 | 界限-陳舊 | 工作階段 | 最終 |
|----------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------------------------------|--------------------------------------------------|
| **全域訂單總數** | 是 | 是，在「過期期間」以外 | 否，部分的「工作階段」訂單 | 否 |
| **一致的首碼保證** | 是 | 是 | 是 | 是 |
| **單純讀取** | 是 | 是，橫跨位於過期期間之外但一直在區域內的區域。 | 是，適用於指定的工作階段 | 否 |
| **單純寫入** | 是 | 是 | 是 | 是 |
| **讀取您的寫入** | 是 | 是 | 是 (在寫入區域中) | 否 |


## 設定預設一致性層級

1.  在 [Azure 入口網站](https://portal.azure.com/)的動態工具列中，按一下 [DocumentDB 帳戶]。

2. 在 [DocumentDB 帳戶] 刀鋒視窗中，選取要修改的資料庫帳戶。

3. 在 [帳戶] 刀鋒視窗中，如果 [所有設定] 刀鋒視窗尚未開啟，按一下最上方命令列中 [設定] 圖示。

4. 在 [所有設定] 刀鋒視窗中，按一下 [功能] 下的 [預設一致性] 項目。

	![此螢幕擷取畫面反白顯示 [設定] 圖示和 [預設一致性] 項目](./media/documentdb-consistency-levels/database-consistency-level-1.png)

5. 在 [預設一致性] 刀鋒視窗中，選取新的一致性層級，然後按一下 [確定]。

	![此螢幕擷取畫面反白顯示 [一致性] 層次和 [確定] 按鈕](./media/documentdb-consistency-levels/database-consistency-level-2.png)

## 查詢的一致性層級

根據預設，針對使用者定義的資源，查詢的一致性層級會與讀取的一致性層級相同。每次在集合中插入、取代或刪除文件時，預設會同步更新索引。這個行為讓查詢能夠使用與文件的讀取相同的一致性層級。雖然 DocumentDB 的寫入已經過最佳化處理，並支援持續的文件寫入數量、同步索引維護，以及提供一致的查詢，但您還是可以設定特定集合，讓集合的索引更新速度變慢。讓索引速度變慢可提升寫入效能，而且適合用於工作負載主要是進行大量讀取的大量擷取案例。

索引模式|	讀取|	查詢  
-------------|-------|---------
一致 (預設)|	從強式、限定過期、工作階段或最終等選項中選取|	從強式、限定過期、工作階段或最終等選項中選取|
緩慢|	從強式、限定過期、工作階段或最終等選項中選取|	最終  

和讀取要求相同，您可以指定 [x-ms-consistency-level](https://msdn.microsoft.com/library/azure/mt632096.aspx) 要求標頭，來降低特定查詢要求的一致性層級。

## 後續步驟

如果您想要詳細了解一致性層級和取捨，我們建議下列資源：

-	Doug Terry。透過棒球 (影片) 來解說複寫的資料一致性。[https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
-	Doug Terry。透過棒球來解說複寫的資料一致性。[http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry。弱式一致複寫資料的工作階段保證。[http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi。現代分散式資料庫系統設計的一致性取捨：CAP 是整個過程中的唯一解決方案」。[http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-	Peter Bailis、Shivaram Venkataraman、Michael J. Franklin、Joseph M. Hellerstein、Ion Stoica。實際局部仲裁的可能界限-陳舊 (PBS)。[http://vldb.org/pvldb/vol5/p776\_peterbailis\_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels。再論最終一致。[http://allthingsdistributed.com/2008/12/eventually\_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)


[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png

<!---HONumber=AcomDC_0720_2016-->