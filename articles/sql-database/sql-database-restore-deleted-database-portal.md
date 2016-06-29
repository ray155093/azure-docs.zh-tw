<properties
	pageTitle="還原已刪除的 Azure SQL Database (Azure 入口網站) | Microsoft Azure"
	description="還原已刪除的 Azure SQL Database (Azure 入口網站)。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 還原使用 Azure 入口網站的已刪除 Azure SQL Database

> [AZURE.SELECTOR]
- [概觀](sql-database-restore-deleted-database.md)
- [Azure 入口網站](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## 選取要還原的資料庫 

若要在 Azure 入口網站中還原資料庫，請執行下列動作：

1.	開啟 [Azure 入口網站](https://portal.azure.com)。
2.  在畫面左側選取 [瀏覽] > [SQL Server]。
3.  瀏覽至包含已刪除資料庫的伺服器，您想要還原與選取伺服器
4.  向下捲動至伺服器刀鋒視窗的 [作業] 區段，然後選取 [已刪除的資料庫]：![還原 Azure SQL Database](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  按一下您想要還原的已刪除資料庫。
6.  指定資料庫名稱，然後按一下 [確定]：

    ![還原 Azure SQL Database](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## 後續步驟

- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)
- [使用 PowerShell 還原已刪除的資料庫](sql-database-restore-deleted-database-powershell.md)
- [使用 REST API 還原已刪除的資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動備份](sql-database-automated-backups.md)

## 其他資源

- [時間點還原](sql-database-point-in-time-restore.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->