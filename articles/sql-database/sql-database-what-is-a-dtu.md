---
title: "SQL Database：DTU 是什麼？ | Microsoft Docs"
description: "了解 Azure SQL Database 交易單元是什麼。"
keywords: "資料庫選項, 資料庫效能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 8b22b5ac29e855f3074cab01042057f214e9e9b7


---
# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>說明資料庫交易單位 (DTU) 和彈性資料庫交易單位 (eDTU)
本文說明資料庫交易單位 (DTU) 和彈性資料庫交易單位 (eDTU)，以及當您達到 DTU 或 eDTU 上限時會發生什麼狀況。  

## <a name="what-are-database-transaction-units-dtus"></a>何謂資料庫交易單位 (DTU)
DTU 是資源的度量單位，而這些資源保證可用於[單一資料庫服務層](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)內特定效能等級的單一 Azure SQL Database。 DTU 是 CPU、記憶體、資料 I/O 和交易記錄檔 I/O 的混合量值，以設計成為典型真實世界 OLTP 工作負載的 OLTP 基準測試工作負載所決定的比率來表示。 藉由提升資料庫的效能等級來使 DTU 加倍，等於讓該資料庫可用的資源集合加倍。 例如，相較於具有 5 個 DTU 的 Basic 資料庫，具有 1750 個 DTU 的 Premium P11 資料庫可提供 350 倍的 DTU 計算能力。 若要了解 OLTP 基準測試工作負載用來判斷 DTU 混合的方法，請參閱 [SQL Database 基準測試概觀](sql-database-benchmark-overview.md)。

![SQL Database 簡介：不同層級和等級的單一資料庫 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

您可以在應用程式停機時間最短 (通常平均少於四秒) 的情況下，隨時 [變更服務層](sql-database-service-tiers.md) 。 對於許多企業和應用程式而言，只要能夠建立資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 在此案例中，您可使用具有特定 eDTU 數目的彈性集區。

## <a name="what-are-elastic-database-transaction-units-edtus"></a>何謂彈性資料庫交易單位 (eDTU)
eDTU 是可以在 Azure SQL 伺服器上一組資料庫 (稱為 [彈性集區](sql-database-elastic-pool.md)) 之間共用的資源集 (DTU) 的度量單位。 彈性集區提供符合成本效益的簡單解決方案，以管理多個不同且具備無法預測的使用模式資料庫的效能目標。 如需詳細資訊，請參閱 [彈性集區和服務層](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) 。

![SQL Database 簡介：不同層級和等級的 eDTU](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

集區以固定價格提供固定數目的 eDTU。 在彈性集區內，會給予個別資料庫彈性以在設定的界限內自動調整。 在沈重的負載之下，資料庫可能耗用較多 eDTU 以符合需求，而負載較輕的資料庫則耗用較少 eDTU，直到完全無負載的資料庫不使用任何 eDTU 為止。 針對整個集區佈建資源，而不是針對每個資料庫佈建資源，可簡化管理工作並可預測集區的預算。

其他 eDTU 可以新增至現有集區，而不會造成資料庫停機，且對集區中資料庫沒有影響。 同樣地，如果不再需要額外 eDTU，則隨時可以從現有集區中移除。 您可以增減集區中的資料庫，或限制資料庫在沈重負載下可以使用的 eDTU，以便為其他資料庫保留 eDTU。 如果資料庫可預測未充分使用的資源，您可以將它移出集區並設定為具有可預測資源量的單一資料庫。

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>如何判斷我的工作負載所需的 DTU 數目？
如果您希望將現有的內部部署或 SQL Server 虛擬機器工作負載移轉至 Azure SQL Database，您可以使用 [DTU 計算機](http://dtucalculator.azurewebsites.net/) 來估計所需的 DTU 數目。 對於現有的 Azure SQL Database 工作負載，您可以使用 [SQL Database 查詢效能深入解析](sql-database-query-performance.md) 來了解您的資料庫資源耗用量 (DTU)，以深入了解如何最佳化您的工作負載。 您也可以使用 [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV，取得最近一小時的資源耗用資訊。 或者，也可查詢目錄檢視 [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx)，以取得最近 14 天的相同資料，雖然五分鐘平均值的精確度較低。

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>如何得知我能受益於彈性資源集區？
集區適合於具備特定使用模式的大量資料庫。 針對指定的資料庫，此模式的特徵是低平均使用量與相對不頻繁的使用量高峰。 SQL Database 會自動評估現有 SQL Database 伺服器中資料庫過去的資源使用量，並在 Azure 入口網站中建議適當的集區組態。 如需詳細資訊，請參閱 [何時應該使用彈性集區？](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>當我達到 DTU 上限時會發生什麼狀況
效能層級會受校正和管理，以提供所需資源來將您的資料庫工作負載執行到您所選服務層/效能層級允許的上限。 如果您的工作負載達到 CPU/資料 IO/記錄檔 IO 限制的其中一項限制，您可繼續接收允許之最大層級的資源，但您可能會經歷較長的查詢延遲。 這些限制並不會導致任何錯誤，但除非是速度慢到使查詢開始逾時，否則會使工作負載速度變慢。 如果您達到最大允許並行使用者工作階段/要求 (背景工作執行緒) 的限制，您會看到明確的錯誤。 如需 CPU、記憶體、資料 I/O 和交易記錄檔 I/O 以外的資源限制資訊，請參閱 [Azure SQL Database 資源限制](sql-database-resource-limits.md) 。

## <a name="next-steps"></a>後續步驟
* 如需單一資料庫和彈性集區可用的 DTU 和 eDTU 相關資訊，請參閱[服務層](sql-database-service-tiers.md)。
* 如需 CPU、記憶體、資料 I/O 和交易記錄檔 I/O 以外的資源限制資訊，請參閱 [Azure SQL Database 資源限制](sql-database-resource-limits.md) 。
* 請參閱 [SQL Database 查詢效能深入解析](sql-database-query-performance.md) ，以了解您的 (DTU) 耗用量。
* 請參閱 [SQL Database 基準測試概觀](sql-database-benchmark-overview.md) ，以了解 OLTP 基準測試工作負載用來判斷 DTU 混合的方法。



<!--HONumber=Feb17_HO3-->


