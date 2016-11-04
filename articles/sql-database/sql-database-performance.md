---
title: Azure SQL Database 效能深入解析 | Microsoft Docs
description: Azure SQL Database 會提供可協助您找出可改善目前查詢效能區域的效能工具。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: monicar

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/19/2016
ms.author: sstein

---
# SQL Database 效能深入解析
Azure SQL Database 藉由提供智慧型的調整動作與建議，提供可協助您識別並改善資料庫效能的效能工具。

1. 在 [Azure 入口網站](http://portal.azure.com)中瀏覽至您的資料庫，然後按一下 [所有設定] > [效能] > [概觀] 以開啟 [效能] 頁面。
2. 按一下 [建議] 以開啟 [SQL Database 建議程式](#sql-database-advisor)，然後按一下 [查詢] 來開啟[查詢效能深入解析](#query-performance-insight)。
   
    ![檢視效能](./media/sql-database-performance/entries.png)

## 效能概觀
按一下 [概觀] 或 [效能] 圖格，將帶您前往資料庫的效能儀表板。此檢視會提供您的資料庫效能的摘要，並可協助您進行效能調整和疑難排解。

![效能](./media/sql-database-performance/performance.png)

* [建議] 圖格會提供適用於資料庫的微調建議分解 (如果有多個建議，則會顯示前 3 個)。按一下此圖格，即會帶您前往 **SQL Database 建議程式**。
* [微調活動] 圖格會提供資料庫進行中和已完成微調動作的摘要，讓您快速檢視調整活動的歷程記錄。按一下此圖格會帶您前往您的資料庫的完整微調記錄檢視。
* [自動調整] 圖格會顯示資料庫的自動調整設定 (設定了哪些微調動作要自動套用至您的資料庫)。按一下此圖格會開啟 [自動化組態] 對話方塊。
* [資料庫查詢] 圖格會顯示資料庫的查詢效能摘要 (整體 DTU 使用量和排名最前面的資源取用查詢)。按一下此圖格，即會帶您前往**查詢效能深入解析**。

## SQL Database 建議程式
[SQL Database 建議程式](sql-database-advisor.md)提供智慧型微調建議，可協助改善您的資料庫效能。

* 建議要建立或卸除的索引 (以及在沒有任何使用者互動下自動套用索引建議並自動回復對效能產生負面影響的建議的選項)。
* 在資料庫中發現結構描述問題時的建議。
* 當查詢可以受益於參數化查詢時的建議。

## 查詢效能深入解析
[查詢效能深入解析](sql-database-query-performance.md)提供了下列各項，讓您得以花費較少的時間來針對資料庫效能進行疑難排解：

* 更深入的資料庫資源 (DTU) 取用分析。
* 排名最前面的 CPU 取用查詢，進行微調有可能會改善效能。
* 向下鑽研查詢詳細資料的功能。

## 其他資源
* [單一資料庫的 Azure SQL Database 效能指引](sql-database-performance-guidance.md)
* [何時使用彈性資料庫集區？](sql-database-elastic-pool-guidance.md)

<!---HONumber=AcomDC_0720_2016-->