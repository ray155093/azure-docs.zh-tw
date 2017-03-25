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
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 4307797b3961d8efef4045590e340268f0ad226d
ms.lasthandoff: 03/10/2017


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

請先決定您想要執行具有定量專用資源的單一資料庫，還是想要在一組資料庫之間共用資源集區。 檢閱[彈性集區的考量事項](sql-database-elastic-pool-guidance.md)。 若要決定服務層，請先判斷您所需要的資料庫功能底限︰

| **服務層功能** | **基本** | **標準** | **高級** | **進階 RS**|
| :-- | --: | --: | --: | --: |
| 個別資料庫大小上限 | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| 彈性集區中的儲存體總量上限 | 117 GB | 1200 GB | 750 GB | 750 GB |
| 每個集區的資料庫數目上限 | 400  | 400 | 50 | 50 |
| 資料庫備份的保留期限 | 7 天 | 35 天 | 35 天 | 35 天 |
||||||

> [!IMPORTANT]
> 使用 P11 和 P15 效能等級的客戶不需額外付費就能使用最多 4 TB 的內含儲存體。 這個 4 TB 選項目前在下列區域為公開預覽狀態：美國東部 2、美國西部、西歐、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部。 若要了解目前的限制，請參閱[目前的 4 TB 限制](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

一旦決定最低服務層之後，接下來要決定資料庫的效能層級 (DTU 數目)。 標準 S2 和 S3 效能層級通常是不錯的起點。 而對於具有高 CPU 或 IO 需求的資料庫，高階效能層級才是正確的起點。 比起 [標準] 效能層級，[高階] 提供更多的 CPU 以及多了 10 倍的 IO。

## <a name="single-database-service-tiers-and-performance-levels"></a>單一資料庫服務層和效能等級
若為單一資料庫，每個服務層內會有多個效能等級。 您可以使用 [Azure 入口網站](sql-database-manage-single-databases-portal.md)、[PowerShell](sql-database-manage-single-databases-powershell.md)、[Transact-SQL](sql-database-manage-single-databases-tsql.md)、C# 和 REST API，有彈性地選擇最符合工作負載需求的層級。 

無論裝載的資料庫數目，您的資料庫都保證會有一組資源，且您資料庫的預期效能特性不會受到影響。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需此服務層資料表中所有其他資料列的詳細說明，請參閱 [服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>相應放大或相應縮小單一資料庫

一開始挑選服務層和效能層級後，您可以根據實際經驗，動態放大或縮小單一資料庫。 如果您需要相應增加或相應減少，使用 [Azure 入口網站](sql-database-manage-single-databases-portal.md)、[PowerShell](sql-database-manage-single-databases-powershell.md)、[Transact-SQL](sql-database-manage-single-databases-tsql.md)、C# 和 REST API 即可輕鬆變更資料庫層級。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 這個時間範圍會有不同，但平均在 4 秒下，而且超過 99% 的案例少於 30 秒。 如果在連接停用時有大型交易執行中，則此時間範圍可能會更長。  

整個向上調整程序的期間取決於資料庫變更前後的大小和服務層。 例如，在標準服務層內進行變更的 250 GB 資料庫應在 6 小時內完成。 對於進階服務層內變更效能層級的相同大小資料庫，它應該在 3 小時內完成。

* 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。 
* 升級資料庫時若將 [異地複寫](sql-database-geo-replication-portal.md) 啟用，您必須先將其次要資料庫升級為所需的效能層，然後再升級主要資料庫。
* 從高階服務層降級時，您必須先終止所有的「異地複寫」關聯性。 您可以遵循 [從中斷情況復原](sql-database-disaster-recovery.md) 主題所述的步驟，停止主要資料庫與作用中次要資料庫之間的複寫程序。
* 還原服務會針對各種服務層提供不同的選項。 降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。 如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](sql-database-business-continuity.md)。
* 完成變更之前，不會將新屬性套用至資料庫。

> [!IMPORTANT]
> 如需詳細步驟，請參閱[在 Azure 入口網站中管理單一資料庫](sql-database-manage-single-databases-portal.md)、[使用 Powershell 管理單一資料庫](sql-database-manage-single-databases-powershell.md)或[使用 Transact-SQL 管理單一資料庫](sql-database-manage-single-databases-tsql.md)。
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>eDTU 中的彈性集區服務層和效能

集區可讓資料庫共用和取用 eDTU 資源，而無須指派特定效能等級給集區中的每個資料庫。 例如，標準集區中的單一資料庫可從使用 0 個 eDTU 到您設定集區時設定的最大資料庫 eDTU。 集區可讓多個具有不同工作負載的多個資料庫有效使用整個集區中的可用 eDTU。 如需詳細資訊，請參閱 [彈性集區的價格和效能考量](sql-database-elastic-pool-guidance.md) 。

下表說明集區服務層的特性。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

集區中的每個資料庫也需遵循該服務層的單一資料庫特性。 例如，「標準」集區的工作階段數上限為每個集區 4800 - 28800 個，但該集區中的個別資料庫的資料庫限制為 300 個工作階段。

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>相應放大或相應縮小彈性集區

一開始挑選服務層和效能層級後，您可以根據實際經驗，動態放大或縮小彈性集區。 

* 每個資料庫的最小 eDTU 數或每個資料庫的最大 eDTU 數變更作業通常在&5; 分鐘內即可完成。
* 變更集區大小的時間 (eDTUs) 取決於集區中所有資料庫的合併大小。 變更作業平均每 100 GB 會在 90 分鐘以內完成。 舉例來說，如果集區中所有資料庫的總空間為 200 GB，則每集區變更集區 eDTU 的預期延遲時間會少於 3 小時。

如需詳細步驟，請參閱[在 Azure 入口網站中管理彈性集區](sql-database-elastic-pool-manage-portal.md)、[使用 PowerShell 管理彈性集區](sql-database-elastic-pool-manage-powershell.md)、[使用 Transact-SQL 管理彈性集區](sql-database-elastic-pool-manage-tsql.md)或[使用 C# 管理彈性集區](sql-database-elastic-pool-manage-csharp.md)。

## <a name="creating-or-upgrading-to-4tb"></a>建立或升級至 4 TB

下列幾節會討論 4 TB 選項的實作詳細資料。

### <a name="creating-in-the-azure-portal"></a>在 Azure 入口網站中建立

在建立 P11/P15 時，系統會預先選取預設的 1 TB 儲存體選項。 若資料庫位於其中一個受支援區域，您可以將儲存體上限增加為 4 TB。 至於其他所有區域，則無法變更儲存體滑桿。 當您選取 4 TB 的內含儲存體時，價格不會變更。

### <a name="creating-using-powershell-or-transact-sql"></a>使用 PowerShell 或 Transact-SQL 建立

在建立 P11/P15 資料庫時，您可以將 maxsize 值設定為 1 TB (預設值) 或 4 TB。 系統也接受 '1024 GB' 和 '4096 GB' 的值。 如果您選擇 4 TB 的 maxsize 選項，但資料庫佈建在不受支援的區域，create 命令將會失敗並產生錯誤。

### <a name="upgrading-to-4tb"></a>升級至 4 TB 

若現有的 P11 和 P15 資料庫位於其中一個受支援區域，您可以將 maxsize 儲存體增加為 4 TB。 在 Azure 入口網站中、PowerShell 中或使用 Transact-SQL 都能完成此操作。 下列範例示範正在使用 ALTER DATABASE 命令變更 maxsize 的情形︰

```ALTER DATABASE <DatabaseName> MODIFY (MAXSIZE = 4096 GB);
```

Upgrading an existing P11 or P15 database can only be performed by a server-level principal login or by members of the dbmanager database role. 
If executed in a supported region the configuration will be updated immediately. This can be checked using the [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) or by inspecting the database size in the Azure portal. The database will remain online during the upgrade process. However, you will not be able to utilize the full 4 TB of storage until the actual database files have been upgraded to the new maxsize. The length of time required depends upon on the size of the database being upgraded.  

### Error messages
When creating or upgrading an P11/P15 database in an unsupported region, the create or upgrade operation will fail with the following error message: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.**

## Current limitations of P11 and P15 databases with 4 TB maxsize

- When creating or updating a P11 or P15 database, you can only chose between 1 TB and 4 TB maxsize. Intermediate storage sizes are not currently supported.
- The 4 TB database maxsize cannot be changed to 1 TB even if the actual storage used is below 1 TB. Thus, you cannot downgrade a P11-4TB/P15-4TB to a P11-1TB/P15-1TB or a lower performance tier (e.g., to P1-P6) until we are providing additional storage options for the rest of the performance tiers. This restriction also applies to the restore and copy scenarios including point-in-time, geo-restore, long-term-backup-retention, and database copy. Once a database is configured with the 4 TB option, all restore operations of this database must be into a P11/P15 with 4 TB maxsize.
- For Active Geo-Replication scenarios:
   - Setting up a geo-replication relationship: If the primary database is P11 or P15, the secondary(ies) must also be P11 or P15; lower performance tiers will be rejected as secondaries since they are not capable of supporting 4 TB.
   - Upgrading the primary database in a geo-replication relationship: Changing the maxsize to 4 TB on a primary database will trigger the same change on the secondary database. Both upgrades must be successful for the change on the primary to take effect. Region limitations for the 4TB option apply (see above). If the secondary is in a region that does not support 4 TB, the primary will not be upgraded.
- Using the Import/Export service for loading P11-4TB/P15-4TB databases is not supported. Use SqlPackage.exe to [import](sql-database-import-sqlpackage.md) and [export](sql-database-export-sqlpackage.md) data.

## Next steps

* Learn the details of [elastic pools](sql-database-elastic-pool-guidance.md) and [price and performance considerations for elastic pools](sql-database-elastic-pool-guidance.md).
* Learn how to [Monitor, manage, and resize elastic pools](sql-database-elastic-pool-manage-portal.md) and [Monitor the performance of single databases](sql-database-single-database-monitor.md).
* Now that you know about the SQL Database tiers, try them out with a [free account](https://azure.microsoft.com/pricing/free-trial/) and learn [how to create your first SQL database](sql-database-get-started.md).
* For migration scenarios, use the [DTU Calculator](http://dtucalculator.azurewebsites.net/) to approximate the number of DTUs needed. 


