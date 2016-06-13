<properties
   pageTitle="Azure SQL 資料倉儲的效能延展性 | Microsoft Azure"
   description="了解 Azure SQL 資料倉儲中的效能相應放大功能。藉由調整 DWU 以相應放大或暫停和繼續計算資源來節省成本。"
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
   ms.date="05/26/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL 資料倉儲的效能延展性

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-scalability.md)
- [入口網站](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

SQL 資料倉儲的架構分隔儲存體和計算功能，可單獨進行調整。如此一來，您可以相應放大效能，同時只有在需要時支付效能以便節省成本。

本概觀說明 SQL 資料倉儲的下列效能相應放大功能，並提供如何及何時使用它們的建議。

- 藉由調整資料倉儲單位 (DWU) 來調整效能
- 暫停或繼續計算資源

<a name="scale-performance-bk"></a>

## 調整效能

在 SQL 資料倉儲中，您可以快速地將效能相應放大或調整回來，方法是增加或減少 CPU、記憶體和 I/O 頻寬的計算資源。若要調整效能，您只需要調整 SQL 資料倉儲配置到您的資料庫的資料倉儲單位 (DWU) 的數目。SQL 資料倉儲會快速進行變更，並處理硬體或軟體的所有基礎變更。

>[AZURE.NOTE] 您再也不需要為了獲得優異的資料倉儲效能，而研究處理器類型、需要的記憶體容量或儲存空間類型。將您的資料倉儲放在雲端，就不再需要處理低階的硬體問題。相反地，SQL 資料倉儲要問您：您想要多快的資料分析速度？

### 何謂 DWU？

資料倉儲單位 (DWU) 是您的資料庫在任何指定時間的基礎計算功能的量值。當 DWU 數目增加時，SQL 資料倉儲作業可跨越更多分散式 CPU 和記憶體資源平行執行 (例如資料載入或查詢)。這可減少延遲並改善效能。

DWU 是以載入速率和掃描速率為基礎。當您增加 DWU 時，會增加載入速率和掃描速率。

- **載入速率**。SQL 資料倉儲每秒可擷取的記錄數量。具體來說，這是 SQL 資料倉儲可以使用 PolyBase，從 Azure Blob 儲存體匯入至叢集資料行存放區索引的記錄數目。 

- **掃描速率**。查詢每秒可以從 SQL 資料倉儲擷取的記錄數目。具體來說，這是 SQL 資料倉儲可以藉由在叢集資料行存放區索引上執行資料倉儲查詢，所傳回的記錄數目。若要測試掃描速率，我們會使用標準的資料倉儲查詢，以掃描大量的資料列，並接著執行複雜的彙總。這是 IO 和 CPU 密集作業。

>[AZURE.NOTE] 我們正在測量和進行一些重要的效能增強功能，而且很快就會分享預期的速率。在預覽期間，我們將持續增強服務品質 (例如提高壓縮和快取) 以提高這些速率，並確保這些速率依可預期的方式調整。

如需 DWU 的清單，請參閱[容量限制][]文章中的「服務等級目標」。

### 如何調整效能？

若要彈性增加或減少計算能力，只要變更您的資料庫的資料倉儲單位 (DWU)。在幕後，「SQL 資料倉儲」會停止您的執行個體、變更 CPU 和記憶體配置，然後重新啟動您的執行個體。

DWU 配置在 100 個區塊中，但並非所有區塊都可以使用。當 DWU 增加時，效能呈線性增加。在更高的 DWU 層級，您需要加入超過 100 個 DWU，才能注意到顯著的效能改善。為了協助您在 DWU 中選取有意義的跳躍點，我們提供的 DWU 層級可提供最佳的結果。
 
若要調整 DWU，您可以使用任何一種個別的方法。

- [使用 Azure 入口網站調整效能][]
- [使用 PowerShell 調整效能][]
- [使用 REST API 調整效能][]
- [使用 TSQL 調整效能][]

### 應該使用多少 DWU 呢？
 
SQL 資料倉儲的效能是以線性方式調整，在幾秒內就能從某個計算比例變更為另一個 (例如從 100 個 DWU 至 2000 個 DWU)。這可讓您彈性地試驗不同的 DWU 設定，直到您決定最適合您的案例的設定。

> [AZURE.NOTE] 您可能不會在較低的資料量中看到預期的效能調整。我們建議從等於或大於 1 TB 的資料磁碟區開始，以便取得準確的效能測試結果。

尋找您的工作負載的最佳 DWU 的建議事項︰

1. 若是開發過程中的資料倉儲，可從少量的 DWU 開始。
2. 監視應用程式效能，觀察比較所選 DWU 數目與您觀察到的效能。
3. 透過假設線性標尺，判斷需要將效能加快或減慢多少才能達到您需求的最佳效能等級。
4. 增加或減少 DWU 數目與您想要讓您的工作負載更快或更慢執行成正比。服務會迅速回應並調整計算資源，以符合新的 DWU 需求。
5. 繼續進行調整，直到達到您業務需求的最佳效能為止。

### 何時調整 DWU？

整體來說，我們想要讓 DWU 簡單。當您需要更快的結果時，提升您的 DWU 並且為較高的效能支付款項。當您需要較小的計算能力時，減少您的 DWU 並且僅針對您需要的項目支付款項。

DWU 調整時機的建議︰

1. 如果應用程式的工作負載持續變動，請將 DWU 等級上移或下移，以配合尖峰和離峰點。例如，如果工作負載尖峰通常落在月底，可規劃在尖峰天數期間新增更多 DWU，然後在尖峰期間結束之後相應減少。
1. 執行大量資料載入或轉換作業之前，相應增加 DWU 以使您的資料更快速可供使用。

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

<a name="next-steps-bk"></a>

## 後續步驟
請參閱下列文章，以瞭解一些關鍵效能與調整的其他概念：

- [並行模型][]
- [設計資料表][]
- [為資料表選擇雜湊散發索引鍵][]
- [用來改善效能的統計資料][]

<!--Image reference-->

<!--Article references-->

[使用 Azure 入口網站調整效能]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-1-scale-performance
[使用 PowerShell 調整效能]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-1-scale-performance
[使用 REST API 調整效能]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-1-scale-performance
[使用 TSQL 調整效能]: ./sql-data-warehouse-manage-scale-out-tasks-tsql.md

[容量限制]: ./sql-data-warehouse-service-capacity-limits.md

[使用 Azure 入口網站暫停計算]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-2-pause-compute
[使用 PowerShell 暫停計算]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-2-pause-compute
[使用 REST API 暫停計算]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-2-pause-compute
[使用 Azure 入口網站繼續計算]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-3-resume-compute
[使用 PowerShell 繼續計算]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-3-resume-compute
[使用 REST API 繼續計算]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-3-resume-compute

[並行模型]: ./sql-data-warehouse-develop-concurrency.md
[設計資料表]: ./sql-data-warehouse-develop-table-design.md
[為資料表選擇雜湊散發索引鍵]: ./sql-data-warehouse-develop-hash-distribution-key.md
[用來改善效能的統計資料]: ./sql-data-warehouse-develop-statistics.md
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0601_2016-->