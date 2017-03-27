---
title: "VS Code：在 Azure SQL Database 中連接和查詢資料 | Microsoft Docs"
description: "了解如何使用 Visual Studio Code 在 Azure 上連接到 SQL Database。 然後，執行 TRANSACT-SQL (T-SQL) 陳述式來查詢和編輯資料。"
metacanonical: 
keywords: "連線到 SQL Database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9192ed7b69e52c59efe33981e1e557b634679196
ms.lasthandoff: 03/21/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database︰使用 Visual Studio Code 連接及查詢資料

[Visual Studio Code](https://code.visualstudio.com/docs) 是 Linux、macOS 及 Windows 適用的圖形化程式碼編輯器，可支援擴充功能。 使用 Visual Studio Code 搭配 [mssql 擴充功能](https://aka.ms/mssql-marketplace)來連接和查詢 Azure SQL Database。 本執行詳細說明如何使用 Visual Studio Code 連線至 Azure SQL Database，然後執行查詢、插入、更新和刪除陳述式。

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)
- [建立 DB - PowerShell](sql-database-get-started-powershell.md) 

開始之前，確定您已安裝最新版的 [Visual Studio Code](https://code.visualstudio.com/Download) 並已載入 [mssql 擴充功能](https://aka.ms/mssql-marketplace)。 如需 mssql 擴充功能的安裝指引，請參閱[安裝 VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code)。 

## <a name="get-connection-information"></a>取得連線資訊

在 Azure 入口網站中取得 Azure SQL Database 伺服器的完整伺服器名稱。 透過 Visual Studio Code，您可使用此完整伺服器名稱連接到您的伺服器。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在 Azure 入口網站中您資料庫的 [基本資訊] 窗格中，找到後複製 [伺服器名稱] 以便稍後在本快速入門中使用。

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>將語言模式設定為 SQL

在 Visual Studio Code 中將語言模式設定為 **SQL**，以啟用 mssql 命令和 T-SQL IntelliSense。

1. 開啟新的 Visual Studio Code 視窗。 

2. 按 **CTRL+K、M**，輸入 **SQL** 並且按 **ENTER**，將語言模式設定為 SQL。 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>連接到伺服器

使用 Visual Studio Code 建立對 Azure SQL Database 伺服器的連線。

1. 在 VS Code 中，按 **CTRL+SHIFT+P** (或 **F1**) 以開啟命令選擇區。

2. 輸入 **sqlcon** 並且按 **ENTER**。

3. 按一下 [是] 將語言設定為 **SQL**。

4. 按 **ENTER** 以選取 [建立連線設定檔]。 這會為您的 SQL Server 執行個體建立連線設定檔。

5. 請依照提示指定新連線設定檔的連線屬性。 指定每個值之後，按 **ENTER** 繼續。 

   下表說明連線設定檔屬性。

   | 設定 | 說明 |
   |-----|-----|
   | **伺服器名稱** | 輸入您的完整伺服器名稱，例如 **mynewserver20170313.database.windows.net** |
   | **資料庫名稱** | 輸入您的資料庫名稱，例如 **mySampleDatabase** |
   | **驗證** | 選取 SQL 登入 |
   | **使用者名稱** | 輸入您的伺服器管理帳戶 |
   | **密碼 (SQL 登入)** | 輸入伺服器管理帳戶的密碼 | 
   | **儲存密碼？** | 選取 [是] 或 [否] |
   | **[選用] 輸入這個設定檔的名稱** | 輸入連線設定檔名稱，例如 **mySampleDatabase**。 

6. 按 **ESC** 鍵來關閉通知已建立並連接設定檔的資訊訊息。

7. 在狀態列中確認您的連線。

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>查詢資料

使用 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 陳述式在 Azure SQL Database 中查詢資料。

1. 在 [編輯器] 視窗中，於空白查詢視窗中輸入下列查詢︰

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. 按 **CTRL+SHIFT+E** 來擷取 Product 和 ProductCategory 資料表中的資料。

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>插入資料

使用 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 陳述式在 Azure SQL Database 中插入資料。

1. 在 [編輯器] 視窗中，刪除先前的查詢並輸入下列查詢︰

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

3. 按 **CTRL+SHIFT+E** 以在 Product 資料表中插入新資料列。

## <a name="update-data"></a>更新資料

使用 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 陳述式在 Azure SQL Database 中更新資料。

1.  在 [編輯器] 視窗中，刪除先前的查詢並輸入下列查詢︰

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. 按 **CTRL+SHIFT+E** 以在 Product 資料表中更新指定的資料列。

## <a name="delete-data"></a>刪除資料

使用 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 陳述式在 Azure SQL Database 中刪除資料。

1. 在 [編輯器] 視窗中，刪除先前的查詢並輸入下列查詢︰

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. 按 **CTRL+SHIFT+E** 以在 Product 資料表中刪除指定的資料列。

## <a name="next-steps"></a>後續步驟

- 如需 Visual Studio Code 的詳細資訊，請參閱 [Visual Studio Code](https://code.visualstudio.com/docs)
- 如需使用 SQL Server Management Studio 查詢和編輯資料的詳細資訊，請參閱 [SSMS](https://msdn.microsoft.com/library/ms174173.aspx)。
