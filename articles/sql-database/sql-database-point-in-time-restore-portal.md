<properties
	pageTitle="將 Azure SQL Database 還原到先前的時間點 (Azure 入口網站) | Microsoft Azure"
	description="將 Azure SQL Database 還原至先前的時間點。"
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


# 使用 Azure 入口網站將 Azure SQL Database 還原至先前的時間點


> [AZURE.SELECTOR]
- [概觀](sql-database-point-in-time-restore.md)
- [Azure 入口網站](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

本文將說明如何使用 Azure 入口網站，從 [SQL Database 自動備份](sql-database-automated-backups.md)將資料庫還原至先前的時間點。

## 選取要還原至先前時間點的資料庫

若要在 Azure 入口網站中還原資料庫，請執行下列動作：

1.	開啟 [Azure 入口網站](https://portal.azure.com)。
2.  在畫面左側選取 [瀏覽] > [SQL Database]。
3.  瀏覽至您想要還原的資料庫並加以選取。
4.  在資料庫刀鋒視窗的頂端，選取 [還原]：

    ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  指定資料庫名稱、時間點，然後按一下 [確定]：

    ![還原 Azure SQL Database](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## 後續步驟

- [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)
- [時間點還原](sql-database-point-in-time-restore.md)
- [使用 REST API 進行時間點還原](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動備份](sql-database-automated-backups.md)

## 其他資源

- [還原已刪除的資料庫](sql-database-restore-deleted-database.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [異地還原](sql-database-geo-restore.md)
- [作用中異地複寫](sql-database-geo-replication-overview.md)
- [為雲端災害復原設計應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->