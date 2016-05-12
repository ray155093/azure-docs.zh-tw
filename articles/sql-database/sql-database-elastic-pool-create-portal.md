<properties
	pageTitle="使用 Azure 入口網站建立新的彈性集區 | Microsoft Azure"
	description="如何將可調整的彈性資料庫集區新增至 SQL Database 組態，以便更輕鬆地進行多個資料庫的系統管理與資源共用。"
	keywords="可調整資料庫,資料庫組態"
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/28/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# 使用 Azure 入口網站來建立新的彈性資料庫集區

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

有兩種方式可以建立[集區](sql-database-elastic-pool.md)。

1. 自行建立，特別是您知道您要的集區設定時。
2. 從 SQL Database 服務的建議著手。此服務會使用遙測來檢查您過去的使用量。然後針對您的資料庫建議符合成本效益的組態。

您可以將多個集區新增至伺服器，但無法將來自不同伺服器的資料庫新增到相同的集區。若要建立集區，您至少需要一個位於 V12 伺服器的資料庫。如果沒有 v12 資料庫，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。您可以建立只有一個資料庫的集區，但具有多個資料庫的集區才符合成本效益。請參閱[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

集區只適用於 SQL Database V12 伺服器。如果您在 V11 伺服器上有一些資料庫，您可在 V12 伺服器上 [使用 PowerShell 指令碼將它們識別為集區的候選項目](sql-database-elastic-pool-database-assessment-powershell.md)，然後在單一步驟中[使用 PowerShell 升級至 V12 並建立集區](sql-database-upgrade-server-powershell.md)。

## 建立新的集區

1. 在 [Azure 入口網站](http://portal.azure.com/)中，按一下 [SQL 伺服器]，然後按一下要加入集區之資料庫所在的伺服器。
2. 按一下 [新增集區]。

    ![將集區加入伺服器](./media/sql-database-elastic-pool-create-portal/new-pool.png)

	**-或-**

	按一下訊息以查看系統根據資料庫的歷史使用量遙測資料所推薦的集區，然後按一下定價層以查看更多詳細資料並自訂集區。若要了解系統是如何做出推薦的，請參閱本主題稍後的[了解集區建議](#understand-pool-recommendations)。

    ![建議的集區](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

	[彈性資料庫集區] 刀鋒視窗隨即出現，以供您設定集區。如果您在前一個步驟中所按的是 [新增集區]，入口網站會在 [定價層] 底下選擇 [標準集區]，並為該集區選擇唯一 [名稱] 和預設組態。如果您選擇建議的集區，則已預先為集區選擇好建議的定價層和組態，不過您仍可加以變更。

    ![設定彈性集區](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. 若要變更集區的定價層，請依序按一下 [定價層]、您想要的定價層以及 [選取]。

    > [AZURE.IMPORTANT] 在最後一個步驟中選擇好定價層並按一下 [確定] 認可變更後，您就無法再變更集區的定價層。

4. 按一下 [設定集區]，在其中新增資料庫並選擇集區的資源設定。
5. 若要新增資料庫，請依序按一下 [新增資料庫]、您想要新增的資料庫以及 [選取] 按鈕。

    ![新增資料庫](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    如果您要使用的資料庫有足夠的歷史使用量遙測資料，[預估的 eDTU 和 GB 使用量] 圖形和 [實際的 eDTU 使用量] 長條圖便會更新以幫助您決定要使用的組態。

    ![動態建議](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. 使用 [設定集區] 頁面上的控制項，探索設定及設定您的集區。如需各服務層限制的詳細資訊，請參閱[彈性資料庫集區參考](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)；如需如何決定集區適當大小的詳細指引，則請參閱[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。
7. 完成時按一下 [選取]，然後再按一下 [確定] 來建立集區。

## 了解集區建議

SQL Database 服務會評估使用量的歷史資料，並為您推薦一或多個比使用單一資料庫更符合成本效益的集區。每個推薦集區都是以最適合該集區的伺服器資料庫唯一子集進行設定。集區建議包含下列內容︰

- 集區的定價層 (基本、標準或進階)。
- 適當的 [集區 eDTU] \(也稱為每一集區的最大 eDTU)
- 每一資料庫的 [eDTU 上限] 和 [eDTU 下限]
- 集區的建議資料庫清單

服務在建議集區時，會計算過去 30 天的遙測。對於被視為彈性資料庫集區候選項目的資料庫，它必須存在至少 7 天。已在彈性資料庫集區中的資料庫不會被視為彈性資料庫集區建議候選項目。

服務會評估將每個服務層中的單一資料庫移至同一層集區的資源需求和成本效益。例如，會評估伺服器上的所有 Standard 資料庫是否適合 Standard 彈性集區。這表示服務不會進行跨層建議，例如將 Standard 資料庫移到 Premium 集區。

## 其他資源

- [使用入口網站管理 SQL Database 彈性集區](sql-database-elastic-pool-manage-portal.md)
- [使用 PowerShell 管理 SQL Database 彈性集區](sql-database-elastic-pool-manage-powershell.md)
- [使用 C# 管理 SQL Database 彈性集區](sql-database-elastic-pool-manage-csharp.md)
- [使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md) 

<!---HONumber=AcomDC_0504_2016-->