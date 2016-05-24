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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 Azure 入口網站將 Azure SQL Database 還原至先前的時間點


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

本文將說明如何使用 Azure 入口網站將資料庫還原至先前的時間點。

[**還原時間點**](sql-database-point-in-time-restore.md)是自助功能，可讓您將資料庫從我們擷取自所有資料庫的自動備份還原至資料庫保留期間內的任何時間點。若要深入了解自動備份和資料庫保留期限，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

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
- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)



## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->