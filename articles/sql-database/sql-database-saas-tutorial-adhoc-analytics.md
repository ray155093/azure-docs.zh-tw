---
title: "跨多個 Azure SQL Database 執行臨機操作分析查詢 | Microsoft Docs"
description: "在 Wingtip SaaS 多租用戶應用程式中，跨多個 SQL Database 執行臨機操作分析查詢。"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/22/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 31be50ca3f64cc183e516f1b0f06f5a4265f6103
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>對所有 Wingtip SaaS 租用戶執行臨機操作分析查詢

在本教學課程中，您可以建立臨機操作分析資料庫並對所有租用戶執行數個查詢。 這些查詢可以擷取藏在 WTP 應用程式日常操作資料中的深入解析。

若要執行臨機操作分析查詢 (跨越多個租用戶)，Wingtip SaaS 應用程式會使用[彈性查詢](sql-database-elastic-query-overview.md)以及分析資料庫。


在本教學課程中，您了解如何：

> [!div class="checklist"]

> * 部署臨機操作分析資料庫
> * 對所有租用戶資料庫執行分散式查詢



若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>臨機操作分析模式

SaaS 應用程式的其中一個絕佳機會是使用集中儲存在雲端的大量租用戶資料。 使用此資料來深入了解應用程式的作業與使用方式、租用戶、其使用者，其喜好設定、行為等。這些深入解析可以引導應用程式和服務中的功能開發、使用性改進及其他投資。

在單一多租用戶資料庫中存取此資料很容易，但資料大規模分散於可能數千個資料庫時則不太容易存取。 其中一個方法是使用彈性查詢，其可對一組具有共用結構描述的分散式資料庫啟用臨機操作查詢。 彈性查詢會使用單一 head 資料庫，其中定義的外部資料表會鏡射分散式 (租用戶) 資料庫中的資料表或檢視。 提交至此 head 資料庫的查詢會經過編譯，以產生分散式查詢計劃 (包含視需要往下推送到租用戶資料庫的查詢部分)。 彈性查詢會使用目錄資料庫中的分區對應，提供租用戶資料庫的位置。 安裝程式和查詢會直接使用標準 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)，以及支援從 Power BI 和 Excel 等工具進行臨機操作查詢。

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 [用於下載 Wingtip SaaS 指令碼的步驟](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts)。


## <a name="explore-the-global-views-in-the-tenant-databases"></a>探索租用戶資料庫中的全域檢視

Wingtip SaaS 應用程式是使用 tenant-per-database 模型建置，因此會從單一租用戶的觀點定義租用戶資料庫結構描述。 租用戶特定資訊存在於一個資料表 Venue，該資料表一律有單一資料列，此外會設計成一個沒有主索引鍵的堆積。  結構描述中的其他資料表不必與 Venue 資料表相關，因為正常使用時，資料屬於哪個租用戶絕對毫無疑慮。  不過，查詢所有資料庫時，讓資料庫中的資料表資料與特定租用戶相互關聯就變得非常重要。 為了簡化此作業，會將一組檢視新增至租用戶資料庫，以提供每個租用戶的「全域」檢視。 這些全域檢視會將租用戶識別碼投射至要全域查詢的每個資料表中。 這可讓您輕鬆地識別每個租用戶的資料。 為了方便起見，這些檢視表已預先建立於所有的租用戶資料庫中 (以及範本 DB，以便在佈建新租用戶時使用這些檢視)。

1. 開啟 SSMS 並[連線到 tenants1-&lt;USER&gt; 伺服器](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
1. 展開 [資料庫]，以滑鼠右鍵按一下 **contosoconcerthall**，然後選取 [新增查詢]。
1. 執行下列查詢來探索全域檢視︰

   ```T-SQL
   -- This is the base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   -- In the sample database we calculated an integer id from a hash of the Venue name,
   -- but any approach could be used to introduce a unique value.
   -- This is similar to how we create the tenant key in the catalog,
   -- but there is no requirement that the catalog key and this id be the same.
   SELECT * FROM Venues

   -- The base Events table which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

檢查檢視並了解其建立方式︰

1. 在 [物件總管] 中，展開 [contosoconcethall] > [檢視]：

   ![檢視](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

1. 以滑鼠右鍵按一下 **dbo.Venues**。
1. 選取 [指令碼檢視] > [CREATE To] > [新的查詢編輯器視窗]****

對任何 [檢視] 執行此動作來檢查其建立方式。

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>部署用於臨機操作分析查詢的資料庫

此練習會部署 adhocanalytics 資料庫。 此資料庫包含用於查詢所有租用戶資料庫的結構描述。 資料庫會部署到現有的目錄伺服器，也就是包含所有管理相關資料庫的伺服器。

1. 在 PowerShell ISE 中開啟 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\Demo-AdhocAnalytics.ps1 並設定下列值：
   * **$DemoScenario** = 2，**部署臨機操作分析資料庫**。

1. 在指令碼中向下捲動至包含資料庫結構描述的 SQL 指令碼。  檢閱指令碼並注意下列事項︰

   1. 彈性查詢會使用資料庫範圍的認證來存取每個租用戶資料庫。 此認證必須可用於所有資料庫，且通常應獲得啟用這些臨機操作查詢所需的最小權限。
   1. 外部資料來源，其已定義為在目錄資料庫中使用租用戶分區對應。  以此作為外部資料來源，在發出查詢時，查詢將會分散到在目錄中註冊的所有資料庫。
   1. 參考前一節所述之全域檢視的外部資料表。
   1. 已建立和填入的本機資料表 *VenueTypes*。  因為此參考資料表共用於所有租用戶資料庫中，所以可在此以本機資料表表示，這對有些查詢而言，可能會降低在租用戶資料庫與 adhocanalytics 資料庫之間移動的資料量。

1. 按 **F5** 以執行指令碼並建立 adhocanalytics 資料庫。

   忽略有關「RPC 伺服器無法使用」的警告，並沒有關係。


您現在具有 *adhocanalytics* 資料庫，其可用來執行分散式的查詢，以及蒐集所有租用戶的深入解析！

## <a name="run-ad-hoc-analytics-queries"></a>執行臨機操作分析查詢

這個練習會執行臨機操作分析查詢，以從 WTP 應用程式發掘租用戶深入解析。

1. 在 SSMS 中開啟 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\Demo-AdhocAnalyticsQueries.sql。
1. 確定您已連線到 **adhocanalytics** 資料庫
1. 選取您要執行的個別查詢，然後按 **F5**：

    ![query](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]

> * 部署臨機操作分析資料庫
> * 對所有租用戶資料庫執行分散式查詢

現在嘗試[租用戶分析教學課程](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>其他資源

* [以初始 Wingtip SaaS 應用程式部署為基礎的其他教學課程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [彈性查詢](sql-database-elastic-query-overview.md)

