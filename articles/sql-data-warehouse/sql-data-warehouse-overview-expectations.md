<properties
   pageTitle="SQL 資料倉儲預覽期望 |Microsoft Azure"
   description="公用預覽版功能和我們的 SQL 資料倉儲公開上市目標的摘要。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/11/2016"
   ms.author="nicw;barbkess;sonyama"/>


# SQL 資料倉儲預覽版期望

本文將說明 SQL 資料倉儲預覽版功能，以及我們的服務公開上市 (GA) 目標。我們會在增強公用預覽版功能時持續更新這項資訊。

我們的 SQL 資料倉儲目標：

- 可預測的效能以及高達數 PB 資料的線性延展性
- 所有資料倉儲作業皆有極佳的穩定性
- 跨關聯式與非關聯式資料快速地從載入資料進行至資料深入解析

在 SQL 資料倉儲預覽期間，我們將持續努力達成這些目標。

## 可預測且可調整的效能

SQL 資料倉儲引進了資料倉儲單位 (DWU)，做為測量資料倉儲可用計算資源 (CPU、記憶體、儲存體 I/O) 的方法。增加 DWU 數目可增加資源。當 DWU 數目增加時，SQL 資料倉儲作業可跨越更多分散式資源平行執行 (例如資料載入或查詢)。這可減少延遲並改善效能。

任何資料倉儲都具有 2 個基本效能度量：

- 載入速率。每秒可載入資料倉儲的記錄數量。我們會特別測量可透過 PolyBase 從 Azure Blob 儲存體匯入資料表 (具有叢集資料行存放區索引) 的記錄數量。
- 掃描速率。每秒可從資料倉儲循序擷取的記錄數量。我們會特別測量查詢叢集資料行存放區索引所傳回的記錄數量。

我們正在測量一些重要的效能增強功能，而且很快就會分享預期的速率。在預覽期間，我們將持續增強服務品質 (例如提高壓縮和快取) 以提高這些速率，並確保這些速率依可預期的方式調整。

## 資料保護

SQL 資料倉儲會將所有資料儲存在 Azure 本地備援儲存體中。並在當地的資料中心維護多份資料的同步複本，確保當地語系化失敗時能夠提供透明的資料保護。此外，SQL 資料倉儲會使用 Azure 儲存體快照，定期自動備份作用中 (未暫停) 的資料庫。若要深入了解備份和還原的運作方式，請參閱[備份與還原概觀][]。

## 查詢可靠性

SQL 資料倉儲建置在巨量平行處理 (MPP) 架構上。SQL 資料倉儲會自動偵測並降低計算和控制節點失敗。不過，作業 (例如資料載入或查詢) 可能會由於節點失敗或移轉而失敗。在預覽期間，我們將持續增強功能，儘管節點失敗能可順利完成作業。

## 升級和停機

SQL 資料倉儲會定期進行升級以新增新功能，以及安裝重大修正程式。這些升級可能會產生干擾，而且目前無法在預期的排程上升級。如果您認為這個程序產生過多干擾，建議您[建立支援票證][]，以便我們可以協助您解決此程序。

## 後續步驟

[開始使用][]公用預覽版。

<!--Image references-->

<!--Article references-->
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[開始使用]: ./sql-data-warehouse-get-started-provision.md
[備份與還原概觀]: ./sql-data-warehouse-restore-database-overview.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0615_2016-->