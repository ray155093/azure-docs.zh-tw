<properties
	pageTitle="使用 Azure 入口網站監視和管理彈性資料庫集區 | Microsoft Azure"
	description="了解如何使用 Azure 入口網站和 SQL Database 的內建智慧功能來管理、監視及準確估量可調整的彈性資料庫集區，以期能最佳化資料庫效能和管理成本。"
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/02/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 Azure 入口網站監視和管理彈性資料庫集區

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


您可以使用 Azure 入口網站來監視、管理及設定彈性資料庫集區和集區中的資料庫。SQL Database 擁有內建的智慧功能，可分析伺服器上所有資料庫的歷史使用量 (無論是在集區中與否)，並在更符合成本效益時主動推薦資料庫的集區。

入口網站可讓您變更集區和資料庫設定、預覽變更，並同時認可所有變更。您可以預覽變更，例如新增和移除資料庫。系統也會顯示對效能和價格的潛在影響。

若要逐一實施本文章中的步驟，您需要具備資料庫和集區。如果您有現有的資料庫，請參閱[建立集區](sql-database-elastic-pool-create-portal.md)；如果您沒有資料庫，請參閱 [SQL 資料庫教學課程](sql-database-get-started.md)。

## 選擇要使用的集區

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [瀏覽]。
2. 按一下 [SQL 彈性集區]
3. 從清單中，按一下您想要使用的集區。

## 將資料庫移入彈性集區

您可以從現有的集區中新增或移除資料庫。資料庫可以位於其他集區。不過，您只可以新增位於相同邏輯伺服器上的資料庫。

1. 在集區刀鋒視窗的 [彈性資料庫] 下，按一下 [設定集區]。

    ![按一下 [設定集區]][1]

2. 在 [設定集區] 刀鋒視窗中，按一下 [新增至集區]。

	![按一下 [新增到集區]](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. 在 [新增資料庫] 刀鋒視窗中，選取要新增到集區中的資料庫。然後按一下 [選取]。

	![選取要新增的資料庫](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    [設定集區] 刀鋒視窗現在會列出您才新增的資料庫，其狀態設為**處理中**。

    ![擱置中的新增集區](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. 在 [設定集區] 刀鋒視窗中，按一下 [儲存]。

    ![按一下 [Save] (儲存)。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## 將資料庫移出彈性集區

1. 在 [設定集區] 刀鋒視窗中，選取要移除的資料庫。

    ![資料庫清單](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. 按一下 [從集區移除]。

    ![資料庫清單](./media/sql-database-elastic-pool-manage-portal/remove-from-pool.png)

	選取的資料庫會以「選取要移除的資料庫」在 UI 中顯示。


## 監視集區的資源使用率


1. 選擇要使用的集區。
2. 圖表和動態磚會在 [彈性集區監視] 下方顯示集區的重要使用率資訊。

![監視彈性集區](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**若要變更圖表和顯示：**

- 按一下 [**編輯**]。

	![按一下 [編輯]。](./media/sql-database-elastic-pool-manage-portal/edit-resource-utlization.png)

- 在 [編輯圖表] 刀鋒視窗中，選取新的時間範圍 (過去或今天的時間，或上星期的時間)，或按一下 [自訂] 以設定不同的時間範圍。選取圖表類型 (長條圖或折線圖)，再選取要監視的資源。

	![按一下 [編輯]。](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- 然後按一下 [確定]。


## 將警示加入集區資源

您可以將規則加入資源，以在當資源達到您設定的使用率閾值時，傳送電子郵件給人員或傳送警示字串到 URL 端點。

**將警示加入任何資源：**

1. 按一下 [資源使用率] 圖表以開啟 [度量] 刀鋒視窗，按一下 [加入警示]，然後在 [加入警示規則] 刀鋒視窗中填寫資訊 ([資源] 會自動設定為使用中的集區)。
2. 輸入供您和收件者辨別警示的 [名稱] 和 [描述]。
3. 從清單選擇要提出警示的 [度量]。

    圖表會以動態方式顯示該度量的資源使用量，協助您選擇閾值。

4. 選擇 [條件] \(大於、小於等) 和 [閾值]。
5. 按一下 [確定]。

## 變更集區的效能設定

當您監視集區的資源使用情況時，可能會發現集區需要更多的 eDTU。或者，集區中的個別資料庫需要不同的 eDTU 設定。您可以隨時變更集區設定，以在效能和成本之間取得最佳平衡。請參閱[何時應該使用彈性資料庫集區？](sql-database-elastic-pool-guidance.md)以取得更多資訊。

**要變更每個集區的 eDTU，以及每個資料庫的 eDTU：**

1. 開啟 [設定集區] 刀鋒視窗。

    在 [彈性資料庫集區設定] 下，使用滑桿來變更集區設定。

    ![彈性集區資源使用量](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 當設定變更時，則會顯示變更的每月預估成本。

    ![更新集區和新的每月成本](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## 預覽資料庫動作

在認可 [設定集區] 刀鋒視窗中的動作之前，您可以預覽新增和移除的資料庫：

![預覽新增和移除的資料庫](./media/sql-database-elastic-pool-manage-portal/pools-tab.png)。


## 建立及管理彈性工作

彈性工作可讓您對集區中任意數目的資料庫執行 Transact-SQL 指令碼。使用工作之前，請安裝彈性工作元件並提供認證。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。

請參閱[使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)︰使用彈性資料庫工具相應放大、移動資料、查詢或建立交易。


## 其他資源

- [SQL Database 彈性集區](sql-database-elastic-pool.md)
- [使用入口網站來建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md)
- [使用 PowerShell 建立彈性資料庫集區](sql-database-elastic-pool-create-powershell.md)
- [使用 C# 來建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md)
- [彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png

<!---HONumber=AcomDC_0504_2016-->