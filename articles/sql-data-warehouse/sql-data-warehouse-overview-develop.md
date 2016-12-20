---
title: "SQL 資料倉儲的設計決策和程式碼撰寫技術開發 | Microsoft Docs"
description: "SQL 資料倉儲的開發概念、設計決策、建議和程式碼撰寫技巧。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d1c2255264a4240eecc51199050f8406d141ace2


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL 資料倉儲的設計決策和程式碼撰寫技術
若要進一步瞭解 SQL 資料倉儲的重要的設計決策、建議和程式碼撰寫技術，請參閱這些開發文章。

## <a name="key-design-decisions"></a>主要的設計決策
下列文章的重點在於使用 SQL 資料倉儲開發分散式資料倉儲時，必須了瞭解的一些重要概念和設計決策：

* [連接][連接]
* [並行][並行]
* [交易][交易]
* [使用者定義的結構描述][使用者定義的結構描述]
* [資料表散發][資料表散發]
* [資料表索引][資料表索引]
* [資料表分割][資料表分割]
* [CTAS][CTAS]
* [統計資料][統計資料]

## <a name="development-recommendations-and-coding-techniques"></a>開發建議和程式碼撰寫技術
這些文章會強調特定的程式碼撰寫技術、秘訣和建議，用於開發您的 SQL 資料倉儲：

* [預存程序][預存程序]
* [標籤][標籤]
* [檢視][檢視]
* [暫存資料表][暫存資料表]
* [動態 SQL][動態 SQL]
* [迴圈][迴圈]
* [依據選項分組][依據選項分組]
* [變數指派][變數指派]

## <a name="next-steps"></a>後續步驟
仔細閱讀開發文章之後，請查看 [Transact-SQL 參考資料][Transact-SQL 參考資料]頁面，以取得 SQL 資料倉儲支援的語法的詳細資料。

<!--Image references-->

<!--Article references-->
[並行]: ./sql-data-warehouse-develop-concurrency.md
[連接]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[動態 SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[依據選項分組]: ./sql-data-warehouse-develop-group-by-options.md
[標籤]: ./sql-data-warehouse-develop-label.md
[迴圈]: ./sql-data-warehouse-develop-loops.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md
[預存程序]: ./sql-data-warehouse-develop-stored-procedures.md
[資料表散發]: ./sql-data-warehouse-tables-distribute.md
[資料表索引]: ./sql-data-warehouse-tables-index.md
[資料表分割]: ./sql-data-warehouse-tables-partition.md
[暫存資料表]: ./sql-data-warehouse-tables-temporary.md
[交易]: ./sql-data-warehouse-develop-transactions.md
[使用者定義的結構描述]: ./sql-data-warehouse-develop-user-defined-schemas.md
[變數指派]: ./sql-data-warehouse-develop-variable-assignment.md
[檢視]: ./sql-data-warehouse-develop-views.md
[Transact-SQL 參考資料]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[重新命名物件]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


