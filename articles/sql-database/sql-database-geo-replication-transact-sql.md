<properties
    pageTitle="使用 Transact-SQL 為 Azure SQL Database 設定異地複寫 | Microsoft Azure"
    description="使用 Transact-SQL 為 Azure SQL Database 設定異地複寫"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
    ms.date="06/14/2016"
    ms.author="carlrab"/>

# 使用 Transact-SQL 為 Azure SQL Database 設定異地複寫

> [AZURE.SELECTOR]
- [概觀](sql-database-geo-replication-overview.md)
- [Azure 入口網站](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

本文說明如何使用 Transact-SQL，為 Azure SQL Database 設定作用中異地複寫。

若要使用 Transact-SQL 起始容錯移轉，請參閱[使用 Transact-SQL 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-transact-sql.md)。

>[AZURE.NOTE] 作用中異地複寫 (可讀取次要複本) 現在可供所有服務層中的所有資料庫使用。在 2017 年 4 月，不可讀取的次要類型將淘汰，而現有不可讀取的資料庫將自動升級為可讀取的次要複本。

若要使用 Transact-SQL 設定作用中異地複寫，您需要下列項目：

- Azure 訂用帳戶。
- 邏輯 Azure SQL Database 伺服器 <MyLocalServer> 和 SQL Database <MyDB> - 您想要複寫的主要資料庫。
- 一或多個邏輯 Azure SQL Database 伺服器 <MySecondaryServer(n)> - 您將在其中建立次要資料庫的夥伴伺服器的邏輯伺服器。
- 一個主要複本上的 DBManager 登入身分、具備您將進行異地複寫之本機資料庫的 db\_ownership，以及成為您將設定「異地複寫」之夥伴伺服器上的 DBManager。
- 最新版本的 SQL Server Management Studio - 若要取得最新版本的 SQL Server Management Studio (SSMS)，請移至[下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。如需使用 SQL Server Management Studio 來管理 Azure SQL Database 邏輯伺服器和資料庫的詳細資訊，請參閱[使用 SQL Server Management Studio 管理 Azure SQL Database](sql-database-manage-azure-ssms.md)

## 加入次要資料庫

您可以使用 **ALTER DATABASE** 陳述式，在夥伴伺服器上建立異地複寫的次要資料庫。您在包含要複寫的資料庫伺服器的 master 資料庫上執行此陳述式。異地複寫資料庫 (「主要資料庫」) 會具備與要複寫的資料庫相同的名稱，並且預設與主要資料庫具有相同的服務層級。次要資料庫可以是可讀取或不可讀取，並且可以是單一資料庫或彈性資料庫。如需詳細資訊，請參閱 [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 和[服務層](sql-database-service-tiers.md)。建立次要資料庫並植入之後，資料就會開始以非同步方式從主要資料庫複寫。下列步驟說明如何使用 Management Studio 來設定「異地複寫」。提供使用單一資料庫或彈性資料庫建立不可讀取和可讀取次要複本的步驟。

> [AZURE.NOTE] 如果指定的夥伴伺服器上存在與名稱與主要資料庫相同的資料庫，則命令會失敗。


### 加入不可讀取次要複本 (單一資料庫)

您可以使用下列步驟，將不可讀取次要複本建立為單一資料庫。

1. 使用版本 13.0.600.65 或 SQL Server Management Studio 的更新版本。

 	 > [AZURE.IMPORTANT] 下載[最新](https://msdn.microsoft.com/library/mt238290.aspx)版本的 SQL Server Management Studio。建議您一律使用最新版本的 Management Studio 保持與 Azure 入口網站更新同步。


2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使本機資料庫成為一個在 MySecondaryServer1 上具有不可讀取之次要資料庫的「異地複寫」主要複本 (其中 MySecondaryServer1 是您的伺服器易記名稱)。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. 按一下 [執行] 來執行查詢。


### 加入可讀取次要複本 (單一資料庫)
您可以使用下列步驟，將可讀取次要複本建立為單一資料庫。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使本機資料庫成為一個在次要伺服器上具有可讀取之次要資料庫的「異地複寫」主要複本。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. 按一下 [執行] 來執行查詢。



### 加入不可讀取次要複本 (彈性資料庫)

您可以使用下列步驟，將不可讀取次要複本建立為彈性資料庫。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使本機資料庫成為一個在彈性集區中次要伺服器上具有不可讀取之次要資料庫的「異地複寫」主要複本。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. 按一下 [執行] 來執行查詢。



### 加入可讀取次要複本 (彈性資料庫)
您可以使用下列步驟，將可讀取次要複本建立為彈性資料庫。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使本機資料庫成為一個在彈性集區中次要伺服器上具有可讀取之次要資料庫的「異地複寫」主要複本。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. 按一下 [執行] 來執行查詢。



## 移除次要資料庫

您可以使用 **ALTER DATABASE** 陳述式以永久終止次要資料庫與其主要資料庫之間的複寫關係。此陳述式是在主要資料庫所在的 master 資料庫上執行。關聯性終止之後，次要資料庫會成為一般的讀寫資料庫。如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成可讀寫。如需詳細資訊，請參閱 [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 和[服務層](sql-database-service-tiers.md)。

使用下列步驟可從「異地複寫」合作關係中移除異地複寫的次要複本。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式來移除異地複寫的次要複本。

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. 按一下 [執行] 來執行查詢。

## 監視異地複寫組態和健康狀態

監視工作包括監視「異地複寫」組態及監視資料複寫健康狀態。您可以使用 master 資料庫中的 **sys.dm\_geo\_replication\_links** 動態管理檢視，傳回 Azure SQL Database 邏輯伺服器上每個資料庫的所有現有複寫連結的相關資訊。這個檢視針對每個主要和次要資料庫之間的複寫連結包含一個資料列。您可以使用 **sys.dm\_replication\_status** 動態管理檢視，對目前參與複寫連結的每個 Azure SQL Database 傳回一個資料列。這包括主要和次要資料庫。如果給定主要資料庫有一個以上的連續複寫連結，此資料表會對每個關聯性包含一個資料列。會在所有資料庫 (包括邏輯主機) 中建立檢視。不過，在邏輯主機中查詢此檢視會傳回空集合。您可以使用 **sys.dm\_operation\_status** 動態管理檢視來顯示所有資料庫作業的狀態，包括複寫連結的狀態。如需詳細資訊，請參閱 [sys.geo\_replication\_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx)、[sys.dm\_geo\_replication\_link\_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx) 和 [sys.dm\_operation\_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx)。

使用下列步驟可監視「異地複寫」合作關係。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列陳述式可顯示具有「異地複寫」連結的所有資料庫。

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. 按一下 [執行] 來執行查詢。
5. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **MyDB** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。
6. 使用下列陳述式來顯示複寫落後和我的次要資料庫 MyDB 上次複寫的開始時間。

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. 按一下 [執行] 來執行查詢。
8. 使用下列陳述式可顯示與 MyDB 資料庫相關聯的最新異地複寫作業。

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. 按一下 [執行] 來執行查詢。



## 後續步驟

- 若要深入了解作用中異地複寫，請參閱[作用中異地複寫](sql-database-geo-replication-overview.md)
- 若要了解商務持續性設計及復原案例，請參閱[持續性案例](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0629_2016-->