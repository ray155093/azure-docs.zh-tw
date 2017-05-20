---
title: "對所有租用戶執行臨機操作分析查詢 (使用 Azure SQL Database 的範例 SaaS 應用程式) | Microsoft Docs"
description: "對所有租用戶執行臨機操作分析查詢"
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
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c00c25116002c46fc94c5ea59acc101a2055b689
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>對所有 WTP 租用戶執行臨機操作分析查詢

在本教學課程中，您可以建立臨機操作分析資料庫並對所有租用戶執行數個查詢。 這些查詢可以擷取藏在 WTP 應用程式日常操作資料中的深入解析。

若要執行臨機操作分析查詢 (跨越多個租用戶)，WTP 應用程式會使用[彈性查詢](sql-database-elastic-query-overview.md)以及分析資料庫。


在本教學課程中，您了解如何：

> [!div class="checklist"]

> * 部署臨機操作分析資料庫
> * 對所有租用戶資料庫執行分散式查詢



若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 WTP 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 WTP SaaS 應用程式](sql-database-saas-tutorial.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>臨機操作分析模式

SaaS 應用程式的其中一個絕佳機會是使用您集中儲存在雲端的大量租用戶資料。 使用此資料來深入了解應用程式、租用戶、其使用者的作業與使用方式，以及其喜好設定和行為。 您找到的深入解析可以引導應用程式和服務中的功能開發、使用性改進及其他投資。

在單一多租用戶資料庫中存取此資料很容易，但資料大規模分散於可能數千個資料庫時則不太容易存取。 其中一個方法是使用彈性查詢，其可對一組具有共用結構描述的分散式資料庫啟用臨機操作查詢。 彈性查詢會使用單一 head 資料庫，其中定義的外部資料表會鏡射分散式 (租用戶) 資料庫中的資料表。 提交至此 head 資料庫的查詢會經過編譯，以產生分散式查詢計劃 (包含視需要往下推送到租用戶資料庫的查詢部分)。 彈性查詢會使用目錄資料庫中的分區對應，提供租用戶資料庫的位置。 安裝程式和查詢會直接使用一般 T-SQL，以及支援從 Power BI 和 Excel 等工具進行臨機操作查詢。

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 指令碼檔案位於 [Learning Modules 資料夾](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)中。 請將 **Learning Modules** 資料夾下載到您的本機電腦，並維持其資料夾結構。

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>部署用於臨機操作分析查詢的資料庫

這個練習會部署臨機操作分析資料庫，其包含用於發出跨越所有租用戶資料庫之臨機操作查詢的結構描述。

1. 在 PowerShell ISE 中開啟 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\Demo-AdhocAnalytics.ps1 並設定下列值：
   * **$DemoScenario** = 2，**部署臨機操作分析資料庫**。

1. 按 **F5** 來執行指令碼和建立新的 SQL 資料庫以供臨機操作分析，並將它新增至 WTP 目錄。 TicketPurchases、Tickets 和 Venues 資料表會新增為可以查詢的外部資料表。
   如果您在此遇到有關「RPC 伺服器無法使用」的警告，並沒有關係。


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

[Log Analytics (OMS) 教學課程](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>其他資源

* [以初始 Wingtip Tickets Platform (WTP) 應用程式部署為基礎的其他教學課程](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [彈性查詢](sql-database-elastic-query-overview.md)

