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
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# 使用異動複寫將 SQL Server 資料庫移轉到 Azure SQL Database

> [AZURE.SELECTOR]
- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [異動複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

在這篇文章中，您將了解如何使用 SQL Server 異動複寫，在最短的停機時間內，將相容的 SQL Server 資料庫移轉到 Azure SQL Database。

## 了解異動複寫架構

當您在移轉發生時無法負擔從實際執行中移除 SQL Server 資料庫時，可以使用 SQL Server 交易複寫做為移轉解決方案。若要使用此解決方案，您需將 Azure SQL Database 設定為您想要移轉之內部部署 SQL Server 執行個體的訂閱者。內部部署異動複寫散發者會在新交易繼續進行的同時，從要被同步處理的內部部署資料庫 (發行者) 同步處理資料。

您也可以使用交易複寫以移轉內部部署資料庫的子集。您複寫至 Azure SQL Database 的發佈可以限制為複寫的資料庫中資料表的子集。此外，對於複寫的每一個資料表，您可以將資料限制為資料列的子集和 (或) 資料行的子集。

使用交易複寫，開始移轉和完成移轉之間發生的資料或結構描述的所有變更都會出現在您的 Azure SQL Database。

同步處理完成之後，您便已準備好進行移轉，您只需要將您應用程式的連接字串變更成指向您的 Azure SQL Database，而不是指向您的內部部署資料庫。一旦交易複寫清空留在內部部署資料庫的任何變更，且所有應用程式指向 Azure DB，您現在可以安全地解除安裝複寫，讓您的 Azure SQL Database 做為實際執行系統。

 ![SeedCloudTR 圖表](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## 異動複寫需求

交易複寫是內建技術，與 SQL Server 6.5 之後的 SQL Server 整合。它是大部分 DBA 知道他們會體驗到的非常成熟且經過實證的技術。現在，您只要使用 [SQL Server 2016](https://www.microsoft.com/zh-TW/cloud-platform/sql-server)，即可將 Azure SQL Database 設為內部部署發佈的[交易複寫訂閱者](https://msdn.microsoft.com/library/mt589530.aspx)。您從 Management Studio 設定它的經驗，與在內部部署伺服器上設定交易複寫訂閱者完全相同。當發行者和散發者至少其中之一為下列 SQL Server 版本時，即支援對此案例的支援：

 - SQL Server 2016 或以上版本
 - SQL Server 2014 SP1 CU3 和更新版本
 - SQL Server 2014 RTM CU10 和更新版本
 - SQL Server 2012 SP2 CU8 和更新版本
 - SQL Server 2013 SP3 (當它發行時)


> [AZURE.IMPORTANT] 您必須一律使用最新版本的 SQL Server Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。舊版 SQL Server Management Studio 無法將 SQL Database 設定為訂閱者。[更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。


## 後續步驟

- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/zh-TW/cloud-platform/sql-server)

## 其他資源

- [異動複寫](https://msdn.microsoft.com/library/mt589530.aspx)
- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
- [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0803_2016-->