---
title: "匯入 BACPAC 檔案以建立 Azure SQL Database | Microsoft Docs"
description: "匯入現有的 BACPAC 檔案以建立 Azure SQL Database。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move; how to
ms.devlang: NA
ms.date: 08/31/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: 7ffa568e18224ffcf4286636069c1d6ee11d9c1f


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database"></a>匯入 BACPAC 檔案以建立 Azure SQL Database
**單一資料庫**

> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

本文提供使用 [Azure 入口網站](https://portal.azure.com)自 BACPAC 檔案建立 Azure SQL Database 的說明。

BACPAC 是一種包含資料庫結構描述和資料的 .bacpac 檔案。 資料庫是透過由 Azure 儲存體 Blob 容器匯入的 BACPAC 來建立。 如果您的 Azure 儲存體沒有 .bacpac 檔案，可依照 [建立和匯出 Azure SQL Database 的 BACPAC](sql-database-export.md)中的步驟，加以建立。

> [!NOTE]
> Azure SQL Database 會自動為每個使用者資料庫建立並維護可供還原的備份。 如需詳細資訊，請參閱 [商務持續性概觀](sql-database-business-continuity.md)。
> 
> 

若要從 .bacpac 匯入 SQL Database，您需要下列項目：

* Azure 訂用帳戶。 
* Azure SQL Database V12 伺服器。 如果沒有 V12 伺服器，請遵循本文中以下的步驟： [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
* 您想要匯入 [Azure 儲存體帳戶 (標準)](../storage/storage-create-storage-account.md) Blob 容器之資料庫的 .bacpac 檔案。

> [!IMPORTANT]
> 從 Azure Blob 儲存體匯入 BACPAC 時，請使用標準儲存體。 不支援從進階儲存體匯入 BACPAC。
> 
> 

## <a name="select-the-server-to-host-the-database"></a>選取要裝載資料庫的伺服器
開啟 SQL Server 刀鋒視窗：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [SQL Server] 。
3. 按一下伺服器，以將資料庫還原至該伺服器。
4. 在 SQL Server 刀鋒視窗中，按一下 [匯入資料庫] 以開啟 [匯入資料庫] 刀鋒視窗：
   
   ![匯入資料庫][1]
5. 按一下 [儲存體]，並選取您的儲存體帳戶、Blob 容器以及 .bacpac 檔，然後按一下 [確定]。
   
   ![設定儲存體選項][2]
6. 選取新資料庫的定價層，然後按一下 [選取] 。 不支援直接將資料庫匯入至彈性集區，但可以先匯入至單一資料庫，再將資料庫移到集區。
   
   ![選取定價層][3]
7. 針對您將從 BACPAC 檔案建立的資料庫，輸入 **資料庫名稱** 。
8. 選擇驗證類型，然後提供伺服器的驗證資訊。 
9. 按一下 [建立]  ，以從 BACPAC 建立資料庫。
   
   ![建立資料庫][4]

按一下 [建立]  時，會將匯入資料庫要求提交給服務。 視資料庫大小而定，匯入作業可能需要一些時間才能完成。

## <a name="monitor-the-progress-of-the-import-operation"></a>監視匯入作業的進度
1. 按一下 [SQL Server] 。
2. 按一下您要還原的目的地伺服器。
3. 在 [SQL Server] 刀鋒視窗的 [作業] 區域中，按一下 [匯入/匯出記錄] ：
   
   ![匯入匯出記錄][5]
   ![匯入匯出記錄][6]

## <a name="verify-the-database-is-live-on-the-server"></a>確認伺服器上的資料庫為線上狀態
1. 按一下 [SQL 資料庫] 並確認新的資料庫為 [線上] 狀態。

## <a name="next-steps"></a>後續步驟
* 若要了解如何連接並查詢匯入的 SQL Database，請參閱 [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)

<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png



<!--HONumber=Dec16_HO1-->


