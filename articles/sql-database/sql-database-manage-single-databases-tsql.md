---
title: "T-SQL：建立及管理單一 Azure SQL 資料庫 | Microsoft Docs"
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
ms.sourcegitcommit: 38c53d6f1a2635afbba199e6f0a2adb23abb6a3a
ms.openlocfilehash: fcf8326edc2ff84d274f7c4dadeae1fbe2595349


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>使用 Transact-SQL 來建立及管理單一 Azure SQL 資料庫

您可以使用 [Azure 入口網站](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API 或 C# 來建立及管理單一 Azure SQL 資料庫。 此主題是關於使用 Azure 入口網站。 對於 PowerShell，請參閱[使用 Powershell 來建立及管理單一資料庫](sql-database-manage-single-databases-powershell.md)。 對於 Transact-SQL，請參閱[使用 Transact-SQL 來建立及管理單一資料庫](sql-database-manage-single-databases-tsql.md)。 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中使用 Transact-SQL 建立 Azure SQL Database

若要在 SQL Server Management Studio 中使用 Transact-SQL 建立 SQL Database：

1. 使用 SQL Server Management Studio，以伺服器層級主體登入或 **dbmanager** 角色成員的登入連接到 Azure 資料庫伺服器。 如需登入的詳細資訊，請參閱[管理登入](sql-database-manage-logins.md)。
2. 在 [物件總管] 中，開啟 Databases 資料夾、展開 [System Databases] 資料夾、在 [master] 上按一下滑鼠右鍵，然後按一下 [新增查詢]。
3. 使用 **CREATE DATABASE** 陳述式來建立資料庫。 如需詳細資訊，請參閱 [CREATE DATABASE (SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)。 下列陳述式會建立名為 **myTestDB** 的資料庫，並指定它是預設大小上限為 250 GB 的「標準 S0 版本」資料庫。
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. 按一下 [執行]  來執行查詢。
5. 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫] 節點，然後按一下 [重新整理]，以在 [物件總管] 中檢視新的資料庫。 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>變更單一資料庫的服務層與效能等級
使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 來變更您的資料庫大小上限

> [!TIP]
> 如需有關使用 Transact-SQL 來建立資料庫的教學課程，請參閱[建立資料庫 - Azure 入口網站](sql-database-get-started.md)。
>

## <a name="next-steps"></a>後續步驟
* 如需管理工具的概觀，請參閱[管理工具概觀](sql-database-manage-overview.md)。
* 若要了解如何使用 Azure 入口網站執行管理工作，請參閱[使用 Azure 入口網站管理 Azure SQL Database](sql-database-manage-portal.md)。
* 若要了解如何使用 PowerShell 執行管理工作，請參閱[使用 PowerShell 管理 Azure SQL Database](sql-database-manage-powershell.md)。
* 若要了解如何使用 SQL Server Management Studio 執行管理工作，請參閱 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 如需了解 SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database](sql-database-technical-overview.md)。 
* 如需 Azure 資料庫伺服器和資料庫功能的相關資訊，請參閱[功能](sql-database-features.md)。



<!--HONumber=Feb17_HO2-->


