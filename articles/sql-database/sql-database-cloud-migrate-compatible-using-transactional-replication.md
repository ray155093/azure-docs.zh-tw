---
title: "使用異動複寫移轉到 SQL Database | Microsoft Docs"
description: "Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 異動複寫"
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>使用異動複寫將 SQL Server 資料庫移轉到 Azure SQL Database
> [!div class="op_single_selector"]
> * [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [異動複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

在這篇文章中，您將了解如何使用 SQL Server 異動複寫，在最短的停機時間內，將相容的 SQL Server 資料庫移轉到 Azure SQL Database。

## <a name="understanding-the-transactional-replication-architecture"></a>了解異動複寫架構
當您在移轉發生時無法負擔從實際執行中移除 SQL Server 資料庫時，可以使用 SQL Server 異動複寫做為移轉解決方案。 若要使用此解決方案，您需將 Azure SQL Database 設定為您想要移轉之內部部署 SQL Server 執行個體的訂閱者。 內部部署異動複寫散發者會在新交易繼續進行的同時，從要被同步處理的內部部署資料庫 (發行者) 同步處理資料。 

您也可以使用異動複寫以移轉內部部署資料庫的子集。 您複寫至 Azure SQL Database 的發佈可以限制為複寫的資料庫中資料表的子集。 針對要複寫的每一個資料表，您可以將資料限制在資料列的子集和 (或) 資料行的子集。

使用異動複寫時，對您資料或結構描述所做的一切變更都會顯示在 Azure SQL Database 中。 同步處理完成且您已準備好進行移轉之後，請將您應用程式的連接字串變更成指向您的 Azure SQL Database。 當異動複寫清空留在內部部署資料庫上的所有變更，並且您的所有應用程式都指向 Azure DB 之後，您便可以將異動複寫解除安裝。 您的 Azure SQL Database 現在已是您的生產環境系統。

 ![SeedCloudTR 圖表](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="how-transactional-replication-works"></a>異動複寫的運作方式

異動複寫包含 3 個主要元件。 它們是「發佈者」、「散發者」和「訂閱者」。 這些元件一起進行複寫。 「散發者」負責控制在伺服器之間移動資料的程序。 當您設定散發 SQL 將會建立散發資料庫。 每個發佈者需要繫結至散發資料庫。 散發資料庫會在每個複寫進行中保存每個相關聯發佈和資料的中繼資料。 對於異動複寫，它會保留需要在「訂閱者」中執行的所有異動。

發佈者是移轉的所有資料的來源資料庫。 在「發佈者」中可以有許多的發佈。 這些發佈包含對應至需要複寫的所有資料表和資料的發佈。 根據您對於發佈和文章的定義，您可以複寫所有或部分資料庫。 

在複寫中訂閱者是從發佈接收所有資料和異動的伺服器。 每個發佈可以有許多複寫。

## <a name="transactional-replication-requirements"></a>異動複寫需求
[請移至這個連結，取得更新的需求清單。](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> 請使用最新版的 SQL Server Management Studio 以便與 Microsoft Azure 及 SQL Database 更新保持同步。 舊版 SQL Server Management Studio 無法將 SQL Database 設定為訂閱者。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>使用異動複寫工作流程移轉到 SQL Database

1. 設定散發套件
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. 建立發佈
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. 建立訂用帳戶
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>一些秘訣和移轉至 SQL Database 的差異

1. 使用本機散發者 
   - 這會造成伺服器上的效能影響。 
   - 如果無法接受效能影響，您可以使用另一部伺服器，但它會增加管理和系統管理的複雜度。
2. 當選取快照集資料夾時，請確定您選取的資料夾足以容納要複寫的每個資料表 BCP。 
3. 請注意，在快照集完成為止，將會鎖定相關聯的資料表，在排程快照集時請謹記在心。 
4. Azure SQL Database 僅支援推送訂用帳戶
   - 您只能從內部部署資料庫端新增「訂閱者」。

## <a name="next-steps"></a>後續步驟
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>其他資源
* 若要深入了解異動複寫，請參閱[異動複寫](https://msdn.microsoft.com/library/mt589530.aspx)。
* 若要深入了解整體移轉程序和選項，請參閱 [SQL Server 資料庫移轉至雲端 SQL Database](sql-database-cloud-migrate.md)。



<!--HONumber=Dec16_HO2-->


