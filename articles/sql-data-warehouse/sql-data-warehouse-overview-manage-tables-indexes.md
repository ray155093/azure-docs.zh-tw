<properties
   pageTitle="管理 Azure SQL 資料倉儲中的資料表和索引 | Microsoft Azure"
   description="管理 Azure SQL 資料倉儲中資料表和索引之考量、最佳做法及工作的概觀。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 管理 Azure SQL 資料倉儲中的資料表和索引

管理 SQL 資料倉儲中資料表和索引之考量、最佳做法及工作的概觀。



| 類別 | 工作或考量 | 說明 |
| :-----------------------| :---------------------------------------------- | :----------- |
| 資料行存放區索引 | 在載入或插入資料後重建索引 | 根據預設，SQL 資料倉儲會將每個資料表儲存為叢集資料行存放區索引。這可以讓您取得更好的效能和資料壓縮，特別是針對大型資料表。根據您將資料擷取至資料行存放區索引的方式，可能會有一些資料不會以資料行存放區壓縮的方式儲存。當發生此情況時，您可能無法取得資料行存放區索引被設計來提供的效能。<br/><br/>若要確保您的資料行存放區索引處於狀況良好狀態，請參閱[管理資料行存放區索引][]。 |
| 雜湊分散式資料表 | 確認資料已平均分散在節點上 | 雜湊分散式資料表通常是最佳化大型資料表上之聯結和彙總的最佳方式。SQL 資料倉儲會根據指定的散發資料行散發資料表。某些資料行可以做為良好的散發索引鍵，某些則不適合。您的目的是使資料列平均分散。雖然部分程度的不平衡或資料扭曲不會造成太大的影響，但在資料扭曲太過嚴重的情況下，您將會失去 SQL 資料倉儲被設計來提供的巨量平行處理 (MPP) 優點。<br/><br/>若要確保您的雜湊分散式資料表沒有過多資料扭曲，請參閱[管理散發資料扭曲][]。 |





## 後續步驟

如需更多管理祕訣，請移至[管理概觀][]。

<!--Image references-->

<!--Article references-->
[管理資料行存放區索引]: sql-data-warehouse-manage-columnstore-indexes.md
[管理散發資料扭曲]: sql-data-warehouse-manage-distributed-data-skew.md
[管理概觀]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->