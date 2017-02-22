---
title: "使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉 | Microsoft Docs"
description: "使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a9d184a4-09e0-4f41-b364-40425f68f430
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: b0180a9f32e1176667fe8e33a4151b2b70956adc


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉

本文說明如何使用 [Azure 入口網站](http://portal.azure.com)起始容錯移轉至次要 SQL Database。 若要設定「異地複寫」，請參閱 [為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-portal.md)。

## <a name="initiate-a-failover"></a>起始容錯移轉
次要資料庫可被切換成為主要資料庫。  

1. 在 [Azure 入口網站](http://portal.azure.com) 中，瀏覽至「異地複寫」合作關係中的主要資料庫。
2. 在 [SQL Database] 刀鋒視窗上，選取 [所有設定]  >  [異地複寫]。
3. 在 [次要] 清單中，選取要做為新主要資料庫的資料庫，然後按一下 [容錯移轉]。
   
    ![容錯移轉][2]
4. 按一下 [是]  即可開始容錯移轉。

命令會立即會將次要資料庫切換為主要角色。 

切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。 如果主要資料庫有多個次要資料庫，此命令會自動重新設定其他次要複本以連接至新的主要複本。 在正常情況下，完成整個作業所需的時間應該少於一分鐘。 

> [!NOTE]
> 發出命令時，如果主要複本處於線上並且正在認可交易，可能會發生部分資料遺失。
> 
> 

## <a name="next-steps"></a>後續步驟
* 容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。
* 若要了解如何使用主動式異地複寫在災害之後進行復原，包括復原前和復原後步驟，以及執行災害復原演練，請參閱 [災害復原演練](sql-database-disaster-recovery.md)
* 如需 Sasha Nosov 有關主動式異地複寫的部落格文章，請參閱 [新異地複寫功能要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* 如需如何設計雲端應用程式使用主動式異地複寫的相關資訊，請參閱 [使用異地複寫設計商務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* 如需使用主動式異地複寫與彈性集區的相關資訊，請參閱[彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
* 如需商務持續性的概觀，請參閱 [商務持續性概觀](sql-database-business-continuity.md)

<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png



<!--HONumber=Feb17_HO3-->


