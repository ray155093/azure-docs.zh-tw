<properties
	pageTitle="彈性資料庫集區的 SQL Database |Microsoft Azure"
	description="使用集區管理數百或數千個資料庫。針對一組效能單位的一個價格可以分散在集區。您可以隨意將資料庫移入或移出。"
	keywords="彈性資料庫、sql 資料庫"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/04/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 什麼是 Azure 彈性資料庫集區？

SaaS 開發人員必須建立並管理數十、數百或甚至數千個 SQL Database。彈性資料庫集區可簡化跨多個資料庫的建立、維護和效能管理。從集區任意加入或減少資料庫。請參閱[在 Azure 入口網站中建立 SQL 資料庫的可調整彈性資料庫集區](sql-database-elastic-pool-create-portal.md)，或[使用 PowerShell](sql-database-elastic-pool-create-powershell.md) 或 [C#](sql-database-elastic-pool-csharp.md)。

如需 API 和錯誤詳細資訊，請參閱[彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。

## 運作方式

單一租用戶資料庫模型的常見 SaaS 應用程式模式是︰每個客戶被授與一個資料庫。每個客戶 (資料庫) 針對記憶體、IO 和 CPU，都有無法預期的資源需求。由於需求有高有低，您要如何配置資源？ 您通常有兩個選項：(1) 根據尖峰使用量額外佈建資源並額外付款，或是 (2) 少量佈建來節省成本，但會降低在尖峰期間的效能和客戶滿意度。彈性資料庫集區會確保資料庫在必要時取得所需的效能資源，同時在可預測的預算之內，提供簡單的資源配置機制，藉此解決這個問題。

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

在 SQL Database 中，資料庫能夠處理資源需求的相對量值，在單一資料庫中是以資料庫交易單位 (DTU) 表示，在彈性資料庫集區中則以彈性 DTU (eDTU) 表示。如需深入了解 DTU 和 eDTU 的資訊，請參閱 [SQL Database 簡介](sql-database-technical-overview.md#understand-dtus)。

集區以固定價格提供固定數目的 eDTU。在集區內，會給予個別資料庫彈性以在設定的參數內自動調整。沈重負載的資料庫底可以使用更多的 eDTU 以滿足需求。負載較輕的資料庫耗用較少 eDTU，而完全無負載的資料庫不使用任何 eDTU。針對整個集區佈建資源，而不是針對單一資料庫佈建資源，可簡化管理工作。此外，您還可以有可預測的集區預算。

其他的 eDTU 可以加入現有的集區而不會有資料庫停機或資料庫的負面影響。同樣地，如果不再需要額外 eDTU，則隨時可以從現有集區中移除。

您也可以在集區加入或減少資料庫。如果可以預測資料庫使用少量資源，則將它移出。

## 哪個資料庫會進入集區？

![在彈性資料庫集區中共用 eDTU 的 SQL 資料庫。][1]

最適合加入至彈性資料庫集區的資料庫通常是有時活躍，有時閒置。在上述範例中，您可以看到單一資料庫的活動、4 個資料庫的活動，最後是具有 20 個資料庫的彈性資料庫集區的活動。活動隨時間而不同的資料庫很適合加入至彈性集區，因為它們不是永遠都在使用中，而且可以共用 eDTU。並非所有資料庫都符合這個模式。有些資料庫的資源需求比較固定，這類資料庫較適合基本、標準和進階服務層，在這些服務層中會個別指派資源。

[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。


> [AZURE.NOTE] 彈性資料庫集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。

## 彈性資料庫集區屬性
彈性集區和彈性資料庫的限制。

| 屬性 | 說明 |
| :-- | :-- |
| 服務層 | Basic、Standard 或 Premium。服務層決定可設定的效能和儲存體限制的範圍，以及商務持續性選項。在集區內的每個資料庫都與集區具有相同的服務層。「服務層」也稱為「版本」。|
| 每個集區的 eDTU | 可由集區中的資料庫共用的 eDTU 數目上限。在相同的時間點，集區中的資料庫所使用的總 eDTU 數目不得超過此限制。 |
| 每個集區的儲存體 | 可由集區中的資料庫所共用的儲存體數量上限。集區中的資料庫所使用的儲存體總數不得超過這個限制。這個限制是由每個集區的 eDTU 所決定。如果超過此限制，所有資料庫都會變成唯讀。 |
| 每個資料庫的 eDTU 數目上限 | 集區中的任何資料庫可以使用的 eDTU 數目上限，並且適用於集區中的所有資料庫。每個資料庫的 eDTU 數目上限不等於資源保證。 |
| 每個資料庫的 eDTU 數目下限 | 集區中的任何資料庫受到保證的 eDTU 數目下限，並且適用於集區中的所有資料庫。每個資料庫的 eDTU 數目下限可以設為 0。請注意，集區中的資料庫數目和每個資料庫 eDTU 數目下限的乘積不能超過每個集區的 eDTU。 |


## 彈性集區和彈性資料庫的 eDTU 和儲存體限制


[AZURE.INCLUDE [彈性資料庫的 SQL DB 服務層資料表](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

## 彈性資料庫工作

使用集區，只要在**[彈性工作](sql-database-elastic-jobs-overview.md)**中執行指令碼，就能簡化管理工作。彈性資料庫工作會消除與大量資料庫相關聯的冗長工作。若要開始，請參閱[開始使用彈性資料庫工作](sql-database-elastic-jobs-getting-started.md)。

如需其他工具的詳細資訊，請參閱[彈性資料庫工具學習圖](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)。

## 集區中資料庫的業務續航力功能

彈性資料庫支援 V12 伺服器上的單一資料庫可用的大部分[業務續航力功能](sql-database-business-continuity.md) (目前為預覽階段)。

### 還原時間點

系統會自動備份彈性資料庫集區中的資料庫，而且備份保留原則與單一資料庫相對應的服務層相同。總結來說，每一層的資料庫具有不同的還原範圍：

* **基本集區**：可還原至過去 7 天內的任意點。
* **標準集區**：可還原至過去 14 天內的任意點。
* **進階集區**：可還原至過去 35 天內的任意點。

在預覽期間，在集區中的資料庫將會還原到相同集區中的新資料庫。卸除的資料庫一律還原為集區外的獨立資料庫，而進入該服務層的最低效能層級。例如，標準集區中卸除的彈性資料庫將還原為 S0 資料庫。您可以透過 Azure 入口網站或以程式設計方式使用 REST API，來執行資料庫還原作業。PowerShell Cmdlet 支援即將推出。

### 異地還原

異地還原可讓您將集區中的資料庫復原到不同區域中的伺服器。在預覽期間，若要在不同的伺服器上還原集區中的資料庫，目標伺服器需要具有與來源集區同名的集區。如有必要，請在目標伺服器上建立新的集區，並提供相同名稱，再還原資料庫。如果目標伺服器上沒有同名的集區，異地還原作業將會失敗。如需詳細資料，請參閱[使用異地還原進行復原](sql-database-disaster-recovery.md#recover-using-geo-restore)。


### 異地複寫

「標準」或「高階」彈性資料庫集區中的所有資料庫都可以使用異地複寫。只要服務層相同，異地複寫合作關係中的一個或所有資料庫就可以在彈性資料庫集區中。您可以使用 [Azure 入口網站](sql-database-geo-replication-portal.md)、[PowerShell](sql-database-geo-replication-powershell.md) 或 [Transact-SQL](sql-database-geo-replication-transact-sql.md) 為彈性資料庫集區設定異地複寫。

### 匯入和匯出

支援從集區將資料庫匯出。目前不支援直接將資料庫匯入至集區，但可以先匯入至單一資料庫，再將資料庫移到集區。


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0413_2016-->