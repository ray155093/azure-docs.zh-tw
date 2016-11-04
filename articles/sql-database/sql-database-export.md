---
title: 使用 Azure 入口網站將 Azure SQL Database 封存到 BACPAC 檔案
description: 使用 Azure 入口網站將 Azure SQL Database 封存到 BACPAC 檔案
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# 使用 Azure 入口網站將 Azure SQL Database 封存到 BACPAC 檔案
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-export.md)
> * [PowerShell](sql-database-export-powershell.md)
> 
> 

本文提供的指示，說明如何使用 [Azure 入口網站](https://portal.azure.com)，將 Azure SQL Database 封存到 BACPAC 檔案 (儲存於 Azure Blob 儲存體中)。

當您需要建立 Azure SQL Database 的封存檔時，可以將資料庫結構描述和資料匯出到 BACPAC 檔案。BACPAC 檔案就是副檔名為 BACPAC 的 ZIP 檔案。BACPAC 檔案可以稍後儲存在 Azure Blob 儲存體，或在內部部署位置的本機儲存體中，之後再匯入至 Azure SQL Database 或 SQL Server 內部部署安裝。

***注意事項***

* 為了讓封存檔處於交易一致狀態，您必須確定在匯出期間未發生任何寫入活動，或者是從 Azure SQL Database 的[交易一致性複本](sql-database-copy.md)匯出。
* 封存到 Azure Blob 儲存體的 BACPAC 檔案大小上限為 200 GB。若要將較大的 BACPAC 檔案封存到本機儲存體，請使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式。此公用程式隨附於 Visual Studio 和 SQL Server。您也可以[下載](https://msdn.microsoft.com/library/mt204009.aspx)最新版的 SQL Server Data Tools 以取得此公用程式。
* 不支援使用 BACPAC 檔案封存 Azure 進階儲存體。
* 如果匯出作業超過 20 個小時，它可能會被取消。若要增加匯出期間的效能，您可以︰
  * 暫時提高您的服務等級。
  * 在匯出期間停止所有讀取及寫入活動。
  * 在所有大型資料表上搭配使用 [叢集索引](https://msdn.microsoft.com/library/ms190457.aspx)和非 null 值。若沒有叢集索引，如果要花超過 6-12 小時，匯出可能會失敗。這是因為匯出服務需要完成資料表掃描，以便嘗試匯出整份資料表。用來判斷資料表是否已針對匯出進行最佳化的最佳方式是執行 DBCC SHOW\_STATISTICS，並確定 RANGE\_HI\_KEY 不是 null 且其值具有良好的分佈。如需詳細資訊，請參閱 [DBCC SHOW\_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 並非用於備份和還原作業。Azure SQL Database 會自動為每個使用者資料庫建立備份。如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。
> 
> 

若要完成本文，您需要下列項目：

* Azure 訂用帳戶。
* Azure SQL Database。
* 用來在標準儲存體中儲存 BACPAC 的 [Azure 標準儲存體帳戶](../storage/storage-create-storage-account.md)與 Blob 容器。

## 匯出您的資料庫
開啟欲匯出資料庫的 [SQL Database] 刀鋒視窗。

> [!IMPORTANT]
> 若要保證在交易上一致的 BACPAC 檔案，您應該先[建立您的資料庫複本](sql-database-copy.md)，然後匯出資料庫複本。
> 
> 

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [SQL Database]。
3. 按一下要封存的資料庫。
4. 在 [SQL Database] 刀鋒視窗中，按一下 [匯出] 以開啟 [匯出資料庫] 刀鋒視窗：
   
   ![匯出按鈕][1]
5. 按一下 [**儲存體**]，並選取您的儲存體帳戶以及要儲存 BACPAC 的 Blob 容器：
   
   ![匯出資料庫][2]
6. 選取驗證類型。
7. 針對包含欲匯出之資料庫的 Azure SQL Server，輸入適當的驗證認證。
8. 按一下 [確定] 封存資料庫。按一下 [確定] 時，會建立匯出資料庫要求並將它提交至服務。匯出所需的時間長度取決於您資料庫的大小和複雜性，以及您的服務等級。您將會收到通知。
   
   ![匯出通知][3]

## 監視匯出作業的進度
1. 按一下 [SQL Server]。
2. 按一下包含您剛才封存之原始 (來源) 資料庫的伺服器。
3. 向下捲動到 [作業]。
4. 在 SQL Server 刀鋒視窗中，按一下 [匯入/匯出記錄]：
   
   ![匯入匯出記錄][4]

## 確認 BACPAC 位於儲存體容器中
1. 按一下 [儲存體帳戶]。
2. 按一下您用來儲存 BACPAC 封存檔的儲存體帳戶。
3. 按一下 [**容器**]，並選取存放匯出資料庫的容器，以取得詳細資料 (您也可以從這裡下載並儲存 BACPAC)。
   
   ![.bacpac 檔案詳細資料][5]

## 後續步驟
* 若要了解如何將 BACPAC 匯入 Azure SQL Database，請參閱[將 BACPAC 匯入 Azure SQL Database](sql-database-import.md)
* 若要了解如何將 BACPAC 匯入 SQL Server 資料庫，請參閱[將 BACPAC 匯入 SQL Server 資料庫](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

<!---HONumber=AcomDC_0817_2016-->