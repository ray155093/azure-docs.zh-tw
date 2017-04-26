---
title: "資料庫相容性層級 130 - Azure SQL Database | Microsoft Docs"
description: "在本文中，我們會探索以相容性層級 130 執行 Azure SQL Database 的優點，並善用新查詢最佳化工具和查詢處理器功能的優點。 我們也會解決對現有 SQL 應用程式的查詢效能可能造成的副作用。"
services: sql-database
documentationcenter: 
author: alainlissoir
manager: jhubbard
editor: 
ms.assetid: 8619f90b-7516-46dc-9885-98429add0053
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.topic: article
ms.date: 03/03/2017
ms.author: alainl
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: a5e84ded4e7b574a24583be507902f9537328153
ms.lasthandoff: 04/10/2017


---
# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>改善 Azure SQL Database 中相容性層級 130 的查詢效能
Azure SQL Database 會在許多不同的相容性層級上以透明方式執行數十萬個資料庫，對其所有客戶保留並保證對應 Microsoft SQL Server 版本的回溯相容性！

在本文中，我們會探索以相容性層級 130 執行 Azure SQL Database 的優點，並善用新查詢最佳化工具和查詢處理器功能的優點。 我們也會解決對現有 SQL 應用程式的查詢效能可能造成的副作用。

當作歷程記錄的提醒，預設相容性層級的 SQL 版本比對如下︰

* 100：在 SQL Server 2008 和 Azure SQL Database 中。
* 110：在 SQL Server 2012 和 Azure SQL Database 中。
* 120：在 SQL Server 2014 和 Azure SQL Database 中。
* 130：在 SQL Server 2016 和 Azure SQL Database 中。

> [!IMPORTANT]
> **新建**資料庫的預設相容性層級為 130。
> 
> 2016 年 6 月中之前建立的資料庫，具有在建立時的相容性層級。 從舊版 Azure SQL Database 移轉的資料庫，相容性層級為 100 或 110。 2016 年 6 月中之前建立的伺服器，其 master 資料庫相容性層級即為伺服器建立時的相容性層級，且無法變更。 master 資料庫和使用者資料庫的相容性層級不同，並不會影響使用者資料庫的功能或效能。
> 

## <a name="about-compatibility-level-130"></a>關於相容性層級 130
首先，如果想要知道資料庫目前的相容性層級，請執行下列 Transact-SQL 陳述式。

```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


在**新建**資料庫變更為層級 130 之前，讓我們透過一些基本的查詢範例來檢閱這項變更的相關資訊，並了解相關人員如何從中受益。

關聯式資料庫中的查詢處理可能非常複雜，而需要有深厚的電腦科學和數學背景，才能了解根本的設計選擇和行為。 本文件的內容已刻意簡化，以確保具有一些最基本技術背景的人員可以了解相容性層級變更的影響，並判斷其對於應用程式有何好處。

我們快速看一下相容性層級 130 對資料表有何好處。  您可以在 [ALTER DATABASE 相容性層級 (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)找到更多詳細資料，但簡短摘要如下︰

* Insert-select 陳述式的 Insert 作業可以是多執行緒作業或可以有平行計劃，而這項作業之前是單一執行緒作業。
* 記憶體最佳化資料表和資料表變數查詢現在可以有平行計劃，而這項作業之前也是單一執行緒作業。
* 記憶體最佳化資料表的統計資料現在可以取樣並會自動更新。 如需詳細資訊，請參閱 [Database Engine 新功能：In-Memory OLTP](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) 。
* 資料行存放區索引的批次模式和資料列模式變更
  * 具有資料行存放區索引的資料表現在會以批次模式排序。
  * 時間範圍彙總現在以批次模式運作，例如 T-SQL LAG/LEAD 陳述式。
  * 具有多個不同子句的資料行存放區資料表會以批次模式進行查詢。
  * 在 DOP = 1 之下執行或具有序列計劃的查詢也會以批次模式執行。
* 最後，基數估計改進將伴隨著相容性層級 120，但如果您是在較低的相容性層級 (也就是 100 或 110) 執行，移到相容性層級 130 也會帶來這些改進，而這些改進也有益於您應用程式的查詢效能。

## <a name="practicing-compatibility-level-130"></a>演練相容性層級 130
首先，我們要建立一些資料表、索引和隨機資料，以演練某些新功能。 T-SQL 指令碼範例可以在 SQL Server 2016 底下或在 Azure SQL Database 底下執行。 不過，在建立 Azure SQL Database 時，務必至少選擇 P2 資料庫，因為您至少需要幾個核心才能允許多執行緒處理，並因而受益於這些功能。

```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


我們現在來看一下相容性層級 130 隨附的一些查詢處理功能。

## <a name="parallel-insert"></a>平行 INSERT
執行下列 T-SQL 陳述式時會在相容性層級 120 和 130 之下執行 INSERT 作業，其分別在單一執行緒模型 (120) 和多執行緒模型 (130) 中執行 INSERT 作業。

```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


藉由要求實際查詢計劃、查看其圖形表示法或其 XML 內容，您即可判斷正在執行哪個基數估計函式。 查看圖 1 的並排計劃，我們可以清楚地看到資料行存放區 INSERT 作業從 120 中的序列執行變成 130 中的平行執行。 此外，請注意 130 計劃中的迭代器圖示變更顯示兩個平行箭號，這說明了迭代器現在的確是平行執行的這項事實。 如果您有大量 INSERT 作業要完成，平行執行 (和資料庫可支配的核心數目連結) 的效果會更佳；視您的情況而言，速度最高可快 100 倍！

*圖 1︰INSERT 作業從序列執行變成在相容性層級 130 的平行執行。*

![圖 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)

## <a name="serial-batch-mode"></a>序列批次模式
同樣地，在處理資料列時移到相容性層級 130 即可進行批次模式處理。 第一，批次模式作業僅適用於您已備妥資料行存放區索引時。 第二，一個批次通常代表 ~900 個資料列，並使用針對多核心 CPU、較高記憶體輸送量最佳化的程式碼邏輯，而且盡可能直接運用資料行存放區的壓縮資料。 在這些條件之下，SQL Server 2016 一次可以處理 ~900 個資料列，而不是一次處理 1 個資料列，因此作業的整體額外成本現在是由整個批次分擔，進而降低各資料列的整體成本。 這個共用作業數目結合資料行存放區壓縮，基本上可減少 SELECT 批次模式作業中的延遲。 您可以在 [資料行存放區索引指南](https://msdn.microsoft.com/library/gg492088.aspx)找到有關資料行存放區和批次模式的詳細資訊。

```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


觀察圖 2 的並排查詢計劃，我們可以看到處理模式已隨相容性層級變更，因此，同時在兩個相容性層級執行查詢時，我們可以看到在處理 2 個批次之際，大部分的處理時間花費在資料列模式 (86%) (相較於批次模式 (14%))。 增加資料集，優點也會隨之增加。

*圖 2︰SELECT 作業從序列變成在相容性層級 130 的批次模式。*

![圖 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)

## <a name="batch-mode-on-sort-execution"></a>排序執行的批次模式
類似前述範例，但套用至排序作業後，從資料列模式 (相容性層級 120) 轉換為批次模式 (相容性層級 130) 可提升 SORT 作業的效能，其理由相同。

```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


如圖 3 並排顯示，我們可以看到資料列模式的排序作業代表 81% 的成本，而批次模式只代表 19% 的成本 (排序本身分別佔 81% 和 56%)。

*圖 3︰SORT 作業從資料列模式變成在相容性層級 130 的批次模式。*

![圖 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)

這些範例明顯只包含數萬個資料列，遠低於目前大部分 SQL Server 中可用的資料。 針對數百萬個資料列進行預估，依您的工作負載本質而異，如此可轉換成每天省下數分鐘的執行時間。

## <a name="cardinality-estimation-ce-improvements"></a>基數估計 (CE) 改進
從 SQL Server 2014 開始，執行相容性層級 120 或更高層級的資料庫，將使用新的基數估計功能。 基本上，基數估計是用於根據其預估成本來判斷 SQL Server 將如何執行查詢的邏輯。 這項估計是使用來自與該查詢所牽涉物件相關聯的統計資料的輸入來計算。 實際上，概括來說，基數估計函式是資料列計數估計值，以及下列各項的相關資訊：值分佈、相異值計數，以及查詢中參考的資料表和物件內含的重複計數。 如果這些估計值錯誤，可能會因為授與的記憶體不足 (也就是 TempDB 溢出) 或選取序列計劃執行 (而非平行計劃執行) 等等，而導致不必要的磁碟 I/O。 總而言之，不正確的估計值可能會導致查詢執行的整體效能降低。 另一方面，更完善的估計值、更精確的估計值會導致更好的查詢執行！

如之前所述，查詢最佳化和評估值是複雜的事物，但如果您想要深入了解查詢計劃和基數估算器，您可以參考 [使用 SQL Server 2014 基數估計器來最佳化查詢計劃](https://msdn.microsoft.com/library/dn673537.aspx) 文件，以獲得深入說明。

## <a name="which-cardinality-estimation-do-you-currently-use"></a>您目前使用哪個基數估計？
若要判斷您的查詢正在執行哪個基數估計，請使用下列查詢範例。 第一個範例會在相容性層級 110 之下執行，暗示使用舊的基數估計函式。

```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


執行完成後，按一下 XML 連結，並查看第一個迭代器的屬性。 請注意，名為 CardinalityEstimationModelVersion 的屬性名稱目前設定為 70。 這並不表示資料庫相容性層級設定為 SQL Server 7.0 版 (如先前的 T-SQL 陳述式所示，其設定於 110)，但值 70 代表自 SQL Server 7.0 起可用的舊版基數估計功能，此功能直到 SQL Server 2014 (隨附相容性層級 120) 以後才有重大改版。

*圖 4：使用相容性層級 110 或更低層級時，CardinalityEstimationModelVersion 會設定為 70。*

![圖 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)

或者，您可以將相容性層級變更為 130，並使用設定為 NO 的 LEGACY_CARDINALITY_ESTIMATION 搭配 [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) 來停止使用新的基數估計函式。 從基數估計函式的觀點來看，這與使用 110 相同，同時使用最新的查詢處理相容性層級。 如此，您就可以受益於最新相容性層級隨附的新查詢處理功能 (也就是批次模式)，但必要時仍會依賴舊的基數估計功能。

```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


只要移到相容性層級 120 或 130，即可啟用新的基數估計功能。 在這種情況下，預設 CardinalityEstimationModelVersion 設定為 120 或 130。

```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*圖 5：使用相容性層級 130 時，CardinalityEstimationModelVersion 會設定為 130。*

![圖 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)

## <a name="witnessing-the-cardinality-estimation-differences"></a>見證基數估計差異
現在，讓我們執行稍微複雜的查詢 (牽涉到包含一個 WHERE 子句及一些述詞的 INNER JOIN)，而且先查看舊基數估計函式的資料列計數估計值。

```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


有效地執行這項查詢會傳回 200,704 個資料列，而透過舊基數估計功能的資料列預估值宣告 194,284 個資料列。 很明顯地，如之前所述，這些資料列計數結果還是會視您執行先前範例的頻率而定，在每次執行時一再填入範例資料表。 顯然，除了資料表圖形、資料內容，以及此資料彼此相互關聯的方式，查詢中的述詞也會影響實際估計。

*圖 6︰資料列計數估計值為 194,284 或與預期的 200,704 個資料列相差 6,000 個資料列。*

![圖 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)

同樣地，我們現在使用新的基數估計功能執行相同的查詢。

```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


我們現在看到資料列估計值為 202,877，或更加接近且高於舊的基數估計。

*圖 7︰資料列計數估計現在是 202,877，而不是 194,284。*

![圖 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)

事實上，結果集為 200,704 個資料列 (但全都取決於您執行先前範例查詢的頻率，而更重要的是，因為 T-SQL 使用 RAND() 陳述式，所以每次執行傳回的實際值會有所不同)。 因此，在此特殊範例中，新的基數估計在估計資料列數時的效果更好，因為 202,877 比 194,284 更接近 200,704！ 最後，如果您將 WHERE 子句變更為等號 (舉例來說，而非 “>”)，這可能會使新舊基數函式之間的估計值更加不同 (視您可以取得多少相符項目而定)。

很明顯地，在此情況下，與實際計數相差 ~6000 個資料列，在某些情況下並不代表大量資料。 現在，將此變換成跨數個資料表的數百萬個資料列和更複雜的查詢，而有時候估計值可能會相差數百萬個資料列，因此，將會有較高的風險挑到錯誤的執行計畫，或要求授與的記憶體不足 (導致 TempDB 溢出和更多的 I/O)。

如果有機會，請使用您最典型的查詢和資料集來練習這項比較，並親自查看舊估計值和新估計值受到的影響，而有些估計值可能變得與事實相差更遠，有些估計值則可能更接近結果集中傳回的實際資料列計數。 這全都取決於您的查詢圖形、Azure SQL Database 特性、資料集的本質和大小，以及其相關的可用統計資料。 如果您剛建立 Azure SQL Database 執行個體，查詢最佳化工具必須從頭建立其知識，而不是重複使用先前查詢執行所構成的統計資料。 因此，估計值非常情境式，而且幾乎是每個伺服器和應用程式情況所特有。 這是要牢記在心的重要層面！

## <a name="some-considerations-to-take-into-account"></a>需要考量的一些注意事項
雖然大部分的工作負載受益於相容性層級 130，但是在對您的生產環境採用此相容性層級之前，基本上有 3 個選項︰

1. 請移到相容性層級 130，並查看如何執行動作。 如果您發現一些效能衰退，請將相容性層級設回其原始層級，或保留 130，而只讓基數估計回復到舊版模式 (如前文所述，這可獨力處理此問題)。
2. 在正式推出前，您可在類似的生產負載之下徹底測試現有的應用程式、進行微調，以及驗證效能。 若有任何問題，如先前所述，您可以隨時回到原始的相容性層級，或讓基數估計回復到舊版模式。
3. 做為處理這些問題的最後一個選項，最新的方法是使用「查詢存放區」。 這是現今的建議選項！ 為了協助分析在相容性層級 120 或更低層級與相容性層級 130 之下的查詢，我們非常鼓勵您使用查詢存放區。 查詢存放區於最新版的 Azure SQL Database 中提供，而且是設計用來協助您進行查詢效能疑難排解。 將查詢存放區視為您的資料庫的飛行資料記錄器，其可收集和呈現關於所有查詢的詳細歷程記錄資訊。 這可減少診斷和解決問題所需的時間，進而大幅簡化效能鑑識。 如需詳細資訊，請參閱 [查詢存放區︰您的資料庫的飛行資料記錄器](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)。

概括而言，如果您已經有一組在相容性層級 120 或更低層級執行的資料庫，並規劃將其中一些資料庫移到 130，或因為您的工作負載自動佈建預設為 130 的新資料庫，請考慮下列各項︰

* 在變更為生產環境中的新相容性層級之前，啟用查詢存放區。 如需詳細資訊，您可以參考 [變更資料庫相容性模式和使用查詢存放區](https://msdn.microsoft.com/library/bb895281.aspx) 。
* 接下來，使用具代表性的資料和類似生產環境的查詢來測試所有重要工作負載，並且比較所經歷的效能與存放區所報告的效能。 如果您遇到一些效能衰退，您可以找出衰退的查詢存放區查詢，並使用查詢存放區提供的計畫強制選項 (也稱為計畫關聯)。 在這種情況下，您肯定會保持相容性層級 130，並使用查詢存放區的建議的先前查詢計畫。
* 如果您想要使用 Azure SQL Database (執行 SQL Server 2016) 的新特性與功能，但容易受到相容性層級 130 所帶來變更的影響，則最後的手段是考慮使用 ALTER DATABASE 陳述式，讓相容性層級強制回到符合您工作負載的層級。 但首先，請注意查詢存放區計畫關聯選項是最佳的選擇，因為不使用 130 基本上會保持在舊版 SQL Server 的功能層級。
* 如果您有橫跨多個資料庫的多租用戶應用程式，則可能必須更新您的資料庫的佈建邏輯，以確保所有資料庫 (舊的和新佈建的) 的相容性層級都一致。 應用程式工作負載效能可能很容易受某些資料庫在不同相容性層級執行的這個事實所影響，因此，所有資料庫的相容性層級都必須一致，以便為所有的客戶提供相同的經驗。 這並非必要，實際上取決於您的應用程式受相容性層級影響的程度。
* 最後，關於「基數估計」，建議在新的條件下測試您的生產工作負載，以判斷您的應用程式是否受益於「基數估計」改進。

## <a name="conclusion"></a>結論
使用 Azure SQL Database 以受益於所有的 SQL Server 2016 增強功能，可以明顯地改善查詢執行。 當然，如同任何新功能，必須執行適當的評估，才能判斷您的資料庫工作負載運作最佳的確切條件。 經驗顯示，使用新的查詢處理函式和新的基數估計時，在相容性層級 130 之下，大部分的工作負載至少可以順利執行。 實際上，總有一些例外狀況，而進行適當的審慎調查是很重要的評估，用來判斷您可以從這些增強功能獲益多少。 同樣地，查詢存放區對於執行這項工作很有幫助！

## <a name="references"></a>參考
* [Database Engine 新功能](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)
* [部落格︰查詢存放區︰您的資料庫的飛行資料記錄器 (Borko Novakovic，2016 年 6 月 8 日)](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)
* [ALTER DATABASE 相容性層級 (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)
* [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)
* [Azure SQL Database 的相容性層級 130](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)
* [使用 SQL Server 2014 基數估計器 (CE) 來最佳化查詢計劃](https://msdn.microsoft.com/library/dn673537.aspx)
* [資料行存放區索引指南](https://msdn.microsoft.com/library/gg492088.aspx)
* [落格︰改善 Azure SQL Database 中相容性層級 130 的查詢效能 (Alain Lissoir，2016 年 5 月 6 日)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)


