---
title: "Azure SQL Database 會以隔離和高效率方式建置多租用戶應用程式"
description: "了解 Azure SQL Database 如何建置多租用戶的應用程式"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 8ebd5499-ca03-4e4e-8755-4cb34339285c
ms.service: sql-database
ms.custom: benefits
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 10/13/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 187954f3ddafdbc17e341ce41f5b109cb95f8a24
ms.openlocfilehash: fb62e82865ac39031f86daa329f93261ec4ffd7b


---
# <a name="build-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>使用 Azure SQL Database 以隔離和高效率方式建置多租用戶應用程式
## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>利用彈性集區及建置更有效率的多租用戶應用程式
如果您是負責撰寫多租用戶應用程式並處理多位客戶的 SaaS 開發人員，通常會面臨在客戶效能、管理與安全性方面的取捨。 有了 Azure SQL Database 彈性集區，您就不再需要那樣妥協。 這些集區可協助您管理及監視多租用戶應用程式，並獲得一位客戶一個資料庫的隔離優點。 請參閱 [多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

![build-multi-tenant-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>
>

## <a name="auto-scaling-you-control"></a>自動調整控制
集區會即時自動調整彈性資料庫的效能和儲存體容量。 您可以控制指派給集區的效能、依照需求新增或移除彈性資料庫，以及定義彈性資料庫的效能，但不會影響集區的整體成本。 這表示您不需要擔心個別資料庫的使用管理。

[閱讀文件](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>您的環境的智慧型管理
內建大小建議會主動識別因集區而獲益的資料庫。 這些建議可讓「假設」分析快速最佳化，以符合您的效能目標。 豐富的效能監視和疑難排解儀表板可協助您視覺化過去的集區使用率。

[閱讀文件](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>符合您的需求的效能和價格
基本、標準和進階集區會提供廣泛的效能、儲存體和價格選項。 集區最多可以包含 400 個彈性資料庫。 彈性資料庫可以自動相應增加至 1000 個彈性資料庫交易單位 (eDTU)。

[閱讀文件](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>彈性工具
除了彈性集區，還有 SQL Database 功能可協助管理跨多個資料庫的作業活動︰

**執行跨資料庫的查詢和報告**  
[彈性資料庫查詢](sql-database-elastic-query-overview.md) 可讓您在彈性集區中執行跨資料庫的查詢或報告，以及一次存取多個資料庫中儲存的遠端資料。

**執行跨資料庫的交易**  
[彈性資料庫交易](sql-database-elastic-transactions-overview.md) 可讓您在 SQL Database 中執行跨多個資料庫的交易以及執行作業 (也就是在處理跨資料庫的財務交易時，或在更新一個資料庫中的庫存及訂單時)。

**在幾個資料庫上執行相同作業**  
[彈性資料庫作業](sql-database-elastic-jobs-overview.md) 會執行系統管理作業，例如重建索引或更新彈性集區中跨越每個資料庫的結構描述。

請移至首頁，查看 SQL Database 還提供哪些項目。
[了解相關資訊](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>後續步驟
取得[免費 Azure 訂用帳戶](https://azure.microsoft.com/get-started/)與[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

## <a name="additional-resources"></a>其他資源
瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)。

檢閱 [SQL Database 的技術概觀](sql-database-technical-overview.md)。  




<!--HONumber=Jan17_HO2-->


