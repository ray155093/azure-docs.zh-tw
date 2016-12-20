---
title: "變更 Azure SQL Database 的服務層級和效能等級 | Microsoft Docs"
description: "變更 Azure SQL Database 的服務層級和效能等級說明如何相應增加或減少您的 SQL Database。 變更 Azure SQL Database 的定價層。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: cbd67e88-08d5-40e2-a223-0fb0c718a782
ms.service: sql-database
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 1e297de81be07a107691c54875ca638ae73d43df
ms.openlocfilehash: 3f283166f09437611aa04d7cadf5b161d0e52f8a


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>使用 Azure 入口網站變更 SQL Database 的服務層級和效能等級 (定價層)
> [!div class="op_single_selector"]
> * [**Azure 入口網站**](sql-database-scale-up.md)
> * [PowerShell](sql-database-scale-up-powershell.md)
> 
> 

服務層和效能層級描述您的 SQL Database 可用的功能和資源，而且可以視您的應用程式變更進行更新。 如需詳細資訊，請參閱 [服務層](sql-database-service-tiers.md)。

請注意，變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 這個時間範圍會有不同，但平均在 4 秒下，而且超過 99% 的案例少於 30 秒。 很少發生，尤其是如果在連接停用時有大型交易執行中，則此時間範圍可能會更長。  

在大部分情況下，整個相應增加的程序少於 5 分鐘。 

使用 [Azure SQL Database 服務層和效能等級](sql-database-service-tiers.md)中的資訊，來判斷適合您 Azure SQL Database 的服務層和效能等級。

* 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。 
* 在啟用 [異地複寫](sql-database-geo-replication-overview.md) 的情況下升級資料庫時，您必須先將其次要資料庫升級至所需的效能層，然後再升級主要資料庫。
* 將服務層降級時，您必須先終止所有「異地複寫」關聯性。 
* 還原服務會針對各種服務層提供不同的選項。 降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。 如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](sql-database-business-continuity.md)。
* 變更資料庫定價層不會變更資料庫大小上限。 若要變更資料庫大小上限，請使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 或 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)。
* 完成變更之前，不會將新屬性套用至資料庫。

**若要完成本文，您需要下列項目：**

* Azure SQL Database。 如果您沒有 SQL Database，請遵循此文章中的步驟來建立： [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>變更您資料庫的服務層級和效能等級
開啟您想要相應增加或減少之資料庫的 [SQL Database] 刀鋒視窗：

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [更多服務]  >  [SQL Database]。
2. 按一下您想要變更的資料庫。
3. 接著，在 [SQL Database] 刀鋒視窗中，按一下 [定價層 (級別 DTU)]：
   
   ![定價層][1]
4. 選取新的階層，然後按一下 [選取]：
   
   按一下 [選取] 提交調整要求以變更定價層。 視資料庫大小而定，調整作業需要一些時間才能完成 (請參閱本文一開始的資訊)。
   
   > [!NOTE]
   > 變更資料庫定價層不會變更資料庫大小上限。 若要變更資料庫大小上限，請使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 或 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)。
   > 
   > 
   
   ![選取定價層][2]
5. 按一下右上角的通知圖示 (鐘)︰
   
   ![通知][3]
   
   按一下通知文字以開啟詳細資料窗格，您可以在其中看到要求的狀態。

## <a name="next-steps"></a>後續步驟
* 使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 或 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx) 變更資料庫大小上限。
* [相應放大和縮小](sql-database-elastic-scale-get-started.md)
* [使用 SSMS 連接和查詢 SQL Database](sql-database-connect-query-ssms.md)
* [匯出 Azure SQL Database](sql-database-export.md)

## <a name="additional-resources"></a>其他資源
* [業務續航力概觀](sql-database-business-continuity.md)
* [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png



<!--HONumber=Nov16_HO3-->


