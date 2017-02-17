---
title: "什麼是 Azure 彈性集區？ | Microsoft Docs"
description: "使用集區管理數百或數千個資料庫。 針對一組效能單位的一個價格可以分散在集區。 您可以隨意將資料庫移入或移出。"
keywords: "彈性集區, sql 資料庫"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 02/01/2017
ms.author: ddove
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 65b7e72d2cf0afd57aebd2e5938b540b2a2b4f20
ms.openlocfilehash: a38e1bf40bde45d0122e07b50c748d57757635a5


---
# <a name="what-is-an-azure-sql-elastic-pool"></a>什麼是 Azure SQL 彈性集區？
SQL 彈性集區是 Azure 邏輯伺服器上固定數目的 eDTU (以固定價格提供)，而這些 eDTU 共用於該伺服器上的資料庫集區。 SQL DB 彈性集區提供符合成本效益的簡單解決方案，以管理多個不同且具備無法預測的使用模式資料庫的效能目標。

> [!NOTE]
> 彈性集區已在所有 Azure 區域中正式運作 (GA)，但印度西部除外，此區域目前提供預覽版。  我們將儘速在此區域提供彈性集區的 GA。
>
>

## <a name="how-it-works"></a>運作方式
常見的 SaaS 應用程式模式是單一租用戶資料庫模型︰每個客戶會被授與他們自己的資料庫。 每個客戶 (資料庫) 針對記憶體、IO 和 CPU，都有無法預期的資源需求。 由於需求有高有低，您該如何以有效率且符合成本效益的方式配置資源？ 您通常有兩個選項：(1) 根據尖峰使用量額外佈建資源並額外付款，或是 (2) 少量佈建來節省成本，但會降低在尖峰期間的效能和客戶滿意度。 彈性集區可藉由確保資料庫會在需要時取得它們所需的效能資源，來解決此問題。 它們會在可預測的預算內提供簡單的資源配置機制。 若要深入了解使用彈性集區的 SaaS 應用程式的設計模式，請參閱 [採用 Azure SQL Database 的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

在 SQL Database 中，資料庫處理資源需求的能力相對量值就單一資料庫而言是以「資料庫交易單位」(DTU) 表示，就彈性集區中的資料庫而言則是以彈性 DTU (eDTU) 表示。 如需深入了解 DTU 和 eDTU 的資訊，請參閱 [SQL Database 簡介](sql-database-technical-overview.md)。

集區以固定價格提供固定數目的 eDTU。 在集區內，會給予個別資料庫彈性以在設定的參數內自動調整。 負載量大時，資料庫可以取用更多的 eDTU 以滿足需求。 負載較輕的資料庫取用較少的 eDTU，而完全無負載的資料庫不會取用任何 eDTU。 針對整個集區佈建資源，而不是針對單一資料庫佈建資源，可簡化管理工作。 此外，您還可以有可預測的集區預算。

資料庫不必停機就可以在現有集區中新增額外的 eDTU，不過可能需要移動資料庫來為新的 eDTU 保留項目提供額外的計算資源。 同樣地，如果不再需要額外 eDTU，則隨時可以從現有集區中移除。

您也可以在集區加入或減少資料庫。 如果可以預測資料庫使用少量資源，則將它移出。

## <a name="which-databases-go-in-a-pool"></a>哪個資料庫會進入集區？
![在彈性集區中共用 eDTU 的 SQL 資料庫。][1]

最適合加入彈性集區的資料庫通常是有時活躍、有時閒置的資料庫。 在上述範例中，您可以看到單一資料庫的活動、4 個資料庫的活動，最後則是具有 20 個資料庫的彈性集區的活動。 活動隨時間而不同的資料庫很適合加入至彈性集區，因為它們不是永遠都在使用中，而且可以共用 eDTU。 並非所有資料庫都符合這個模式。 有些資料庫的資源需求比較固定，這類資料庫較適合基本、標準和進階服務層，在這些服務層中會個別指派資源。

[彈性集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>彈性集區的 eDTU 和儲存體限制

下表說明基本、標準和高階彈性集區的特性。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

如果彈性集區的所有 DTU 均已使用，則集區中的每個資料庫會收到等量的資源以處理查詢。  SQL Database 服務藉由確保運算時間的均等配量，提供資料庫之間的資源共用公平性。 彈性集區資源共用公平性不包括任何資源數量，否則當每個資料庫的最小 DTU 數設為非零的值時，便會對每個資料庫保證資源數量。

## <a name="elastic-pool-properties"></a>彈性集區屬性

下表說明彈性集區和集區資料庫的限制。

### <a name="limits-for-elastic-pools"></a>彈性集區的限制
| 屬性 | 說明 |
|:--- |:--- |
| 服務層 |Basic、Standard 或 Premium。 服務層決定可設定的效能和儲存體限制的範圍，以及商務持續性選項。 在集區內的每個資料庫都與集區具有相同的服務層。 「服務層」也稱為「版本」。 |
| 每集區 eDTU |可由集區中的資料庫共用的 eDTU 數目上限。 在相同的時間點，集區中的資料庫所使用的總 eDTU 數目不得超過此限制。 |
| 每集區最大儲存體 (GB) |可由集區中的資料庫所共用的儲存體數量上限 (GB)。 集區中的資料庫所使用的儲存體總數不得超過這個限制。 這個限制是由每個集區的 eDTU 所決定。 如果超過此限制，所有資料庫都會變成唯讀。 每個集區的儲存體上限是指集區中資料檔案的儲存體上限，並不包含記錄檔所使用的空間。 |
| 每個集區的資料庫數目上限 |每個集區允許的資料庫數目上限。 |
| 每個集區的並行背景工作數上限 |集區中所有資料庫可用的並行背景工作角色 (要求) 數目上限。 |
| 每集區的並行登入數上限 |集區中所有資料庫的並行登入數的 數目上限。 |
| 每集區並行工作階段數上限 |集區中所有資料庫可用的工作階段數目上限。 |

### <a name="limits-for-pooled-databases"></a>集區資料庫的限制
| 屬性 | 說明 |
|:--- |:--- |
| 每資料庫的 eDTU 上限 |集區中任何資料庫可以使用的 eDTU 數目上限，是否可用則是根據集區中其他資料庫的使用量而定。  每個資料庫的 eDTU 數目上限不等於資料庫的資源保證。  這個設定是全域設定，會套用至集區中的所有資料庫。 將每個資料庫的 eDTU 設定為最上限，以處理資料庫使用率的尖峰。 某種程度的過量使用是可預期的情況，因為集區通常會假設資料庫的熱門和冷門使用模式；在這些模式中，所有資料庫不會同時處於尖峰期。 例如，假設每個資料庫的尖峰使用量是 20 個 DTU，且集區中的 100 個資料庫只有 20% 會同時暴增到尖峰。  如果每一資料庫的 eDTU 上限設為 20 個 eDTU，則以 5 倍的量過量使用集區，並將每集區 eDTU 設為 400 個是合理的作法。 |
| 每資料庫的 eDTU 下限 |集區中單一資料庫能夠保證的最小 eDTU 數。  這個設定是全域設定，會套用至集區中的所有資料庫。 每個資料庫最小 eDTU 建議設定為 0，同時也是預設值。 此屬性會設為 0 到每一資料庫的 eDTU 使用量平均值之間的任意數。 集區中資料庫數目和每個資料庫 eDTU 數目下限的乘積不能超過每個集區的 eDTU。  例如，如果集區有 20 個資料庫，且每個資料庫的最小 eDTU 設定為 10 eDTU，則每個集區 eDTU 必須至少為 200 個 eDTU。 |
| 每個資料庫的儲存體上限 (GB) |集區中資料庫的儲存體上限。 集區資料庫共用集區儲存體，所以資料庫儲存體的大小會限制為較小的剩餘集區儲存體，以及每資料庫儲存體的上限。 每個資料庫的儲存體上限是指資料檔案的大小上限，並不包含記錄檔所使用的空間。 |

## <a name="elastic-jobs"></a>彈性工作
使用集區，只要在**[彈性作業](sql-database-elastic-jobs-overview.md)**中執行指令碼，就能簡化管理工作。 彈性作業會消除與大量資料庫相關聯的冗長工作。 若要開始，請參閱[開始使用彈性工作](sql-database-elastic-jobs-getting-started.md)。

如需可供使用多個資料庫之其他資料庫工具的詳細資訊，請參閱[使用Azure SQL Database 向上調整](sql-database-elastic-scale-introduction.md)。

## <a name="business-continuity-features-for-databases-in-a-pool"></a>集區中資料庫的業務續航力功能
集區資料庫通常會支援單一資料庫可用的相同[商務持續性功能](sql-database-business-continuity.md)。

### <a name="point-in-time-restore"></a>還原時間點
還原時間點會自動備份資料庫，以將集區中的資料庫復原到特定的時間點。 請參閱 [還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>異地還原
異地還原會在資料庫因裝載區域中的事件而無法使用時，提供預設復原選項。 請參閱 [還原 Azure SQL Database 或容錯移轉到次要資料庫](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>主動式異地複寫
針對比異地還原具有更積極復原需求的應用程式，請使用 [Azure 入口網站](sql-database-geo-replication-portal.md)、[PowerShell](sql-database-geo-replication-powershell.md) 或 [Transact-SQL](sql-database-geo-replication-transact-sql.md) 來設定主動式異地複寫。

## <a name="additional-resources"></a>其他資源
* [有關 Azure SQL Database 彈性功能的 Microsoft Virtual Academy 視訊課程](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png



<!--HONumber=Feb17_HO1-->


