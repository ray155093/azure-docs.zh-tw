---
title: "SQL Database - 自動調整 | Microsoft Docs"
description: "SQL Database 會分析 SQL 查詢，並自動調整使用者的工作負載。"
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: jovanpop
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f5552793db2d89542782b7d9e8f996314f62e429
ms.contentlocale: zh-tw
ms.lasthandoff: 06/17/2017


---
# <a name="automatic-tuning"></a>自動微調

Azure SQL Database 是完全受管理的資料服務，可監視在資料庫上執行的查詢，並可自動提升工作負載效能。 Azure SQL Database 具備的內建智慧機制可自動調整，並可透過動態調整工作負載的資料庫來提升查詢效能。 您能在 Azure SQL Database 上啟用以最佳化查詢效能的最重要功能之一，可能是 Azure SQL Database 中的自動調整功能。

如需使用 Azure 入口網站以[啟用自動調整](sql-database-automatic-tuning-enable.md)的步驟，請參閱本文章。

## <a name="why-automatic-tuning"></a>為何需要自動調整？

傳統資料庫系統管理的主要工作之一，就是監視工作負載、識別應新增以提升效能的重要 SQL 查詢、索引，以及識別很少使用的索引。 Azure SQL Database 會提供您需要監視之查詢和索引的詳細解析。 不過，持續監視資料庫是冗長乏味的艱辛工作，尤其是在處理許多資料庫時。 即使使用 Azure SQL Database 和 Azure 入口網站提供的所有可用工具和報告，都可能無法有效率地管理大量資料庫。 您可以考慮使用自動調整功能將一些監視和微調動作委派給 Azure SQL Database，而不是監視和手動調整您的資料庫。 


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="how-does-automatic-tuning-work"></a>自動調整的運作方式為何？

Azure SQL Database 會持續不斷地監視和分析效能，藉以了解您的工作負載特性，並找出潛在的問題和改進方式。

![自動調整程序](./media/sql-database-automatic-tuning/tuning-process.png)

此程序可讓 Azure SQL Database 藉由找出可能改善工作負載效能的索引和計劃，以及找出會影響工作負載的索引，動態調整您的工作負載。 根據這些調查結果，自動調整會套用可改善工作負載效能的調整動作。 此外，Azure SQL Database 會在自動調整做出任何變更之後持續監視效能，確定所做的該變更可改善工作負載的效能。 未能改善效能的任何動作都會自動還原。 此驗證程序是一項重要功能，可確保自動調整所做的任何變更不會降低工作負載的效能。

Azure SQL Database 中有兩個可用的自動調整層面：

 -  **自動索引管理**：識別您的資料庫中應該新增的索引，以及應該移除的索引。
 -  **自動計劃更正** (即將推出，已可用於 SQL Server 2017)：識別有問題的計劃並修正 SQL 計劃效能問題。

## <a name="automatic-index-management"></a>自動索引管理

在 Azure SQL 資料庫中，因為 Azure SQL Database 會了解您的工作負載，並確保永遠以最佳方式為資料編製索引，所以索引管理很簡單。 您的工作負載要達到最佳效能，有適當的索引設計非常重要，而且自動索引管理可以協助您將索引最佳化。 自動索引管理可以修正資料庫中索引編製不正確所造成的效能問題，或維護和改善現有資料庫結構描述的索引。 

### <a name="why-do-you-need-index-management"></a>為什麼需要索引管理？

索引可加速某些從資料表讀取資料的查詢，但會使更新資料的查詢變慢。 您需要仔細分析建立索引的時機，以及要將哪些資料行包含在索引中。 某些索引在一段時間後可能不再需要。 因此，您必須定期找出已無用處的索引，並加以卸除。 如果您對未使用的索引置之不理，會使更新資料的查詢效能降低，對讀取資料的查詢也毫無益處。 未使用的索引也會影響整體系統效能，因為其他的更新需要不必要的記錄。

找出一組最佳的索引，能改善查詢從資料表讀取資料時的效能，並對需要持續進行複雜分析的更新之影響降到最低。

Azure SQL Database 會使用內建智慧和進階規則分析您的查詢，識別出對目前工作負載而言最佳的索引以及可能要移除的索引。 Azure SQL Database 可確保最佳化讀取資料的查詢所需的索引保持在最少，對其他查詢的影響降到最低。

### <a name="how-to-identify-indexes-that-need-to-be-changed-in-your-database"></a>如何識別資料庫中需要變更的索引？

Azure SQL Database 讓索引管理程序變得容易。 Azure SQL Database 不是手動工作負載分析和索引監視的冗長程序，而是分析工作負載、找出使用新索引會更快速的查詢，以及找出未使用或重複的索引。 如需找出應變更的索引詳細資訊，可在 [Azure 入口網站中的尋找索引建議](sql-database-advisor-portal.md)中找到。

### <a name="automatic-index-management-considerations"></a>自動索引管理考量

如果您發現內建規則可改善資料庫效能，您可以讓 Azure SQL Database 自動管理索引。

在 Azure SQL Database 中建立必要索引所需的動作可能會耗用資源，並暫時影響工作負載效能。 若要讓建立索引對工作負載效能的影響降到最低，Azure SQL Database，可為任何索引管理作業找到適當的時間範圍進行。 如果資料庫需要的資源來執行工作負載，就會將調整動作延後，並在資料庫有足夠的資源可用於維護工作時啟動。 自動索引管理的其中一項重要功能是驗證動作成效。 當 Azure SQL Database 建立或卸除索引時，監視程序會分析您的工作負載以確認該動作可以改善效能。 如果沒有顯著改善 - 就會立即還原動作。 如此一來，Azure SQL Database 可確保自動採取的動作不會影響工作負載的效能。 基礎結構描述的維護作業清楚了解自動調整所建立的索引。 自動建立索引的存在並不會封鎖卸除或重新命名資料行這類的結構描述變更。 Azure SQL Database 自動建立的索引會在與之相關的資料表或資料行遭到卸除時立即卸除。

## <a name="automatic-plan-choice-correction"></a>自動計劃選擇更正

自動計劃更正為 SQL Server 2017 CTP2.0 中新增的自動調整功能，可識別行為異常的 SQL 計劃。 這個自動調整選項即將在 Azure SQL Database 推出。 如需詳細資訊，可在[自動調整 SQL Server 2017](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) 中找到。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整，並讓自動調整功能完全管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要使用手動調整，您可以檢閱 [Azure 入口網站中的調整建議](sql-database-advisor-portal.md)並手動套用可改善查詢效能的建議。

