<properties
	pageTitle="SQL Database 效能調整秘訣 | Microsoft Azure"
	description="透過評估和改進來調整 Azure SQL Database 效能的秘訣。"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="sql 效能調整，資料庫效能調整，sql 效能調整秘訣，sql 資料庫效能調整"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>

# SQL Database 效能調整秘訣
您可以隨時變更單一資料庫的[服務層級](sql-database-service-tiers.md)或增加彈性資料庫集區的 eDTU 以提升效能，但您可能會想要先識別提升與最佳化查詢效能的機會。遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。本文提供 SQL Database 的效能調整指引。

[AZURE.INCLUDE [支援免責聲明](../../includes/support-disclaimer.md)]

## 評估和調整資料庫效能的步驟
1.	在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Database]、選取資料庫，然後使用監視圖表尋找接近其上限的資源。預設會顯示 DTU 耗用量。按一下 [編輯] 來變更所顯示的時間範圍和值。
2.	使用[查詢效能深入解析](sql-database-query-performance.md)來以 DTU 評估查詢，然後使用 [SQL Database 建議程式](sql-database-advisor.md)來檢視適用於建立和卸除索引、參數化查詢及修正結構描述問題的建議。
3.	您可以使用動態管理檢視 (DMV)、擴充的事件 (Xevent) 和 SSMS 中的查詢存放區即時取得效能參數。如需詳細的監視和調整秘訣，請參閱[效能指引主題](sql-database-performance-guidance.md)。


    > [AZURE.IMPORTANT] 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。[更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。


## 使用更多資源提升資料庫效能的步驟
1.	若是單一資料庫，您可以視需要[變更服務層級](sql-database-scale-up.md)以提升資料庫效能。
2.	若是多個資料庫，請考慮使用[彈性資料庫集區](sql-database-elastic-pool-guidance.md)自動調整資源。

<!---HONumber=AcomDC_0914_2016-->