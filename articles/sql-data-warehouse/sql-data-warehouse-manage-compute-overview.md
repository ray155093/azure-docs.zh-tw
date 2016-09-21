<properties
   pageTitle="管理 Azure SQL 資料倉儲中的計算能力 (概觀) | Microsoft Azure"
   description="Azure SQL 資料倉儲中的效能相應放大功能。藉由調整 DWU 以相應放大或暫停和繼續計算資源來節省成本。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# 管理 Azure SQL 資料倉儲中的計算能力 (概觀)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-manage-compute-overview.md)
- [入口網站](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

SQL 資料倉儲的架構分隔儲存體和計算功能，可單獨進行調整。如此一來，您可以相應放大效能，同時只有在需要時支付效能以便節省成本。

本概觀說明 SQL 資料倉儲的下列效能相應放大功能，並提供如何及何時使用它們的建議。

- 藉由調整[資料倉儲單位 (DWU)][] 來調整計算能力
- 暫停或繼續計算資源

<a name="scale-performance-bk"></a>

## 調整效能

在 SQL 資料倉儲中，您可以快速地將效能相應放大或調整回來，方法是增加或減少 CPU、記憶體和 I/O 頻寬的計算資源。若要調整效能，您只需要調整 SQL 資料倉儲配置給您的資料庫的[資料倉儲單位 (DWU)][] 數目。SQL 資料倉儲會快速進行變更，並處理硬體或軟體的所有基礎變更。

您再也不需要為了獲得優異的資料倉儲效能，而研究處理器類型、需要的記憶體容量或儲存空間類型。將您的資料倉儲放在雲端，就不再需要處理低階的硬體問題。相反地，SQL 資料倉儲要問您：您想要多快的資料分析速度？

### 如何調整效能？

若要彈性增加或減少計算能力，只要變更您的資料庫的[資料倉儲單位 (DWU)][]。隨著您新增更多 DWU，效能將呈線性比例上升。在更高的 DWU 層級，您需要加入超過 100 個 DWU，才能注意到顯著的效能改善。為了協助您在 DWU 中選取有意義的跳躍點，我們提供的 DWU 層級可提供最佳的結果。
 
若要調整 DWU，您可以使用任何一種個別的方法。

- [使用 Azure 入口網站調整計算能力][]
- [使用 PowerShell 調整計算能力][]
- [使用 REST API 調整計算能力][]
- [使用 TSQL 調整計算能力][]

### 應該使用多少 DWU 呢？
 
SQL 資料倉儲的效能是以線性方式調整，在幾秒內就能從某個計算比例變更為另一個 (例如從 100 個 DWU 至 2000 個 DWU)。這可讓您彈性地試驗不同的 DWU 設定，直到您決定最適合您的案例的設定。

為了要了解您理想的 DWU 值，嘗試在載入您的資料之後相應增加和相應減少並執行幾個查詢。由於調整很快速，您可以在一小時內嘗試一些不同層級的效能。請記住，SQL 資料倉儲是設計用來處理大量資料，以及查看其真正的調整功能 (尤其是我們所提供的較大規模)，所以您會想要使用接近或超過 1 TB 的大型資料集。

尋找您的工作負載的最佳 DWU 的建議事項︰

1. 若是開發過程中的資料倉儲，可從少量的 DWU 開始。DW400 或 DW200 是不錯的起點。
2. 監視應用程式效能，觀察比較所選 DWU 數目與您觀察到的效能。
3. 透過假設線性標尺，判斷需要將效能加快或減慢多少才能達到您需求的最佳效能等級。
4. 增加或減少 DWU 數目與您想要讓您的工作負載更快或更慢執行成正比。服務會迅速回應並調整計算資源，以符合新的 DWU 需求。
5. 繼續進行調整，直到達到您業務需求的最佳效能為止。

### 何時調整 DWU？

當您需要更快的結果時，提升您的 DWU 並且為較高的效能支付款項。當您需要較小的計算能力時，減少您的 DWU 並且僅針對您需要的項目支付款項。

DWU 調整時機的建議︰

1. 如果應用程式的工作負載持續變動，請將 DWU 等級上移或下移，以配合尖峰和離峰點。例如，如果工作負載尖峰通常落在月底，可規劃在尖峰天數期間新增更多 DWU，然後在尖峰期間結束之後相應減少。
2. 執行大量資料載入或轉換作業之前，相應增加 DWU 以使您的資料更快速可供使用。

<a name="pause-compute-bk"></a>

## 暫停計算

[AZURE.INCLUDE [SQL 資料倉儲暫停描述](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，您可以使用任何一種個別的方法。

- [使用 Azure 入口網站暫停計算][]
- [使用 PowerShell 暫停計算][]
- [使用 REST API 暫停計算][]

<a name="resume-compute-bk"></a>

## 繼續計算

[AZURE.INCLUDE [SQL 資料倉儲繼續描述](../../includes/sql-data-warehouse-resume-description.md)]

若要繼續資料庫，您可以使用任何一種個別的方法。

- [使用 Azure 入口網站繼續計算][]
- [使用 PowerShell 繼續計算][]
- [使用 REST API 繼續計算][]

## 權限

調整資料庫需要 [ALTER DATABASE][] 所述的權限。暫停和繼續需要 [SQL DB 參與者][]權限，特別是 Microsoft.Sql/servers/databases/action。

<a name="next-steps-bk"></a>

## 後續步驟
請參閱下列文章，以瞭解其他一些關鍵效能概念：

- [工作負載和並行存取管理][]
- [資料表設計概觀][]
- [資料表散發][]
- [索引資料表的編製][]
- [資料表分割][]
- [資料表統計資料][]
- [最佳作法][]

<!--Image reference-->

<!--Article references-->
[資料倉儲單位 (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[使用 Azure 入口網站調整計算能力]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[使用 PowerShell 調整計算能力]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[使用 REST API 調整計算能力]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[使用 TSQL 調整計算能力]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[使用 Azure 入口網站暫停計算]: ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[使用 PowerShell 暫停計算]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[使用 REST API 暫停計算]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[使用 Azure 入口網站繼續計算]: ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[使用 PowerShell 繼續計算]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[使用 REST API 繼續計算]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[工作負載和並行存取管理]: ./sql-data-warehouse-develop-concurrency.md
[資料表設計概觀]: ./sql-data-warehouse-tables-overview.md
[資料表散發]: ./sql-data-warehouse-tables-distribute.md
[索引資料表的編製]: ./sql-data-warehouse-tables-index.md
[資料表分割]: ./sql-data-warehouse-tables-partition.md
[資料表統計資料]: ./sql-data-warehouse-tables-statistics.md
[最佳作法]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB 參與者]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/

<!----HONumber=AcomDC_0907_2016-->