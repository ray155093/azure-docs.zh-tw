---
title: "SQL Database 效能：服務層 | Microsoft Docs"
description: "比較 SQL Database 服務層。"
keywords: "資料庫選項, 資料庫效能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 7bbdbe345bd468c01e2a790610bcf6c063c11f9b


---
# <a name="sql-database-service-tiers-for-single-databases-and-elastic-database-pools"></a>適用於單一資料庫和彈性資料庫集區的 SQL Database 服務層
[Azure SQL Database](sql-database-technical-overview.md) 提供三個服務層以及多個效能等級來處理不同的工作負載。 較高的效能等級提供越來越多的資源集，旨在提供愈來愈高的輸送量。 您可以動態變更服務層和效能層級。 如需詳細資訊，請參閱 [變更資料庫服務層和效能層級](sql-database-scale-up.md) 。

您可以在資料庫自己的 [服務層](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) 及自己的效能等級管理每個資料庫。 您也可以在具有一組共用資源的[彈性資料庫集區](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus)中管理多個資料庫。 可供單一資料庫使用的資源會以資料庫交易單位 (DTU) 表示，而可供彈性資料庫集區使用的資源則會以彈性 DTU 或 eDTU 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[何謂 DTU](sql-database-what-is-a-dtu.md)。 

在這兩種情況下，服務層包含[基本]、[標準] 和 [高階]。 

## <a name="service-tiers"></a>服務層
基本、標準和高階服務層都具備 99.99% 的執行時間 SLA，並且提供可預測的效能、彈性的商務持續性選項、安全性功能，以及小時計費。 下表提供最適用於不同應用程式工作負載的服務層範例。

| 服務層 | 目標工作負載 |
| :--- | --- |
| **基本** |最適合於小型資料庫，通常會在指定的時間內支援單一的作用中作業。 範例包括用於開發或測試，或者不常使用之小規模應用程式的資料庫。 |
| **標準** |適用於大多數雲端應用程式，可支援多個並行查詢。 範例包括工作群組或 Web 應用程式。 |
| **高級** |針對高交易量而設計，可支援大量並行使用者，且需要最高層級商務持續性功能。 範例包括支援任務關鍵性應用程式的資料庫。 |

> [!NOTE]
> Web 和 Business Edition 均已淘汰。 如果您打算繼續使用Web 和 Business 版本，請閱讀 [終止常見問題集](https://azure.microsoft.com/pricing/details/sql-database/web-business/) 。
> 
> 

## <a name="single-database-service-tiers-and-performance-levels"></a>單一資料庫服務層和效能等級
若為單一資料庫，每個服務層內會有多個效能等級。 您可以彈性選擇最符合您工作負載需求的層級。 如果您需要相應增加或相應減少，可以快速變更資料庫的服務層。 如需詳細資訊，請參閱 [變更資料庫服務層和效能層級](sql-database-scale-up.md) 。

此處所列的效能特性會套用至使用 [SQL Database V12](sql-database-technical-overview.md)建立的資料庫。 無論裝載的資料庫數目，您的資料庫都保證會有一組資源，且您資料庫的預期效能特性不會受到影響。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需此服務層資料表中所有其他資料列的詳細說明，請參閱 [服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 
> 

## <a name="elastic-database-pool-service-tiers-and-performance-in-edtus"></a>eDTU 中的彈性資料庫集區服務層和效能
您也可以在[彈性資料庫集區](sql-database-elastic-pool.md)內建立多個資料庫。 彈性資料庫集區中的所有資料庫會共用一組通用資源。 您可使用 *彈性資料庫交易單位* (eDTU) 來測量效能特性。 如同單一資料庫，集區有三個服務層：**基本**、**標準**和**進階**。 集區的這三個服務層仍會定義整體效能限制與多項功能。

集區可讓彈性資料庫共用和取用 DTU 資源，而無須指派特定效能等級給集區中的每個資料庫。 例如，標準集區中的資料庫所使用的 eDTU 個數可從 0 個到您在設定集區時設定的最大資料庫 eDTU。 集區可讓多個具有不同工作負載的多個資料庫有效使用整個集區中的可用 eDTU。 如需詳細資料，請參閱[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

下表說明基本、標準和高階彈性資料庫集區的特性。

[!INCLUDE [SQL DB service tiers table for elastic database pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

集區中的每個資料庫也需遵循該服務層的單一資料庫特性。 例如，「標準」集區的工作階段數上限為每個集區 4800 - 28800 個，但該集區中的個別資料庫的資料庫限制為 300 個工作階段。

## <a name="choosing-a-service-tier"></a>選擇服務層
若要決定服務層，請先判斷資料庫應為單一資料庫或是彈性資料庫集區的一部分。 

### <a name="choosing-a-service-tier-for-a-single-database"></a>選擇單一資料庫的服務層
若要決定單一資料庫的服務層，請先判斷您需要的資料庫功能以選擇 SQL Database 版本：

* 資料庫大小 (基本最大 2GB、標準最大 250 GB、進階最大 500 GB 至 1 TB - 視效能層級而定)
* 資料庫備份的保留期限 (基本 7 天、標準 35 天、進階 35 天)

一旦決定 SQL Database 版本之後，接下來要決定資料庫的效能層級 (DTU 數目)。 如同您所猜測，然後根據實際經驗 [動態相應增加或減少](sql-database-scale-up.md) 。 也可以使用 [DTU 計算機](http://dtucalculator.azurewebsites.net/) 估計所需的 DTU 數目。 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>選擇彈性資料庫集區的服務層。
若要決定彈性資料庫集區的服務層，先判斷您需要的資料庫功能以選擇集區的服務層。

* 資料庫大小 (基本 2 GB、標準 250 GB、進階 500 GB)
* 資料庫備份的保留期限 (基本 7 天、標準 35 天、進階 35 天)
* 每個集區的資料庫數目 (基本 400 個、標準 400 個、進階 50 個)
* 每個集區的最大儲存空間 (基本 117 GB、標準 1200 GB、進階 750 GB)

一旦決定集區的服務層之後，接下來要決定集區的效能層級 (eDTU)。 如同您所猜測，然後根據實際經驗 [動態相應增加或相應減少](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) 。 也可以使用 [DTU 計算機](http://dtucalculator.azurewebsites.net/) 估計集區中個別資料庫所需的 DTU 數目，協助您設定集區的上限。

## <a name="next-steps"></a>後續步驟
* 請在 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)進一步了解這些層的定價。
* 深入了解[彈性資料庫集區](sql-database-elastic-pool-guidance.md)和[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。
* 了解如何[監視、管理彈性資料庫集區和調整其大小](sql-database-elastic-pool-manage-portal.md)和[監視單一資料庫的效能](sql-database-single-database-monitor.md)。
* 如果您認識了 SQL Database 各個層，可以透過[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)親身體驗，然後了解[如何建立您的第一個 SQL Database](sql-database-get-started.md)。

## <a name="additional-resources"></a>其他資源
* [使用 Azure SQL Database 設計多租用戶 SaaS 應用程式的模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [有關 Azure SQL Database 中彈性資料庫功能的 Microsoft Virtual Academy 視訊課程](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)




<!--HONumber=Nov16_HO4-->


