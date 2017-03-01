---
title: "使用 Azure 入口網站為 Azure SQL Database 設定異地複寫 | Microsoft Docs"
description: "使用 Azure 入口網站為 Azure SQL Database 設定異地複寫"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: fe2d2ef731fb94c7e4e8da0e518bcef8c1ada650
ms.lasthandoff: 02/16/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>使用 Azure 入口網站為 Azure SQL Database 設定作用中異地複寫

本文說明如何使用 [Azure 入口網站](http://portal.azure.com)為 SQL Database 設定「主動式異地複寫」。

若要使用 Azure 入口網站起始容錯移轉，請參閱 [使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-portal.md)。

> [!NOTE]
> 主動式異地複寫 (可讀取次要複本) 現在可供所有服務層中的所有資料庫使用。 在 2017 年 4 月，不可讀取的次要類型將被淘汰，而現有的不可讀取資料庫將自動升級為可讀取的次要資料庫。
> 
> 

若要使用 Azure 入口網站來設定作用中異地複寫，您需要下列資源：

* Azure SQL Database：您想要複寫到不同地理區域的主要資料庫。

> [!Note]
主動式異地複寫必須是在相同訂用帳戶內的資料庫之間進行。

## <a name="add-a-secondary-database"></a>新增次要資料庫
下列步驟會在異地複寫合作關係中建立新的次要資料庫。  

若要新增次要資料庫，您必須是訂用帳戶擁有者或共同擁有者。

次要資料庫的名稱會與主要資料庫相同，並且預設會具有相同的服務層級。 次要資料庫可以是單一資料庫或彈性集區中的資料庫。 如需詳細資訊，請參閱 [服務層](sql-database-service-tiers.md)。
建立並植入次要複本之後，就會開始從主要資料庫將資料複寫到新的次要資料庫。

> [!NOTE]
> 如果夥伴資料庫已經存在 (例如，因終止先前的「異地複寫」關聯性所導致)，命令將會失敗。
> 
> 

### <a name="add-secondary"></a>加入次要
1. 在 [Azure 入口網站](http://portal.azure.com)中，瀏覽至您想要為「異地複寫」設定的資料庫。
2. 在 SQL Database 頁面上，選取 [異地複寫]，然後選取要建立次要資料庫的區域。 您可以選取裝載主要資料庫之區域以外的任何區域，但我們建議您選取[配對區域](../best-practices-availability-paired-regions.md)。
   
    ![設定異地複寫](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. 選取或設定伺服器及次要資料庫的定價層。
   
    ![設定次要資料庫](./media/sql-database-geo-replication-portal/create-secondary.png)
4. (選擇性) 您可以將次要資料庫新增至彈性集區。 若要在集區中建立次要資料庫，請按一下 [彈性集區]，然後選取目標伺服器上的集區。 集區必須已存在目標伺服器上。 此工作流程不會建立集區。
5. 按一下 [建立]  以加入次要資料庫。
6. 將會建立次要資料庫並開始植入程序。
   
    ![設定次要資料庫](./media/sql-database-geo-replication-portal/seeding0.png)
7. 當植入程序完成時，次要資料庫會顯示其狀態。
   
    ![植入完成](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="remove-secondary-database"></a>移除次要資料庫
此作業會永久終止對次要資料庫的複寫，並將次要資料庫的角色變更為一般讀寫資料庫。 如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成讀寫資料庫。  

1. 在 [Azure 入口網站](http://portal.azure.com) 中，瀏覽至「異地複寫」合作關係中的主要資料庫。
2. 在 SQL Database 頁面上，選取 [異地複寫]。
3. 在 [次要] 清單中，選取您想要從「異地複寫」合作關係中移除的資料庫。
4. 按一下 [ **停止複寫**]。
   
    ![移除次要](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. 隨即開啟確認視窗。 按一下 [是] 以從異地複寫合作關係中移除資料庫。 (將它設定為讀寫資料庫不屬於任何複寫的一部分。)

## <a name="next-steps"></a>後續步驟
* 若要深入了解作用中異地複寫，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)。
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)。


