<properties
	pageTitle="從異地備援備份 (Azure 入口網站) 異地還原 Azure SQL Database | Microsoft Azure"
	description="從異地備援備份 (Azure 入口網站) 異地還原 Azure SQL Database。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/17/2016"
	ms.author="sstein"
	ms.workload="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 Azure 入口網站從異地備援備份異地還原 Azure SQL Database


> [AZURE.SELECTOR]
- [概觀](sql-database-recovery-using-backups.md)
- [異地還原：PowerShell](sql-database-geo-restore-powershell.md)

本文說明如何透過 Azure 入口網站，使用異地還原來將資料庫還原至新的伺服器。

## 選取要還原的資料庫

若要在 Azure 入口網站中還原資料庫，請執行下列動作：

1.	開啟 [Azure 入口網站](https://portal.azure.com)。
2.  在畫面左側選取 [新增] > [資料和儲存體] > [SQL Database]。
3.  選取 [備份] 做為來源，然後選取您想要從中復原的異地備援備份。

    ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  指定資料庫名稱，您想要還原資料庫的目標伺服器，然後按一下 [建立]︰


## 後續步驟

- 如需商務持續性概觀和案例，請參閱[商務持續性概觀](sql-database-business-continuity.md)
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
- 若要了解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)
- 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-geo-replication-overview.md)
- 若要了解如何使用自動備份進行封存，請參閱[資料庫複製](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->