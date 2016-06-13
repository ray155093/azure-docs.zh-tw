<properties
	pageTitle="SQL Database 彈性集區價格和效能"
	description="彈性資料庫集區專屬定價資訊。"
	services="sql-database"
	documentationCenter=""
	authors="srinia"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/27/2016"
	ms.author="srinia"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 彈性資料庫集區的計費和定價資訊

彈性資料庫集區會依據下列特性計費：

- 彈性集區在建立時就會開始計費，即使集區中沒有資料庫也一樣。
- 彈性集區會以每小時計費。這和單一資料庫效能等級的計量頻率相同。
- 如果彈性集區調整為新的 eDTU 量，則在調整作業完成之前，不會根據新的 eDTU 量來計算集區費用。這會遵循與變更獨立資料庫的效能等級相同的模式。


- 彈性集區的價格是以集區的 eDTU 數為計算基礎。彈性集區的價格與其中之彈性資料庫的數目和使用量無關。
- 價格的計算方式為 (集區的 eDTU 數) x (每 eDTU 的單價)。

在同一個服務層中，彈性集區的 eDTU 單價大於獨立資料庫的 DTU 單價。如需詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。


若要了解 eDTU 及服務層，請參閱 [SQL Database 選項和效能](sql-database-service-tiers.md)。

## 後續步驟

- [建立彈性資料庫集區](sql-database-elastic-pool-create-portal.md)
- [監視、管理和估算彈性資料庫集區大小](sql-database-elastic-pool-manage-portal.md)
- [SQL Database 選項和效能：了解每個服務層中可用的項目](sql-database-service-tiers.md)
- [用來識別彈性資料庫集區適用資料庫的 PowerShell 指令碼](sql-database-elastic-pool-database-assessment-powershell.md)

<!---HONumber=AcomDC_0601_2016-->