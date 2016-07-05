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
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 Azure 入口網站從異地備援備份異地還原 Azure SQL Database


> [AZURE.SELECTOR]
- [概觀](sql-database-geo-restore.md)
- [Azure 入口網站](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

本文說明如何透過 Azure 入口網站，使用異地還原來將資料庫還原至新的伺服器。

## 選取要還原的資料庫

若要在 Azure 入口網站中還原資料庫，請執行下列動作：

1.	開啟 [Azure 入口網站](https://portal.azure.com)。
2.  在畫面左側選取 [新增] > [資料和儲存體] > [SQL Database]。
3.  選取 [備份] 做為來源，然後選取您想要從中復原的異地備援備份。

    ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  指定資料庫名稱，您想要還原資料庫的目標伺服器，然後按一下 [建立]︰

## 後續步驟

- 如需如何使用 Azure 入口網站，從異地備援備份還原 Azure SQL Database 的詳細步驟，請參閱[使用 Azure 入口網站進行異地還原](sql-database-geo-restore-portal.md)
- 如需從異地備援備份還原 Azure SQL Database 的詳細資訊，請參閱[使用 PowerShell 進行異地還原](sql-database-geo-restore.md)
- 如需如何從中斷復原的完整討論，請參閱[從中斷復原](sql-database-disaster-recovery.md)

## 其他資源

- [商務持續性案例](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->