<properties 
    pageTitle="使用 Transact-SQL 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉 | Microsoft Azure" 
    description="使用 Transact-SQL 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉" 
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
    ms.workload="data-management"
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# 使用 Transact-SQL 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


本文說明如何使用 Transact-SQL 起始容錯移轉至次要 SQL Database。若要設定「異地複寫」，請參閱[為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-transact-sql.md)。



若要起始容錯移轉，您需要下列各項︰

- 一個主要複本上的 DBManager 登入身分、具備您將進行異地複寫之本機資料庫的 db\_ownership，以及成為您將設定「異地複寫」之夥伴伺服器上的 DBManager。
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。[更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。




## 起始規劃的容錯移轉，將次要資料庫升級成為新主要複本

您可以使用 **ALTER DATABASE** 陳述式，來升級次要資料庫，使它成為規劃的方式中的新主要資料庫，將現有主要降級成為次要資料庫。此陳述式是在要升級的異地複寫次要資料庫所在的 Azure SQL Database 邏輯伺服器上的 master 資料庫上執行。這項功能是為了規劃的容錯移轉 (例如 DR 鑽研期間) 設計，並且需要主要資料庫可供使用。

此命令會執行下列工作流程：

1. 暫時切換複寫為同步模式，造成所有未完成的交易被排清至次要複本並封鎖所有新交易；

2. 切換「異地複寫」合作關係中兩個資料庫的角色。

此順序可保證在角色切換之前兩個資料庫經過同步處理，因此不會發生資料遺失。切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。如果主要資料庫有多個次要資料庫，此命令會自動重新設定其他次要複本以連接至新的主要複本。在正常情況下，完成整個作業所需的時間應該少於一分鐘。如需詳細資訊，請參閱 [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 和[服務層](sql-database-service-tiers.md)。


使用下列步驟來起始規劃的容錯移轉。

1. 在 Management Studio 中，連接到異地複寫次要資料庫所在的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，將次要資料庫切換為主要角色。

        ALTER DATABASE <MyDB> FAILOVER;

4. 按一下 [執行] 來執行查詢。

>[AZURE.NOTE] 在少數情況下，作業會無法完成並且可能會出現停滯。在此情況下，使用者可以執行強制容錯移轉命令並接受資料遺失。


## 起始從主要資料庫到次要資料庫的未規劃的容錯移轉

您可以使用 **ALTER DATABASE** 陳述式，來升級次要資料庫，使它成為未規劃的方式中的新主要資料庫，每當主要資料庫無法使用時，一次強制將現有主要降級成為次要資料庫。此陳述式是在要升級的異地複寫次要資料庫所在的 Azure SQL Database 邏輯伺服器上的 master 資料庫上執行。

這項功能是針對還原資料庫的可用性非常重要而且部分資料遺失是可接受時的災害復原所設計。叫用強制容錯移轉時，指定的次要資料庫立即成為主要資料庫，並開始接受寫入交易。原始主要資料庫能夠與這個新的主要資料庫重新連線時，會在原始主要資料庫執行增量備份，而舊的主要資料庫會變成新主要資料庫的次要資料庫；之後就只是新的主要資料庫的複本。

不過，因為次要資料庫上不支援還原時間點，發生強制容錯移轉之前，如果使用者想要復原已認可到舊主要資料庫但尚未複寫到新主要資料庫的資料，使用者應該接洽支援人員復源遺失的資料。

如果主要資料庫有多個次要資料庫，此命令會自動重新設定其他次要複本以連接至新的主要複本。

使用下列步驟來起始非計劃性的容錯移轉。

1. 在 Management Studio 中，連接到異地複寫次要資料庫所在的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，將次要資料庫切換為主要角色。

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. 按一下 [執行] 來執行查詢。

>[AZURE.NOTE] 如果在主要資料庫和次要資料庫上線時發出此命令，舊的主要會立即變成新的次要資料庫，而不會進行資料同步處理。發出命令時，如果主要複本正在認可交易，可能會發生部分資料遺失。



## 後續步驟   

- 容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。如需詳細資訊，請參閱[災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。
- 若要了解如何使用主動式異地複寫在災害之後進行復原，包括復原前和復原後步驟，以及執行災害復原演練，請參閱[災害復原](sql-database-disaster-recovery.md)
- 如需 Sasha Nosov 有關主動式異地複寫的部落格文章，請參閱[新異地複寫功能要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- 如需如何設計雲端應用程式使用主動式異地複寫的相關資訊，請參閱[使用異地複寫設計商務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- 如需使用主動式異地複寫與彈性資料庫集區的相關資訊，請參閱[彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
- 如需商務持續性的概觀，請參閱[商務持續性概觀](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0831_2016-->