<properties
   pageTitle="Azure SQL 資料倉儲中的資料庫還原 (Azure 入口網站) | Microsoft Azure"
   description="還原 Azure SQL 資料倉儲中的即時、已刪除或無法存取之資料庫的 Azure 入口網站工作。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/02/2016"
   ms.author="elfish;barbkess;sonyama"/>

# 備份與還原 Azure SQL 資料倉儲中的資料庫 (Azure 入口網站)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-manage-database-restore.md)
- [入口網站](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

在 SQL 資料倉儲中還原資料庫的 Azure 入口網站工作。

本主題中的工作：

- 還原即時資料庫
- 還原已刪除的資料庫

## 開始之前

請驗證您的 SQL Database DTU 容量。由於 SQL 資料倉儲會還原至位於您邏輯 SQL Server 上的新資料庫，請務必確保您還原至的 SQL Server 擁有足夠的 DTU 容量可供新資料庫使用。如需有關[如何檢視和增加 DTU 配額][]的詳細資訊，請參閱此部落格文章。


## 還原即時資料庫

還原資料庫：

1. 登入 [Azure 入口網站][]。
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Database]。
3. 瀏覽至您的資料庫，然後選取它。
4. 在資料庫刀鋒視窗頂端，按一下 [還原]。
5. 指定新的 [資料庫名稱]、選取 [還原點]，然後按一下 [建立]。
6. 資料庫還原程序將會開始，您可以使用 [通知] 來監視此程序。


## 還原已刪除的資料庫

還原已刪除的資料庫：

1. 登入 [Azure 入口網站][]。
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Server]。
3. 瀏覽至您的伺服器，然後選取它。
4. 在伺服器的刀鋒視窗上，向下捲動至 [作業]，然後按一下 [已刪除的資料庫] 圖格。
5. 按一下您想要還原的已刪除資料庫。
5. 指定新的 [資料庫名稱]，然後按一下 [建立]。
6. 資料庫還原程序將會開始，您可以使用 [通知] 來監視此程序。


## 後續步驟
如需詳細資訊，請參閱 [Azure SQL Database 商務持續性概觀][]和[管理概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: sql-database-business-continuity.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md
[管理概觀]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->

<!--Blog references-->
[如何檢視和增加 DTU 配額]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->