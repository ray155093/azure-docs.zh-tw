<properties 
    pageTitle="使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉 | Microsoft Azure" 
    description="使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# 使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md)


本文說明如何使用 [Azure 入口網站](http://portal.azure.com)起始容錯移轉至次要 SQL Database。若要設定「異地複寫」，請參閱[為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-portal.md)。


## 起始容錯移轉

次要資料庫可被切換成為主要資料庫。

1. 在 [Azure 入口網站](http://portal.azure.com)中，瀏覽至「異地複寫」合作關係中的主要資料庫。
2. 在 [SQL Database] 刀鋒視窗上，選取 [所有設定] > [異地複寫]。
3. 在 [次要] 清單中，選取要做為新主要資料庫的資料庫，然後按一下 [容錯移轉]。

    ![容錯移轉][2]

4. 按一下 [是] 即可開始容錯移轉。

命令會立即會將次要資料庫切換為主要角色。

切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。如果主要資料庫有多個次要資料庫，此命令會自動重新設定其他次要複本以連接至新的主要複本。在正常情況下，完成整個作業所需的時間應該少於一分鐘。

>[AZURE.NOTE] 發出命令時，如果主要複本處於線上並且正在認可交易，可能會發生部分資料遺失。


## 其他資源   


- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [新異地複寫功能要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [使用異地複寫設計業務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

<!---HONumber=AcomDC_0608_2016-->