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
	ms.date="05/09/2016"
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


您可以使用 Azure 入口網站來監視及管理彈性資料庫集區和集區中的資料庫。從入口網站，您可以監視彈性集區與集區內資料庫的使用率。也可以對彈性集區進行一些變更，並且一次提交所有的變更。這些變更包括新增或移除資料庫、變更您的彈性集區設定，或變更您的資料庫設定。

下圖顯示彈性集區範例。此檢視包括︰

*  圖表，可供監視彈性集區和集區中內含資料庫的資源使用量。 
*  [設定] 集區按鈕，以對彈性集區進行變更。 
*  [建立資料庫] 按鈕，以建立新的資料庫並將它加入至目前的彈性集區。
*  彈性工作，可藉由對清單中的所有資料庫執行 Transact SQL 指令碼，協助您管理大量資料庫。 

![集區檢視][2]

若要進行本文中的步驟，Azure 中需要至少有一個資料庫和彈性集區的 SQL Server。如果您沒有彈性集區，請參閱[建立集區](sql-database-elastic-pool-create-portal.md)；如果您沒有資料庫，請參閱 [SQL Database 教學課程](sql-database-get-started.md)。

## 彈性集區監視

您可以移至特定集區，以查看其資源使用率。根據預設，集區已設定為顯示過去 1 小時內的儲存體和 eDTU 使用量。此圖表可以設定為顯示各種時間範圍的不同度量。

1. 選擇要使用的集區。
2. [彈性集區監視] 之下的是標示為 [資源使用率] 的圖表。按一下此圖表。

	![彈性集區監視][3]

	[度量] 刀鋒視窗將會開啟，顯示指定時間範圍內指定度量的詳細檢視。

	![Metric blade][9]

### 自訂圖表顯示

您可以編輯此圖表和 [度量] 刀鋒視窗，以顯示其他度量，例如所用的 CPU 百分比、資料 IO 百分比和記錄 IO 百分比。
 
2. 在 [度量] 刀鋒視窗上，按一下 [編輯]。

	![按一下 [編輯]。][6]

- 在 [編輯圖表] 刀鋒視窗中，選取新的時間範圍 (過去 1 小時、今天或上一週)，或按一下 [自訂] 以選取過去兩週內的任何日期範圍。選取圖表類型 (長條圖或折線圖)，再選取要監視的資源。 

	![按一下 [編輯]。](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- 然後按一下 [確定]。


## 彈性資料庫監視

您也可以監視個別資料庫的潛在問題。

1. 在 [彈性資料庫監視] 之下，有一個圖表可顯示五個資料庫的度量。根據預設，此圖表會依過去一小時內的平均 eDTU 使用量顯示集區中的前 5 個資料庫。按一下此圖表。

	![彈性集區監視][4]

2. [資料庫資源使用率] 刀鋒視窗隨即出現。這會提供集區中資料庫使用量的詳細檢視。使用刀鋒視窗下半部中的方格，可以選取集區中的任何資料庫，以在圖表中顯示其使用量 (最多 5 個資料庫)。也可以按一下 [編輯圖表]，自訂圖表中顯示的度量和時間範圍。

	![資料庫資源使用率刀鋒視窗][8]

### 自訂檢視

1. 在 [資料庫資源使用率] 刀鋒視窗中，按一下 [編輯圖表]。

	![按一下編輯圖表](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. 在 [編輯圖表] 刀鋒視窗中，選取新的時間範圍 (過去 1 小時或過去 24 小時)，或按一下 [自訂] 以選取要顯示過去 2 週內的不同一天。

	![按一下自訂](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. 按一下 [比較資料庫依據] 下拉式清單，選取比較資料庫時所要使用的不同度量。

	![編輯圖表](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### 選取要監視的資料庫

在 [資料庫資源使用率] 刀鋒視窗的資料庫清單中，瀏覽清單中的頁面或輸入資料庫的名稱，即可找到特定的資料庫。使用此核取方塊來選取資料庫。

![搜尋要監視的資料庫][7]
  

## 將警示加入集區資源

您可以將規則加入資源，以在當資源達到您設定的使用率閾值時，傳送電子郵件給人員或傳送警示字串到 URL 端點。

**將警示加入任何資源：**

1. 按一下 [資源使用率] 圖表以開啟 [度量] 刀鋒視窗，按一下 [加入警示]，然後在 [加入警示規則] 刀鋒視窗中填寫資訊 ([資源] 會自動設定為使用中的集區)。
2. 輸入可供您和收件者辨別警示的 [名稱] 和 [描述]。
3. 從清單選擇要提出警示的 [度量]。

    圖表會以動態方式顯示該度量的資源使用量，協助您選擇閾值。

4. 選擇 [條件] \(大於、小於等) 和 [閾值]。
5. 按一下 [確定]。



## 將資料庫移入彈性集區

您可以從現有的集區中新增或移除資料庫。資料庫可以位於其他集區。不過，您只可以新增位於相同邏輯伺服器上的資料庫。

1. 在集區刀鋒視窗的 [彈性資料庫] 下，按一下 [設定集區]。

    ![按一下 [設定集區]][1]

2. 在 [設定集區] 刀鋒視窗中，按一下 [新增至集區]。

	![按一下 [新增到集區]](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. 在 [新增資料庫] 刀鋒視窗中，選取要新增到集區中的資料庫。然後按一下 [選取]。

	![選取要新增的資料庫](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    [設定集區] 刀鋒視窗現在會列出您選取要新增的資料庫，其狀態設為 [處理中]。

    ![擱置中的新增集區](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. 在 [設定集區] 刀鋒視窗中，按一下 [儲存]。

    ![按一下 [儲存]。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## 將資料庫移出彈性集區

1. 在 [設定集區] 刀鋒視窗中，選取要移除的資料庫。

    ![資料庫清單](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. 按一下 [從集區移除]。

    ![資料庫清單](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    [設定集區] 刀鋒視窗現在會列出您選取要移除的資料庫，其狀態設為 [處理中]。
    
    ![預覽新增和移除的資料庫](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. 在 [設定集區] 刀鋒視窗中，按一下 [儲存]。

    ![按一下 [Save] \(儲存)。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## 變更集區的效能設定

當您監視集區的資源使用率時，可能會發現需要一些調整。也許集區的效能或儲存體限制需要變更。您可能想要變更集區中的資料庫設定。您可以隨時變更集區設定，以在效能和成本之間取得最佳平衡。請參閱[何時應該使用彈性資料庫集區？](sql-database-elastic-pool-guidance.md)以取得更多資訊。

**若要變更每個集區的 eDTU 和儲存體限制，以及每個資料庫的 eDTU：**

1. 開啟 [設定集區] 刀鋒視窗。

    在 [彈性資料庫集區設定] 下，使用滑桿來變更集區設定。

    ![彈性集區資源使用量](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 當設定變更時，則會顯示變更的每月預估成本。

    ![更新集區和新的每月成本](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## 建立及管理彈性工作

彈性工作可讓您對集區中任意數目的資料庫執行 Transact-SQL 指令碼。您可以使用入口網站建立新的作業，或管理現有的作業。

![建立及管理彈性工作][5]


使用工作之前，請安裝彈性工作元件並提供認證。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。

請參閱[使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)︰使用彈性資料庫工具相應放大、移動資料、查詢或建立交易。



## 其他資源

- [SQL Database 彈性集區](sql-database-elastic-pool.md)
- [使用入口網站來建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md)
- [使用 PowerShell 建立彈性資料庫集區](sql-database-elastic-pool-create-powershell.md)
- [使用 C# 來建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md)
- [彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png

<!---HONumber=AcomDC_0511_2016-->