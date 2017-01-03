---
title: "在移轉至 SQL Database 之前修正 SQL Server 資料庫相容性問題 | Microsoft Docs"
description: "Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈, SSDT"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7ff52877-5b63-4adc-aa1a-689669a1146e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: e09c60111286681928ee1dd0b08fade7a102d6f2


---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>使用適用於 Visual Studio 的 SQL Server Data Tools 將 SQL Server 資料庫移轉至 Azure SQL Database
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

在這篇文章中，您將了解如何在移轉至 Azure SQL Database 之前，先使用「適用於 Visual Studio 的 SQL Server Data Tools」來偵測並修正 SQL Server 資料庫相容性問題。

## <a name="using-sql-server-data-tools-for-visual-studio"></a>使用 Visual Studio 的 SQL Server Data Tools
您可以使用 Visual Studio 的 SQL Server Data Tools ("SSDT") 將資料庫結構描述匯入 Visual Studio 資料庫專案以進行分析。 若要進行分析，請將專案的目標平台指定為 SQL Database V12，然後再建置專案。 如果建置成功，則資料庫為相容。 如果建置失敗，您可以在 SSDT (或本主題中討論的其他工具之一) 中解決錯誤。 成功建置專案之後，您可以藉由來源資料庫複本的形式將它發佈回來。 然後您可以使用 SSDT 中的資料比較功能，將資料從來源資料庫複製到 Azure SQL V12 相容資料庫。 然後，您可以移轉此更新的資料庫。 若要使用此選項，請下載 [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)。

  ![VSSSDT 移轉圖表](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> 如果您需要的是僅限結構描述的移轉，則可以直接從 Visual Studio 將結構描述發佈至 Azure SQL Database。 當資料庫結構描述需要的變更超過移轉精靈單獨可處理的數量時，使用這個方法。
> 
> 

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>使用 SQL Server Data Tools for Visual Studio 偵測相容性問題
1. 開啟 Visual Studio 中的 [SQL Server 物件總管]  。 使用 [加入 SQL Server]  連接到包含移轉之資料庫的 SQL Server 執行個體。 在 [物件總管] 中找出資料庫，在該資料庫上按一下滑鼠右鍵，然後選取 [建立新專案]      
   
   ![New Project](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
2. 將匯入設定設為 [僅匯入應用程式範圍的物件] 。 將匯入下列資料的選項取消選取：參考的登入、權限及資料庫設定。    
   
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    
3. 按一下 [開始]  以匯入資料庫並建立專案，這包含資料庫中每個物件的 T-SQL 指令碼檔案。 專案內資料夾中的指令碼檔案為巢狀。    
   
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    
4. 在 Visual Studio「方案總管」中，於資料庫專案上按一下滑鼠右鍵，然後選取 [屬性]。 在 [專案設定] 頁面，將 [目標平台] 設定為 [Microsoft Azure SQL Database V12]。    
   
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
5. 以滑鼠右鍵按一下專案，然後選取 [建置]  來建置專案。    
   
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
6. [錯誤清單]  會顯示每一個不相容。 在此案例中，使用者名稱 NT AUTHORITY\NETWORK SERVICE 不相容。 由於它不相容，您可以加上註解，或將它移除 (和解決將此登入和角色從資料庫解決方案移除的連帶影響)。     
   
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    

## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>使用 SQL Server Data Tools for Visual Studio 修正相容性問題
1. 在第一個指令碼上按兩下，在查詢視窗中開啟指令碼，為指令碼加上註解，然後執行指令碼。     
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)
2. 對每一個含有不相容的指令碼重複執行此程序，直到沒有錯誤為止。    
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
3. 當資料庫沒有錯誤時，在專案上按一下滑鼠右鍵，然後選取 [發行] 。 就會建立和發行來源資料庫的複本 (強烈建議使用複本，至少在一開始時使用)。     
   
   * 在您發佈之前，根據來源 SQL Server 版本 (早於 SQL Server 2014)，您可能需要重設專案的目標平台以啟用部署。     
   * 如果您要移轉舊版 SQL Server 資料庫，在將資料庫移轉至新版 SQL Server 之前，請勿將來源 SQL Server 中不支援的任何功能導入到專案中。     
     
     ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
     
     ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
4. 在 [SQL Server 物件總管] 中，於來源資料庫上按一下滑鼠右鍵，然後按一下 [資料比較] 。 比較專案和原始的資料庫可協助您了解精靈所做的變更。 選取您的 Azure SQL V12 版本的資料庫，然後按一下 [完成] 。    
   
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
   
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    
5. 檢閱偵測到的差異，然後按一下 [更新目標]  將資料從來源資料庫移轉至 Azure SQL V12 資料庫。     
   
   ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
6. 選擇部署方法。 請參閱 [將相容的 SQL Server 資料庫移轉至 SQL Database](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>後續步驟
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>其他資源
* [SQL Database 功能](sql-database-features.md)
* [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
* [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


