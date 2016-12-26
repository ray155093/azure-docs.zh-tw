---
title: "在移轉至 Azure SQL Database 之前，先使用 SQL Server Management Studio 來判斷 SQL Database 相容性 | Microsoft Docs"
description: "Microsoft Azure SQL Database, 資料庫移轉, SQL Database 相容性, 匯出資料層應用程式精靈"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: c9ead868-aa1e-4a92-a099-6baf7c0dda32
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/29/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 15d0bf86d6094b6a8c0d6aaf1e1bb59382a81ffb


---
# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>在移轉至 Azure SQL Database 之前，先使用 SQL Server Management Studio 來判斷 SQL Database 相容性
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

在本文中，您將學習判斷 SQL Server 資料庫是否相容，以使用 SQL Server Management Studio 中的「匯出資料層應用程式精靈」移轉到 SQL Database。

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio
1. 請確認您有最新版本的 SQL Server Management Studio。 新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。
   
   > [!IMPORTANT]
   > 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
   > 
   > 
2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。
3. 以滑鼠右鍵按一下 [物件總管] 中的來源資料庫，指向 [工作]，並按一下 [匯出資料層應用程式]
   
    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. 在匯出精靈中，按一下 [下一步]，然後在 [設定] 索引標籤上，將匯出設定為將 BACPAC 檔案儲存到本機磁碟位置或 Azure Blob。 如果您有沒有資料庫相容性問題，才會儲存 BACPAC 檔案。 如果有相容性問題，它們會顯示在主控台上。
   
    ![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)
5. 若要略過匯出資料，請按一下 [進階] 索引標籤並清除 [全選] 核取方塊。 此時我們的目標是只測試相容性。
   
    ![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)
6. 然後依序按一下 [下一步] 和 [完成]。 資料庫相容性問題 (如果有的話) 會在精靈驗證結構描述之後出現。
   
    ![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)
7. 如果沒有任何錯誤出現，代表您的資料庫相容且您已準備好移轉。 如果您有錯誤，您必須加以修正。 若要查看錯誤，請按一下 [錯誤] 以**驗證結構描述**。 
    ![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)
8. 如果 *.BACPAC 檔案已成功產生，則您的資料庫與 SQL Database 相容，且您已準備好移轉。

## <a name="next-steps"></a>後續步驟
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [修正資料庫移轉相容性問題](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [將相容 SQL Server 資料庫移轉到 SQL Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他資源
* [SQL Database 功能](sql-database-features.md)
* [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
* [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


