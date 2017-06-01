---
title: "使用 Node.js 連接 Azure SQL Database | Microsoft Docs"
description: "提供可用來連線及查詢 Azure SQL Database 的 Node.js 程式碼範例。"
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4d3c3541749870b09aecc9efb63413f7c045e044
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Azure SQL Database︰使用 Node.js 連接及查詢資料

此快速入門示範如何使用 [Node.js](https://nodejs.org/en/) 連線至 Azure SQL Database，然後從 Windows、Ubuntu Linux 和 Mac 平台使用 Transact-SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。

## <a name="prerequisites"></a>必要條件

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)
- [建立 DB - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-nodejs"></a>安裝 Node.js 

本節中的步驟假設您已熟悉使用 Node.js 進行開發且不熟悉 Azure SQL Database。 如果您不熟悉使用 Node.js 進行開發，請前往[使用 SQL Server 建置應用程式](https://www.microsoft.com/en-us/sql-server/developer-get-started/)並選取 **Node.js**，然後選取您的作業系統。

### <a name="mac-os"></a>**Mac OS**
輸入下列命令以安裝 **brew**、適用於 Mac OS X 的簡易使用封裝管理員和 **Node.js**。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
輸入下列命令，以安裝 **Node.js** 和適用於 Node.js 的 **npm** 封裝管理員。

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
造訪 [Node.js 下載頁面](https://nodejs.org/en/download/)，並選取您需要的 Windows 安裝程式選項。


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>安裝適用於 Node.js 的 Tedious SQL Server 驅動程式
建議的 Node.js 驅動程式為 **[tedious](https://github.com/tediousjs/tedious)**。 Tedious 是 Microsoft 針對任何平台上之 Node.js 應用程式，所推出的開放原始碼計劃。 針對本教學課程，您需要建立一個空白目錄，以包含您的程式碼和將要安裝的 `npm` 相依性。

若要安裝 **tedious** 驅動程式，請在目錄中執行下列命令：

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>取得連線資訊

取得連線到 Azure SQL Database 所需的連線資訊。 您在下一個程序中需要完整的伺服器名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [概觀] 頁面上，檢閱如下圖所示的完整伺服器名稱。 您可將滑鼠移至伺服器名稱上，以帶出 [按一下以複製] 選項。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果您忘記 Azure SQL Database 伺服器的登入資訊，請瀏覽至 [SQL Database 伺服器] 頁面來檢視伺服器系統管理員名稱，並視需要重設密碼。
    
## <a name="select-data"></a>選取資料

使用下列程式碼來依照類別查詢 Azure SQL Database 中的前 20 項產品。 先從 tedious 驅動程式程式庫，匯入驅動程式 Connect and Request 類別。 接著建立組態物件，並將 **username**、**password**、**server** 和 **database** 變數的值，取代為您建立內含 AdventureWorksLT 範例資料之資料庫時所指定的值。 使用指定的 `config`物件建立 `Connection` 物件。 接著再定義 `connection` 物件的 `connect`事件，以執行 `queryDatabase()`函式。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

## <a name="insert-data-into-the-database"></a>將資料插入至資料庫
使用下列程式碼，藉由使用 `insertIntoDatabase()` 函式與 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 陳述式將新產品插入 SalesLT.Product 資料表中。 將 **username**、**password**、**server** 和 **database** 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>更新資料庫中的資料
使用下列程式碼，藉由使用 `updateInDatabase()` 函式與 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 陳述式來刪除您先前新增的產品。 將 **username**、**password**、**server** 和 **database** 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 此範例會使用先前範例中插入的產品名稱。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>從資料庫刪除資料
使用下列程式碼從資料庫刪除資料。 將 **username**、**password**、**server** 和 **database** 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 此時請在 `deleteFromDatabase()` 函式中使用 **DELETE 陳述式**。 此範例亦會使用先前範例中插入的產品名稱。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>後續步驟
- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft Node.js Driver for SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [使用 SSMS 連接及查詢](sql-database-connect-query-ssms.md)
- [使用 Visual Studio Code 連線及查詢](sql-database-connect-query-vscode.md)。



