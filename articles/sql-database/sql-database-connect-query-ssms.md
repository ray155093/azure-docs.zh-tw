---
title: "SSMS: 在 Azure SQL Database 中連接和查詢資料 | Microsoft Docs"
description: "了解如何使用 SQL Server Management Studio (SSMS) 在 Azure 上連接到 SQL Database。 然後，執行 TRANSACT-SQL (T-SQL) 陳述式來查詢和編輯資料。"
metacanonical: 
keywords: "連接到 sql database,sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 7ae47bcce700336206d532b414b7d0eea41d87c5
ms.lasthandoff: 04/04/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL Database：使用 SQL Server Management Studio 連接及查詢資料

使用 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)，從使用者介面或在指令碼中建立和管理 SQL Server 資源。 這個快速入門詳細說明如何使用 SSMS 來連線至 Azure SQL Database，然後執行查詢、插入、更新和刪除陳述式。

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)

開始之前，確定您已安裝最新版的 [SSMS](https://msdn.microsoft.com/library/mt238290.aspx)。 

## <a name="get-connection-information"></a>取得連線資訊

在 Azure 入口網站中取得 Azure SQL Database 伺服器的完整伺服器名稱。 透過 SQL Server Management Studio，您可使用此完整伺服器名稱連接到您的伺服器。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在 Azure 入口網站中您資料庫的 [基本資訊] 窗格中，找到後複製 [伺服器名稱]。

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="connect-to-the-server-and-your-new-database"></a>連線至伺服器和新的資料庫

使用 SQL Server Management Studio (SSMS) 建立對 Azure SQL Database 伺服器的連線。

1. 開啟 SQL Server Management Studio。

2. 在 [連接到伺服器] 對話方塊中，輸入下列資訊：
   - **伺服器類型**：指定資料庫引擎
   - **伺服器名稱**︰輸入您的完整伺服器名稱，例如 **mynewserver20170313.database.windows.net**
   - **驗證**：指定 SQL Server 驗證
   - **登入**︰輸入您的伺服器管理帳戶
   - **密碼**：輸入伺服器管理帳戶的密碼
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. 按一下 [ **連接**]。 [物件總管] 視窗隨即在 SSMS 中開啟。 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. 在 [物件總管] 中，展開 [資料庫]，然後展開 [mySampleDatabase] 以檢視範例資料庫中的物件。

## <a name="query-data"></a>查詢資料

使用 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 陳述式在 Azure SQL Database 中查詢資料。

1. 在 [物件總管] 中，於 **mySampleDatabase** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。 隨即開啟已連線到您資料庫的空白查詢視窗。
2. 在查詢視窗中，輸入下列查詢︰

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. 在工具列上，按一下 [執行] 來擷取 Product 和 ProductCategory 資料表中的資料。

    <img src="./media/sql-database-connect-query-ssms/query.png" alt="query" style="width: 780px;" />

## <a name="insert-data"></a>插入資料

使用 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 陳述式在 Azure SQL Database 中插入資料。

1. 在工具列上，按一下 [新增查詢]。 隨即開啟已連線到您資料庫的空白查詢視窗。
2. 在查詢視窗中，輸入下列查詢︰

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

3. 在工具列上，按一下 [執行] 以在Product 資料表中插入新資料列。

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>更新資料

使用 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 陳述式在 Azure SQL Database 中更新資料。

1. 在工具列上，按一下 [新增查詢]。 隨即開啟已連線到您資料庫的空白查詢視窗。
2. 在查詢視窗中，輸入下列查詢︰

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. 在工具列上，按一下 [執行] 以在Product 資料表中更新指定的資料列。

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>刪除資料

使用 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 陳述式在 Azure SQL Database 中刪除資料。

1. 在工具列上，按一下 [新增查詢]。 隨即開啟已連線到您資料庫的空白查詢視窗。
2. 在查詢視窗中，輸入下列查詢︰

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. 在工具列上，按一下 [執行] 以在Product 資料表中刪除指定的資料列。

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>後續步驟

- 如需有關 SSMS 的資訊，請參閱 [使用 SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)。
- 如需使用 Visual Studio Code 查詢和編輯資料的詳細資訊，請參閱 [Visual Studio Code](https://code.visualstudio.com/docs)。

