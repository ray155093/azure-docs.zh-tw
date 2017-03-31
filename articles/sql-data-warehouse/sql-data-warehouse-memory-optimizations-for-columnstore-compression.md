---
title: "改善 Azure SQL 中的資料行存放區索引效能 | Microsoft Docs"
description: "減少記憶體需求或增加可用的記憶體，以最大化壓縮到每個資料列群組之資料行存放區索引的資料列數目。"
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/18/2016
ms.author: shigu;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8d189256ed4c876859203406cda95ce0be36c96c
ms.lasthandoff: 03/29/2017


---

# <a name="memory-optimizations-for-columnstore-compression"></a>資料行存放區壓縮的記憶體最佳化

減少記憶體需求或增加可用的記憶體，以最大化壓縮到每個資料列群組之資料行存放區索引的資料列數目。  使用這些方法來改善壓縮率和查詢資料行存放區索引的效能。

## <a name="why-the-rowgroup-size-matters"></a>為什麼資料列群組很重要
因為資料行存放區索引會藉由掃描個別資料列群組的資料行區段來掃描資料表，最大化每個資料列群組的資料列數目可以提升查詢效能。 當資料列群組會有大量的資料列時，可改善資料壓縮，這表示從磁碟讀取的資料比較少。

如需關於資料列群組的詳細資訊，請參閱[資料行存放區索引指南](https://msdn.microsoft.com/library/gg492088.aspx)。

## <a name="target-size-for-rowgroups"></a>資料列群組的目標大小
為了達到最佳查詢效能，目標是最大化存放區索引中每個資料行的資料列數目。 一個資料列群組最多可有 1,048,576 個資料列。 每個資料列群組沒有資料列數目上限也沒關係。 當資料列群組擁有至少 100,000 個資料列時，資料行存放區索引會獲得良好效能。

## <a name="rowgroups-can-get-trimmed-during-compression"></a>資料列群組可以在壓縮期間進行修剪

在大量載入或資料行存放區索引重建期間，有時可用的記憶體不足，無法壓縮指定給每個資料列群組的所有資料列。 當有記憶體不足的壓力時，資料行存放區索引會修剪資料列群組大小，因此會成功壓縮到資料行存放區內。

當記憶體不足，無法將至少 10,000 個資料列壓縮到每個資料列群組時，SQL 資料倉儲會產生錯誤。

如需有關大量載入的詳細資訊，請參閱[大量載入叢集資料行存放區索引](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index)。

## <a name="how-to-estimate-memory-requirements"></a>如何估計記憶體需求

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

要壓縮一個資料列群組所需的最大記憶體大約是

- 72 MB +
- \#資料列 \* \#資料行 \* 8 個位元組 +
- \#資料列 \* \#短字串資料行 \* 32 個位元組 +
- \#長字串資料行 \* 壓縮字典 16 MB

短字串資料行使用 < = 32 個位元組的字串資料類型和長字串資料行使用 > 32 個位元組的字串資料類型。

會使用專為壓縮文字的壓縮方法來壓縮長字串。 這個壓縮方法會使用字典來儲存文字模式。 字典的大小上限為 16 MB。 資料列群組中的每一個長字串資料行只有一個字典。

如需資料行存放區記憶體需求的深入討論，請參閱影片 [Azure SQL 資料倉儲調整︰組態和指引](https://myignite.microsoft.com/videos/14822)。

## <a name="ways-to-reduce-memory-requirements"></a>減少記憶體需求的方式

使用下列技巧來減少記憶體需求，以將資料列群組壓縮到資料行存放區索引。

### <a name="use-fewer-columns"></a>使用較少的資料行
可能的話，設計較少資料行的資料表。 當資料列群組壓縮至資料行存放區內時，資料行存放區索引會個別壓縮每個資料行區段。 因此，要壓縮資料列群組的記憶體需求會隨資料行數的增加而增加。


### <a name="use-fewer-string-columns"></a>使用較少的字串資料行
字串資料類型的資料行比數字和日期資料類型需要更多的記憶體。 若要減少記憶體需求，請考慮從事實資料表中移除字串資料行，並將其放在較小的維度資料表中。

字串壓縮的其他記憶體需求︰

- 最多 32 個字元的字串資料類型可能每個值需要 32 個額外的位元組。
- 使用超過 32 個字元的字串資料類型會使用字典方法進行壓縮。  資料列群組中的每個資料行可能需要建置額外的 16 MB 建置字典。

### <a name="avoid-over-partitioning"></a>避免過度分割

資料行存放區索引針對每個資料分割會建立一個或多個資料列群組。 在 SQL 資料倉儲中，資料分割數會快速成長，因為資料散發且會分割每個散發。 如果資料表有太多資料分割，則可能沒有足夠的資料列可填滿資料列群組。 缺少的資料列在壓縮期間不會建立記憶體不足的壓力，但這會造成無法達到最佳資料行存放區的資料列群組查詢效能。

要避免過度磁碟分割的另一個原因，是有額外負荷的記憶體將資料列載入分割資料表上的資料行存放區索引。 在載入期間，許多資料分割可能會收到內送資料列並保留在記憶體中，直到每個分割區有足夠的資料列可壓縮為止。 具有太多資料分割會建立額外的記憶體不足壓力。

### <a name="simplify-the-load-query"></a>簡化載入查詢

資料庫會共用在查詢中所有運算子之間的查詢記憶體授權。 當載入查詢具有複雜的排序和聯結時，可供壓縮的記憶體便會減少。

設計負載查詢以僅著重於載入查詢。 如果您需要在資料上執行轉換，從載入查詢個別執行它們。 例如，接移堆積資料表中的資料、執行轉換，然後將暫存資料表載入資料行存放區索引。 您可以也先載入資料，然後使用 MPP 系統來轉換資料。

### <a name="adjust-maxdop"></a>調整 MAXDOP

當有多個 CPU 核心可供每個散發使用時，每個散發會將資料列群組平行壓縮到資料行存放區。 平行處理原則需要額外的記憶體資源，可能會導致記憶體不足的壓力和調整資料列群組。

若要減少記憶體不足的壓力，您可以使用 MAXDOP 查詢提示來強制載入作業，以便在每個散發內的序列模式中執行。

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>配置更多記憶體的方式

DWU 大小和使用者資源類別會共同判斷有多少記憶體可供使用者查詢。 若要增加負載查詢的記憶體授權，您可以增加 DWU 數目或增加資源類別。

- 若要增加 DWU，請參閱[如何調整效能？](sql-data-warehouse-manage-compute-overview.md#scale-compute)
- 若要變更查詢的資源類別，請參閱[變更使用者資源類別的範例](sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example)。

例如，在 DWU 100 上，smallrc 資源類別中的使用者每個散發可以使用 100 MB 的記憶體。 如需詳細資訊，請參閱 [SQL 資料倉儲中的並行存取](sql-data-warehouse-develop-concurrency.md)。

假設您決定您需要 700 MB 的記憶體，以取得高品質的資料列群組大小。 下列範例會示範如何使用足夠的記憶體執行載入查詢。

- 使用 DWU 1000 和 mediumrc，記憶體授權為 800 MB
- 使用 DWU 600 和 largerc，記憶體授權為 800 MB。


## <a name="next-steps"></a>後續步驟

若要尋找更多在 SQL 資料倉儲中改善效能的方法，請參閱[效能概觀](sql-data-warehouse-overview-manage-user-queries.md)。

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

