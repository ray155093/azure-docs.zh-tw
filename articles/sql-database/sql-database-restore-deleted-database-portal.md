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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 還原使用 Azure 入口網站的已刪除 Azure SQL Database


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

本文會向您示範如何還原已刪除的 Azure SQL Database。

若資料庫被刪除，Azure SQL Database 可讓您將已刪除的資料庫還原至刪除的時間點。Azure SQL Database 會將已刪除的資料庫備份，儲存一段資料庫保留期間的時間。

已刪除資料庫的保留期間取決於資料庫所在的服務層，或是資料庫存在的天數，兩者中較少的天數。若要深入了解資料庫保留，請閱讀我們的[商務持續性概觀](sql-database-business-continuity.md)。

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
- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)



## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->