---
title: "Azure 入口網站：將 Azure SQL Database 匯出到 BACPAC 檔案 | Microsoft Docs"
description: "使用 Azure 入口網站將 Azure SQL Database 匯出到 BACPAC 檔案"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>使用 Azure 入口網站將 Azure SQL Database 匯出到 BACPAC 檔案

本文提供的指示，說明如何使用 [Azure 入口網站](https://portal.azure.com)，將 Azure SQL Database 匯出到 BACPAC 檔案 (儲存於 Azure Blob 儲存體中)。 如需有關匯出至 BACPAC 檔案的概觀，請參閱[匯出至 BACPAC](sql-database-export.md)。

> [!NOTE]
> 您也可以使用 [SQL Server Management Studio](sql-database-export-ssms.md)、[PowerShell](sql-database-export-powershell.md) 或 [SQLPackage](sql-database-export-sqlpackage.md)，將 Azure SQL 資料庫檔案匯出到 BACPAC 檔案。
>

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要下列項目：

* Azure 訂用帳戶。
* Azure SQL Database。 
* 用來在標準儲存體中儲存 BACPAC 的 [Azure 標準儲存體帳戶](../storage/storage-create-storage-account.md) 與 Blob 容器。

## <a name="export-your-database"></a>匯出您的資料庫

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 開啟欲匯出資料庫的 [SQL Database] 刀鋒視窗。
3. 請確定匯出期間沒有任何交易發生。 

   > [!IMPORTANT]
   > 若要保證在交易上一致的 BACPAC 檔案，最好先[建立您的資料庫複本](sql-database-copy.md)，然後從資料庫複本匯出。 
   > 

4. 按一下 [SQL Database] 。
5. 按一下要封存的資料庫。
6. 在 [SQL Database] 刀鋒視窗中，按一下 [匯出] 開啟 [匯出資料庫] 刀鋒視窗：
   
   ![匯出按鈕][1]
7. 按一下 [儲存體]，並選取您的儲存體帳戶以及要儲存 BACPAC 的 Blob 容器：
   
   ![匯出資料庫][2]
8. 選取驗證類型。 
9. 針對包含欲匯出之資料庫的 Azure SQL Server，輸入適當的驗證認證。
10. 按一下 [確定] 匯出資料庫。 按一下 [確定]  時，會建立匯出資料庫要求並將它提交至服務。 匯出所需的時間長度取決於您資料庫的大小和複雜性，以及您的服務等級。 
11. 檢視您收到的通知。
   
   ![匯出通知][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>監視匯出作業的進度
1. 按一下 [SQL Server] 。
2. 按一下包含您封存之原始 (來源) 資料庫的伺服器。
3. 向下捲動到 [作業]。
4. 在 SQL Server 刀鋒視窗中，按一下 [匯入/匯出記錄] ：
   
   ![匯入匯出記錄][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>確認 BACPAC 位於儲存體容器中
1. 按一下 [儲存體帳戶] 。
2. 按一下您用來儲存 BACPAC 封存檔的儲存體帳戶。
3. 按一下 [ **容器** ]，並選取存放匯出資料庫的容器，以取得詳細資料 (您也可以從這裡下載並儲存 BACPAC)。
   
   ![.bacpac 檔案詳細資料][5]    

## <a name="next-steps"></a>後續步驟

* 若要了解除了匯出的資料庫，還有 Azure SQL 資料庫備份的長期備份保留也可達到封存目的，請參閱[長期備份保留](sql-database-long-term-retention.md)
* 若要了解如何將 BACPAC 匯入 SQL Server 資料庫，請參閱 [將 BACPAC 匯入 SQL Server 資料庫](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


