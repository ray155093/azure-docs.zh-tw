---
title: "SSMS：將 SQL Server 資料庫匯出到 BACPAC 檔案 (Azure) | Microsoft Docs"
description: "本文說明如何使用 SQL Server Management Studio 中的「匯出資料層應用程式精靈」將 SQL Server 資料庫匯出到 BACPAC 檔案。"
keywords: "Microsoft Azure SQL Database, 資料庫移轉, 匯出資料庫, 匯出 BACPAC 檔案, 匯出資料層應用程式精靈"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f0238ac34a4a047bd0286a1a87d2427caf0e06fd


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 將 SQL Server 資料庫匯出到 BACPAC 檔案
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

本文說明如何使用 SQL Server Management Studio 中的「匯出資料層應用程式精靈」將 SQL 伺服器資料庫匯出成 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。 

1. 請確認您有最新版本的 SQL Server Management Studio。 新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。
   
   > [!IMPORTANT]
   > 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
   > 
   > 
2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。
   
    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)
3. 以滑鼠右鍵按一下 [物件總管] 中的來源資料庫，指向 [工作]，並按一下 [匯出資料層應用程式]
   
    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. 在匯出精靈中，將匯出設定為將 BACPAC 檔案儲存到本機磁碟位置或 Azure Blob。 匯出的 BACPAC 一律會包含完整的資料庫結構描述，以及預設之所有資料表的資料。 如果您想要排除部分或所有資料表的資料，請使用 [進階] 索引標籤。 比方說，您可能會選擇只匯出參考資料表的資料，而不是所有資料表的資料。

    ![匯出設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

   > [!IMPORTANT]
   > 將 BACPAC 匯出至 Azure blob 儲存體時，請使用標準儲存體。 不支援從進階儲存體匯入 BACPAC。
   >
   
## <a name="next-steps"></a>後續步驟
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [使用 SSMS 將 BACPAC 匯入 Azure SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [使用 SqlPackage 將 BACPAC 匯入 Azure SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [使用 Azure 入口網站將 BACPAC 匯入 Azure SQL Database](sql-database-import.md)
* [使用 PowerShell 將 BACPAC 匯入 Azure SQL Database](sql-database-import-powershell.md)

## <a name="additional-resources"></a>其他資源
* [SQL Database 功能](sql-database-features.md)
* [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
* [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


