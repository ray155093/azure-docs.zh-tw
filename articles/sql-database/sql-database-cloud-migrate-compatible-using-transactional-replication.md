<properties
   pageTitle="使用異動複寫移轉到 SQL Database | Microsoft Azure"
   description="Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 異動複寫"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# 使用異動複寫將 SQL Server 資料庫移轉到 Azure SQL Database

> [AZURE.SELECTOR]
- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [異動複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

在這篇文章中，您將了解如何使用 SQL Server 異動複寫，在最短的停機時間內，將相容的 SQL Server 資料庫移轉到 Azure SQL Database。

## 了解異動複寫架構

當您在移轉發生時無法負擔從實際執行中移除 SQL Server 資料庫時，可以使用 SQL Server 異動複寫做為移轉解決方案。若要使用此解決方案，您需將 Azure SQL Database 設定為您想要移轉之內部部署 SQL Server 執行個體的訂閱者。內部部署異動複寫散發者會在新交易繼續進行的同時，從要被同步處理的內部部署資料庫 (發行者) 同步處理資料。

您也可以使用異動複寫以移轉內部部署資料庫的子集。您複寫至 Azure SQL Database 的發佈可以限制為複寫的資料庫中資料表的子集。針對要複寫的每一個資料表，您可以將資料限制在資料列的子集和 (或) 資料行的子集。

使用異動複寫時，對您資料或結構描述所做的一切變更都會顯示在 Azure SQL Database 中。同步處理完成且您已準備好進行移轉之後，請將您應用程式的連接字串變更成指向您的 Azure SQL Database。當異動複寫清空留在內部部署資料庫上的所有變更，並且您的所有應用程式都指向 Azure DB 之後，您便可以將異動複寫解除安裝。您的 Azure SQL Database 現在已是您的生產環境系統。

 ![SeedCloudTR 圖表](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## 異動複寫需求

異動複寫是內建技術，與 SQL Server 6.5 之後的 SQL Server 整合。它是一項成熟且經過實證的技術，大多數 DBA 都知道這項技術且有相關的經驗。有了 [SQL Server 2016](https://www.microsoft.com/zh-TW/cloud-platform/sql-server)，現在便能夠將 Azure SQL Database 設定為內部部署發佈的[異動複寫訂閱者](https://msdn.microsoft.com/library/mt589530.aspx)。您從 Management Studio 設定它的體驗，與在內部部署伺服器上設定異動複寫訂閱者相同。當發行者和散發者至少是下列其中一個 SQL Server 版本時，即支援對此案例的支援：

 - SQL Server 2016 或以上版本
 - SQL Server 2014 SP1 CU3 和更新版本
 - SQL Server 2014 RTM CU10 和更新版本
 - SQL Server 2012 SP2 CU8 和更新版本
 - SQL Server 2012 SP3 和更新版本


> [AZURE.IMPORTANT] 請使用最新版的 SQL Server Management Studio 以便與 Microsoft Azure 及 SQL Database 更新保持同步。舊版 SQL Server Management Studio 無法將 SQL Database 設定為訂閱者。[更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。


## 後續步驟

- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/zh-TW/cloud-platform/sql-server)

## 其他資源

- [異動複寫](https://msdn.microsoft.com/library/mt589530.aspx)
- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
- [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->