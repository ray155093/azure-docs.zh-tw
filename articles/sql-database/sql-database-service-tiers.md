---
title: "SQL Database 效能：服務層 | Microsoft Docs"
description: "比較 SQL Database 服務層。"
keywords: "資料庫選項, 資料庫效能"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 05/31/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 9ea73d39a8fcee82e749d20accdd3a3c30cba94e
ms.contentlocale: zh-tw
ms.lasthandoff: 06/01/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL Database 選項和效能：了解每個服務層中可用的項目

[Azure SQL Database](sql-database-technical-overview.md) 提供四個服務層：**基本**、**標準**、**進階**和**進階 RS**。 每個服務層都有多個效能等級可處理不同的工作負載。 較高的效能等級會提供額外的資源，旨在提供愈來愈高的輸送量。 您可以動態變更服務層和效能層級而不需要停機。 基本、標準和高階服務層都具備 99.99% 的執行時間 SLA、彈性的商務持續性選項、安全性功能，以及按小時計費。 進階 RS 層會提供和進階層相同的效能等級、安全性功能和商務持續性功能，但 SLA 會降低。

> [!IMPORTANT]
> 進階 RS 資料庫在執行時所用的備援複本數，比進階或標準資料庫所用的少。 因此，當服務失敗時，最慢可能會延遲 5 分鐘才能從備份中復原資料庫。
>

您可以在具有特定[效能等級](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)的服務層內，建立具有專用資源的單一資料庫。 您也可以在資源會由多個資料庫共用的[彈性集區](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)內建立資料庫。 單一資料庫可用的資源會以資料庫交易單位 (DTU) 表示，而彈性集區可用的資源則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[什麼是 DTU？](sql-database-what-is-a-dtu.md) 

## <a name="choosing-a-service-tier"></a>選擇服務層
下表提供最適用於不同應用程式工作負載的服務層範例。

| 服務層 | 目標工作負載 |
| :--- | --- |
| **基本** | 最適合於小型資料庫，通常會在指定的時間內支援單一的作用中作業。 範例包括用於開發或測試，或者不常使用之小規模應用程式的資料庫。 |
| **標準** |雲端應用程式具有低度到中等 IO 效能需求時的建議選項，支援多個並行查詢。 範例包括工作群組或 Web 應用程式。 |
| **高級** | 針對具有高 IO 效能需求的高交易量而設計，支援許多並行使用者。 範例包括支援任務關鍵性應用程式的資料庫。 |
| **進階 RS** | 專為需要大量 IO 但不需要高可用性保證的工作負載所設計。 範例包括測試高效能工作負載，或資料庫不是記錄系統的分析工作負載。 |
|||

請先決定您想要執行具有定量專用資源的單一資料庫，還是想要在一組資料庫之間共用資源集區。 檢閱[彈性集區的考量事項](sql-database-elastic-pool.md)。 若要決定服務層，請先判斷您所需要的資料庫功能底限︰

| **服務層功能** | **基本** | **標準** | **高級** | **進階 RS**|
| :-- | --: | --: | --: | --: |
| 單一資料庫大小上限 | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| 彈性集區大小上限 | 156 GB | 2.9 TB | 4 TB* | 750 GB |
| 彈性集區中的資料庫大小上限 | 2 GB | 250 GB | 500 GB | 500 GB |
| 每個集區的資料庫數目上限 | 500  | 500 | 100 | 100 |
| 單一資料庫 DTU 上限 | 5 | 100 | 4000 | 1000 |
| 彈性集區中每個資料庫的 DTU 上限 | 5 | 3000 | 4000 | 1000 |
| 資料庫備份的保留期限 | 7 天 | 35 天 | 35 天 | 35 天 |
||||||

> [!IMPORTANT]
> 目前已在下列區域中提供額外的儲存體選項：美國東部 2、美國西部、美國維吉尼亞州政府、西歐、德國中部、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部。 請參閱[目前的 4 TB 限制](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

一旦決定最低服務層之後，接下來要決定資料庫的效能層級 (DTU 數目)。 標準 S2 和 S3 效能層級通常是不錯的起點。 而對於具有高 CPU 或 IO 需求的資料庫，高階效能層級才是正確的起點。 比起 [標準] 效能層級，[高階] 提供更多的 CPU 以及多了 10 倍的 IO。

## <a name="single-database-service-tiers-and-performance-levels"></a>單一資料庫服務層和效能等級
若為單一資料庫，每個服務層內會有多個效能等級。 您可以使用 Azure 入口網站、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database)、C# 和 REST API，有彈性地選擇最符合工作負載需求的層級。  

無論裝載的資料庫數目，您的資料庫都保證會有一組資源，且您資料庫的預期效能特性不會受到影響。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需此服務層資料表中所有其他資料列的詳細說明，請參閱 [服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>相應放大或相應縮小單一資料庫

一開始挑選服務層和效能層級後，您可以根據實際經驗，動態放大或縮小單一資料庫。 如果您需要相應增加或相應減少，使用 Azure 入口網站、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database)、C# 和 REST API 即可輕鬆變更資料庫層級。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 這個時間範圍會有不同，但平均在 4 秒下，而且超過 99% 的案例少於 30 秒。 如果在連接停用時有大型交易執行中，則此時間範圍可能會更長。  

整個向上調整程序的期間取決於資料庫變更前後的大小和服務層。 例如，在標準服務層內進行變更的 250 GB 資料庫應在 6 小時內完成。 對於進階服務層內變更效能層級的相同大小資料庫，它應該在 3 小時內完成。

* 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。 
* 升級資料庫時若將[異地複寫](sql-database-geo-replication-portal.md)啟用，您必須先將其次要資料庫升級為所需的效能層級，然後再升級主要資料庫。
* 從高階服務層降級時，您必須先終止所有的異地複寫關聯性。 您可以遵循 [從中斷情況復原](sql-database-disaster-recovery.md) 主題所述的步驟，停止主要資料庫與作用中次要資料庫之間的複寫程序。
* 還原服務會針對各種服務層提供不同的選項。 降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。 如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](sql-database-business-continuity.md)。
* 完成變更之前，不會將新屬性套用至資料庫。

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>eDTU 中的彈性集區服務層和效能

集區可讓資料庫共用和取用 eDTU 資源，而無須指派特定效能等級給集區中的每個資料庫。 例如，標準集區中的單一資料庫可從使用 0 個 eDTU 到您設定集區時設定的最大資料庫 eDTU。 集區可讓多個具有不同工作負載的多個資料庫有效使用整個集區中的可用 eDTU。 如需詳細資訊，請參閱 [彈性集區的價格和效能考量](sql-database-elastic-pool.md) 。

下表描述彈性集區的資源限制。  根據 DTU 和服務層，彈性集區中個別資料庫的資源限制通常與集區外部之單一資料庫的資源限制相同。  例如，S2 資料庫的並行背景工作數上限是 120 個背景工作。  因此，如果集區中每個資料庫的最大 DTU 是 50 DTU (這相當於 S2)，標準集區中的資料庫最大並行背景工作數上限也會是 120 個背景工作。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>相應放大或相應縮小彈性集區

一開始挑選服務層和效能層級後，您可以根據實際經驗，動態放大或縮小彈性集區。 

* 每個資料庫的最小 eDTU 數或每個資料庫的最大 eDTU 數變更作業通常在 5 分鐘內即可完成。
* 變更集區大小的時間 (eDTUs) 取決於集區中所有資料庫的合併大小。 變更作業平均每 100 GB 會在 90 分鐘以內完成。 舉例來說，如果集區中所有資料庫的總空間為 200 GB，則每集區變更集區 eDTU 的預期延遲時間會少於 3 小時。

如需詳細步驟，請參閱[在 Azure 入口網站中管理彈性集區](sql-database-elastic-pool-manage-portal.md)、[使用 PowerShell 管理彈性集區](scripts/sql-database-monitor-and-scale-pool-powershell.md)、[使用 Transact-SQL 管理彈性集區](sql-database-elastic-pool-manage-tsql.md)或[使用 C# 管理彈性集區](sql-database-elastic-pool-manage-csharp.md)。

## <a name="creating-or-upgrading-to-4tb"></a>建立或升級至 4 TB

下列幾節會討論 4 TB 選項的實作詳細資料。

### <a name="creating-in-the-azure-portal"></a>在 Azure 入口網站中建立

在建立 P11/P15 時，系統會預先選取預設的 1 TB 儲存體選項。 若資料庫位於其中一個受支援區域，您可以將儲存體上限增加為 4 TB。 至於其他所有區域，則無法變更儲存體滑桿。 當您選取 4 TB 的內含儲存體時，價格不會變更。

### <a name="creating-using-powershell-or-transact-sql"></a>使用 PowerShell 或 Transact-SQL 建立

在建立 P11/P15 資料庫時，您可以將 maxsize 值設定為 1 TB (預設值) 或 4 TB。 系統也接受 '1024 GB' 和 '4096 GB' 的值。 如果您選擇 4 TB 的 maxsize 選項，但資料庫佈建在不受支援的區域，create 命令將會失敗並產生錯誤。

### <a name="upgrading-to-4tb"></a>升級至 4 TB 

若現有的 P11 和 P15 資料庫位於其中一個受支援區域，您可以將 maxsize 儲存體增加為 4 TB。 在 Azure 入口網站中、PowerShell 中或使用 Transact-SQL 都能完成此操作。 下列範例示範正在使用 ALTER DATABASE 命令變更 maxsize 的情形︰

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

只有伺服器層級的主要登入或 dbmanager 資料庫角色的成員，才能執行現有的 P11 或 P15 資料庫升級。 如果是在支援的區域中執行，則系統會立即更新組態。 您可以使用 [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)，或檢查 Azure 入口網站中的資料庫大小加以確認。 在升級過程中，資料庫會保持離線狀態。 但是，在將實際的資料庫檔案升級至新的大小上限前，您無法完整地使用 4TB 儲存體。 所需的時間長短依進行升級的資料庫大小而定。  

### <a name="error-messages"></a>錯誤訊息
在不受支援的區域中建立或升級 P11/P15 資料庫時，該建立或升級作業會失敗，並且顯示下列錯誤訊息：**具備最多 4TB 儲存體的 P11 和 P15 資料庫僅供美國東部 2、美國西部、美國維吉尼亞州政府、西歐、德國中部、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部使用。**

## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>P11 和 P15 資料庫目前的限制大小上限為 4TB

- 建立或更新 P11 或 P15 資料庫時，您只能在 1TB 和 4TB 的大小上限之間選擇。 目前不支援中繼儲存體大小。
- 即使實際使用的儲存體不足 1TB，您也無法將 4TB 的資料庫大小上限變更為 1TB。 因此，在我們為其他效能層級提供額外的儲存體選項前，您無法將 P11-4TB/P15-4TB 降級為 P11-1TB/P15-1TB 或更低的效能層級 (例如，降為 P1-P6)。 這項限制也適用於包括時間點、異地還原、長期備份保留期和資料庫備份等還原和複製案例。 一旦將資料庫設定為 4TB 的選項，此資料庫的所有還原作業都必須以 4TB 大小上限的 P11/P15 執行。
- 若是作用中異地複寫案例：
   - 設定異地複寫關聯性：如果 P11 或 P15 為主要資料庫，則次要資料庫也必須是 P11 或 P15；系統會拒絕使用較低的效能層級作為次要資料庫，因為這些資料庫無法支援 4TB 的大小。
   - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 4TB 會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於 4TB 選項的區域限制 (請參閱上述內容)。 如果次要資料庫位於不支援 4TB 大小的區域，就無法升級主要資料庫。
- 不支援使用匯入/匯出服務載入 P11-4TB/P15-4TB 資料庫的功能。 使用 SqlPackage.exe 來[匯入](sql-database-import.md)和[匯出](sql-database-export.md)資料。

## <a name="next-steps"></a>後續步驟

* 深入了解[彈性集區](sql-database-elastic-pool.md)和[彈性集區](sql-database-elastic-pool.md)的價格與效能考量。
* 了解如何[監視、管理彈性集區和調整其大小](sql-database-elastic-pool-manage-portal.md)和[監視單一資料庫的效能](sql-database-single-database-monitor.md)。
* 如果您認識了 SQL Database 各個層，可以透過[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)親身體驗，然後了解[如何建立您的第一個 SQL Database](sql-database-get-started-portal.md)。
* 若要進行移轉，請使用 [DTU 計算機](http://dtucalculator.azurewebsites.net/)估計所需的 DTU 數目。 


