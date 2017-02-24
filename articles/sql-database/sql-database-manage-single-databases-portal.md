---
title: "Azure 入口網站：建立及管理單一 Azure SQL 資料庫 | Microsoft Docs"
description: "有關如何使用 Azure 入口網站來建立及管理單一 Azure SQL 資料庫的快速參考資料"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 319e22e2fb423e0fe1fabf95d0018d4fa3232e0b
ms.openlocfilehash: 5d7f11b9b027f86adf3d2382e82f2d382d71ee51


---
# <a name="create-and-manage-single-azure-sql-databases-with-the-azure-portal"></a>使用 Azure 入口網站來建立及管理單一 Azure SQL 資料庫

您可以使用 [Azure 入口網站](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API 或 C# 來建立及管理單一 Azure SQL 資料庫。 此主題是關於使用 Azure 入口網站。 對於 PowerShell，請參閱[使用 Powershell 來建立及管理單一資料庫](sql-database-manage-single-databases-powershell.md)。 對於 Transact-SQL，請參閱[使用 Transact-SQL 來建立及管理單一資料庫](sql-database-manage-single-databases-tsql.md)。 

## <a name="create-a-single-azure-sql-database-with-the-azure-portal"></a>使用 Azure 入口網站來建立單一 Azure SQL 資料庫

1. 開啟 [Azure 入口網站](https://portal.azure.com/)中的 [SQL Database] 刀鋒視窗。 

    ![SQL Database](./media/sql-database-get-started/sql-databases.png)
2. 在 SQL Database 刀鋒視窗中，按一下 [新增]。

    ![新增 SQL Database](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> 如需有關使用 Azure 入口網站來建立資料庫的教學課程，請參閱[建立資料庫 - Azure 入口網站](sql-database-get-started.md)。
>    

## <a name="view-and-update-sql-database-settings-using-the-azure-portal"></a>使用 Azure 入口網站檢視和更新 SQL Database 設定

1. 開啟 [Azure 入口網站](https://portal.azure.com/)中的 [SQL Database] 刀鋒視窗。 

    ![SQL Database](./media/sql-database-get-started/sql-databases.png)

2. 按一下您想使用的資料庫，然後按一下 [SQL Database] 刀鋒視窗上所需的設定。

    ![新範例資料庫刀鋒視窗](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>變更單一資料庫的服務層與效能等級
開啟您想要相應增加或減少之資料庫的 [SQL Database] 刀鋒視窗：

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [更多服務]  >  [SQL Database]。
2. 按一下您想要變更的資料庫。
3. 在 [SQL 資料庫] 刀鋒視窗上，按一下 [定價層 (調整 DTU)]：
   
   ![定價層](./media/sql-database-manage-single-database-portal/new-tier.png)

4. 選取新的階層，然後按一下 [選取]：
   
   按一下 [選取] 提交調整要求以變更定價層。 視資料庫大小而定，調整作業需要一些時間才能完成 (請參閱此文章一開始的資訊)。
   
   > [!NOTE]
   > 變更資料庫定價層不會變更資料庫大小上限。 若要變更資料庫大小上限，請使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 或 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)。
   > 
   > 
   
   ![選取定價層](./media/sql-database-manage-single-database-portal/choose-tier.png)
5. 按一下右上角的通知圖示 (鐘)︰
   
   ![通知](./media/sql-database-manage-single-database-portal/scale-notification.png)
   
6. 按一下通知文字以開啟詳細資料窗格，您可以在其中看到要求的狀態。

## <a name="next-steps"></a>後續步驟
* 如需管理工具的概觀，請參閱[管理工具概觀](sql-database-manage-overview.md)。
* 若要了解如何使用 Azure 入口網站執行管理工作，請參閱[使用 Azure 入口網站管理 Azure SQL Database](sql-database-manage-portal.md)。
* 若要了解如何使用 PowerShell 執行管理工作，請參閱[使用 PowerShell 管理 Azure SQL Database](sql-database-manage-powershell.md)。
* 若要了解如何使用 SQL Server Management Studio 執行管理工作，請參閱 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 如需了解 SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database](sql-database-technical-overview.md)。 
* 如需 Azure 資料庫伺服器和資料庫功能的相關資訊，請參閱[功能](sql-database-features.md)。



<!--HONumber=Feb17_HO2-->


