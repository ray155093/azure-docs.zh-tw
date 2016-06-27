<properties
   pageTitle="還原 Azure SQL 資料倉儲 (入口網站) | Microsoft Azure"
   description="還原 Azure SQL 資料倉儲的 Azure 入口網站工作。"
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
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# 還原 Azure SQL 資料倉儲 (入口網站)

> [AZURE.SELECTOR]
- [概觀][]
- [入口網站][]
- [PowerShell][]
- [REST][]

在本文中，您將了解如何使用 Azure 入口網站來還原 Azure SQL 資料倉儲。

## 開始之前

**請驗證您的 DTU 容量。** 每個 SQL 資料倉儲都是由 SQL Server 邏輯伺服器裝載。此邏輯伺服器的容量上限是以 DTU 測量。請務必先確保裝載您的資料庫的 SQL Server 邏輯伺服器擁有足夠的 DTU 容量可供正在還原的資料庫使用，才可以還原 SQL 資料倉儲。請參閱此部落格文章以深入了解[如何檢視和增加 DTU 配額][]。


## 還原作用中或已暫停的資料庫

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
6. 資料庫還原程序將會開始，您可以使用 [通知]來監視此程序。


## 後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: ./sql-database-business-continuity.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->

<!--Blog references-->
[如何檢視和增加 DTU 配額]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->