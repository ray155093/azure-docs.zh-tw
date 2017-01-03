---
title: "Azure 建議程式效能建議 | Microsoft Docs"
description: "使用建議程式將 Azure 部署的效能最佳化。"
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: c350134e8cea5771900e2e88b02b572c20e9d5bd

---

# <a name="advisor-performance-recommendations"></a>建議程式效能建議

建議程式效能建議有助於強化及提升業務關鍵應用程式的速度和回應能力。 您可以從 [建議程式] 儀表板的 [效能] 索引標籤，利用建議程式取得效能建議。

![建議程式效能索引標籤](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="sql-database-recommendations"></a>SQL Database 建議

建議程式可針對所有的 Azure 資源提供一致的合併建議檢視。 它會與 SQL Database 建議程式整合，以提供改善 SQL Azure 資料庫效能的相關建議。 SQL Database 建議程式會藉由分析您的使用歷程記錄來評估 SQL Azure 資料庫的效能。 接著會提供最適合用於執行資料庫之一般工作負載的建議事項。 

> [!NOTE]
> 若要取得建議，資料庫必須持續使用一週，而且那一週之內必須有一些一致的活動。 相較於隨機蹦出的零星活動，一致的查詢模式更有利於 SQL Database 建議程式最佳化。

如需 SQL Database 建議程式的詳細資訊，請參閱 [SQL Database 建議程式](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/)。

![SQL Database 建議](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="redis-cache-recommendations"></a>Redis 快取建議

Azure 建議程式會識別高記憶體使用量、伺服器負載、網路頻寬或大量用戶端連線會對其效能造成負面影響的 Redis 快取執行個體。 它也提供最佳作法建議來避免潛在的問題。 如需 Redis 快取建議的詳細資訊，請參閱 [Redis 快取建議程式](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor)。


## <a name="app-services-recommendations"></a>應用程式服務建議

Azure 建議程式整合了最佳作法建議，以供提升應用程式服務體驗和探索相關的平台功能。 應用程式服務建議的範例如下︰
- 偵測應用程式執行階段與緩和選項已耗盡記憶體或 CPU 資源的執行個體
- 偵測共置資源 (如 Web 應用程式和資料庫) 可改善效能並降低成本的執行個體。 

如需應用程式服務建議的詳細資訊，請參閱 [Azure App Service 的最佳作法](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/)。
![應用程式服務建議](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何在建議程式中存取效能建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左導覽窗格中的 [更多服務]，在服務功能表窗格中，向下捲動至 [監視和管理]，然後按一下 [Azure 建議程式]。 這會啟動 [建議程式] 儀表板。 
3. 在 [建議程式] 儀表板中，按一下 [效能] 索引標籤，然後選取您想要接收建議的訂用帳戶。

> [!NOTE]
> 建議程式會針對您已獲得「擁有者」、「參與者」或「讀取者」角色的訂用帳戶，產生相關建議。

## <a name="next-steps"></a>後續步驟

若要深入了解建議程式建議，請參閱下列資源：

-  [建議程式簡介](advisor-overview.md)
-  [開始使用建議程式](advisor-get-started.md)
-  [建議程式高可用性建議](advisor-high-availability-recommendations.md)
-  [建議程式安全性建議](advisor-security-recommendations.md)
-  [建議程式成本建議](advisor-performance-recommendations.md)



<!--HONumber=Nov16_HO3-->


