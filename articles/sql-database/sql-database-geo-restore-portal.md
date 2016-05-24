<properties
	pageTitle="從異地備援備份 (Azure 入口網站) 異地還原 Azure SQL Database。| Microsoft Azure"
	description="從異地備援備份 (Azure 入口網站) 異地還原 Azure SQL Database。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 Azure 入口網站從異地備援備份異地還原 Azure SQL Database


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

本文說明如何使用 Azure 入口網站，將資料庫還原至使用異地還原的新伺服器。

[異地還原](sql-database-geo-restore.md)可從異地備援備份還原資料庫，進而建立新的資料庫。您可以在任何 Azure 區域中的任何伺服器上建立資料庫。異地還原使用異地備援備份做為其來源，因此即使因為中斷而無法存取資料庫，也能用來復原資料庫。異地還原會對所有服務層自動啟用，且不須額外付費。

## 選取要還原的資料庫

若要在 Azure 入口網站中還原資料庫，請執行下列動作：

1.	開啟 [Azure 入口網站](https://portal.azure.com)。
2.  在畫面左側選取 [新增] > [資料和儲存體] > [SQL Database]。
3.  選取 [備份] 做為來源，然後選取您要從中復原的異地備援備份。

    ![還原 Azure SQL Database](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  指定資料庫名稱，您想要還原資料庫的目標伺服器，然後按一下 [建立]︰

## 後續步驟

- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)


## 其他資源

- [異地還原](sql-database-geo-restore.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->