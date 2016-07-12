<properties
   pageTitle="還原 Azure SQL 資料倉儲 (入口網站) | Microsoft Azure"
   description="還原 Azure SQL 資料倉儲的 Azure 入口網站工作。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess;nicw"/>

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

1. 登入 [Azure 入口網站][]
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Server]
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. 瀏覽至您的伺服器，然後選取它
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. 尋找您想要還原的 SQL 資料倉儲，然後選取它
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. 在 [資料倉儲] 刀鋒視窗頂端，按一下 [還原]
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. 指定新的**資料庫名稱**
7. 選取最新的**還原點**
    1. 確定您選擇了最新的還原點。由於還原點是以 UTC 格式顯示，因此，所顯示的預設選項有時不是最新的還原點。
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. 按一下 [確定]。
9. 資料庫還原程序將會開始，您可以使用 [通知] 來監視此程序

>[AZURE.NOTE] 還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。


## 還原已刪除的資料庫

還原已刪除的資料庫：

1. 登入 [Azure 入口網站][]
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Server]
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. 瀏覽至您的伺服器，然後選取它
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. 在伺服器的刀鋒視窗上捲動至 [作業] 區段
5. 按一下 [已刪除的資料庫] 圖格
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. 選取您想要還原的已刪除資料庫
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. 指定新的**資料庫名稱**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. 按一下 [確定]。
9. 資料庫還原程序將會開始，您可以使用 [通知] 來監視此程序

>[AZURE.NOTE] 還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。


## 後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: ./sql-database-business-continuity.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[完成復原的資料庫]: ./sql-database-recovered-finalize.md

<!--MSDN references-->

<!--Blog references-->
[如何檢視和增加 DTU 配額]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0629_2016-->