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
ms.date: 12/09/2016
ms.author: carlrab; janeng
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: ed598cfdaf5bebc1cf92894ba1f6c79f268ef3c3


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL Database 選項和效能：了解每個服務層中可用的項目
[Azure SQL Database](sql-database-technical-overview.md) 提供三個服務層：[基本]、[標準] 和 [高階]，以及多個效能等級來處理不同的工作負載。 較高的效能等級提供越來越多的資源，旨在提供愈來愈高的輸送量。 您可以[動態變更服務層和效能層級](sql-database-scale-up.md)而不需要停機。 基本、標準和高階服務層都具備 99.99% 的執行時間 SLA、彈性的商務持續性選項、安全性功能，以及按小時計費。 

您可以在選取的[效能層級](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels)上，建立具有專用資源的獨立資料庫。 您也可以在[彈性集區](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)中管理多個資料庫，在其中各資料庫會共用資源。 獨立資料庫可用的資源會以資料庫交易單位 (DTU) 表示，而彈性集區可用的資源則會以彈性 DTU (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱〈[什麼是 DTU？](sql-database-what-is-a-dtu.md)〉。 

在這兩種情況下，服務層包含[基本]、[標準] 和 [高階]。 

## <a name="choosing-a-service-tier"></a>選擇服務層
下表提供最適用於不同應用程式工作負載的服務層範例。

| 服務層 | 目標工作負載 |
| :--- | --- |
| **基本** | 最適合於小型資料庫，通常會在指定的時間內支援單一的作用中作業。 範例包括用於開發或測試，或者不常使用之小規模應用程式的資料庫。 |
| **標準** |雲端應用程式具有低度到中等 IO 效能需求時的建議選項，支援多個並行查詢。 範例包括工作群組或 Web 應用程式。 |
| **高級** | 針對具有高 IO 效能需求的高交易量而設計，支援許多並行使用者。 範例包括支援任務關鍵性應用程式的資料庫。 |

請先決定您想要執行獨立的資料庫，或是共用資源的群組資料庫。 檢閱[彈性集區的考量事項](sql-database-elastic-pool-guidance.md)。 若要決定服務層，請先判斷您所需要的資料庫功能底限︰

* 個別資料庫的大小上限 (基本最大 2 GB、標準最大 250 GB、在高端效能層級中，高階最大可達 500 GB 至 1 TB)
* 使用彈性集區時，最大總儲存容量 (基本最大 117 GB、標準最大 1200 GB、高階最大 750 GB)
* 每個集區的資料庫最大數目 (基本 400 個、標準 400 個、高階 50 個)
* 資料庫備份的保留期限 (基本 7 天、標準和高階 35 天)

一旦決定最低服務層之後，接下來要決定資料庫的效能層級 (DTU 數目)。 在許多情況下，標準的 S2 和 S3 效能層級是不錯的起點。 而對於具有高 CPU 或 IO 需求的資料庫，高階效能層級才是正確的起點。 比起 [標準] 效能層級，[高階] 提供更多的 CPU 以及多了 10 倍的 IO。

一開始挑選效能層級後，您可以接著根據實際經驗，動態放大或縮小[個別資料庫](sql-database-scale-up.md)或[彈性集區](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool)。 若要進行移轉，您也可以使用 [DTU 計算機](http://dtucalculator.azurewebsites.net/)估計所需的 DTU 數目。 

## <a name="standalone-database-service-tiers-and-performance-levels"></a>獨立資料庫服務層和效能等級
對於獨立資料庫，每個服務層內有多個效能等級。 您可以彈性選擇最符合您工作負載需求的層級。 如果您需要相應增加或相應減少，可以很輕鬆地變更資料庫的服務層。 如需詳細資訊，請參閱 [變更資料庫服務層和效能層級](sql-database-scale-up.md) 。

此處所列的效能特性會套用至使用 [SQL Database V12](sql-database-v12-whats-new.md)建立的資料庫。 無論裝載的資料庫數目，您的資料庫都保證會有一組資源，且您資料庫的預期效能特性不會受到影響。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需此服務層資料表中所有其他資料列的詳細說明，請參閱 [服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>eDTU 中的彈性集區服務層和效能

集區可讓資料庫共用和取用 eDTU 資源，而無須指派特定效能等級給集區中的每個資料庫。 例如，「標準」集區中的獨立資料庫可使用 0 個 eDTU 或您設定集區時所設定的最大資料庫 eDTU。 集區可讓多個具有不同工作負載的多個資料庫有效使用整個集區中的可用 eDTU。 如需詳細資訊，請參閱 [彈性集區的價格和效能考量](sql-database-elastic-pool-guidance.md) 。

下表說明集區服務層的特性。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

集區中的每個資料庫也會符合該服務層的獨立資料庫特性。 例如，「標準」集區的工作階段數上限為每個集區 4800 - 28800 個，但該集區中的個別資料庫的資料庫限制為 300 個工作階段。

## <a name="next-steps"></a>後續步驟

* 深入了解[彈性集區](sql-database-elastic-pool-guidance.md)和[彈性集區](sql-database-elastic-pool-guidance.md)的價格與效能考量。
* 了解如何[監視、管理彈性集區和調整其大小](sql-database-elastic-pool-manage-portal.md)，以及[監視獨立資料庫的效能](sql-database-single-database-monitor.md)。
* 如果您認識了 SQL Database 各個層，可以透過[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)親身體驗，然後了解[如何建立您的第一個 SQL Database](sql-database-get-started.md)。




<!--HONumber=Dec16_HO2-->


