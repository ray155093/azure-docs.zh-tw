---
title: "使用 PHP 連線至 Azure SQL Database by using PHP | Microsoft Docs"
description: "提供可用來連線及查詢 Azure SQL Database 的 PHP 程式碼範例。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 693bfdf7bb3d9d72fbd6ac42734ca261af7b492d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>Azure SQL Database︰使用 PHP 來連接及查詢資料

此快速入門示範如何使用 [PHP](http://php.net/manual/en/intro-whatis.php) 來連線至 Azure SQL Database，然後從 Mac OS、Ubuntu Linux 和 Windows 平台使用 Transact-SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。

## <a name="prerequisites"></a>必要條件

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)
- [建立 DB - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-php-and-database-communications-software"></a>安裝 PHP 與資料庫通訊軟體

本節中的步驟假設您已熟悉使用 PHP 進行開發且不熟悉 Azure SQL Database。 如果您不熟悉使用 PHP 進行開發，請前往[使用 SQL Server 建置應用程式](https://www.microsoft.com/en-us/sql-server/developer-get-started/)並選取 **PHP**，然後選取您的作業系統。

### <a name="mac-os"></a>**Mac OS**
開啟您的終端機並輸入下列命令，以安裝 **brew**、**Microsoft ODBC Driver for Mac** 和 **Microsoft PHP Drivers for SQL Server**。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
輸入下列命令，以安裝 **Microsoft ODBC Driver for Linux** 和 **Microsoft PHP Drivers for SQL Server**。

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- [從 WebPlatform Installer](https://www.microsoft.com/web/downloads/platform.aspx?lang=) 安裝 PHP 7.1.1 (x64) 
- 安裝 [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339)。 
- 下載 [Microsoft PHP Driver for SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) 的非安全執行緒 dll，然後將二進位檔放在 PHP\v7.x\ext 資料夾中。
- 接著，新增對 dll 的參考來編輯您的 php.ini (C:\Program Files\PHP\v7.1\php.ini) 檔案。 例如：
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

此時，您應該已向 PHP 註冊 dll。

## <a name="get-connection-information"></a>取得連線資訊

取得連線到 Azure SQL Database 所需的連線資訊。 您在下一個程序中需要完整的伺服器名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [概觀] 頁面上，檢閱如下圖所示的完整伺服器名稱。 您可將滑鼠移至伺服器名稱上，以帶出 [按一下以複製] 選項。  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果您忘記伺服器登入資訊，請瀏覽至 [SQL Database 伺服器] 頁面來檢視伺服器系統管理員名稱，並視需要重設密碼。     
    
## <a name="select-data"></a>選取資料
使用下列程式碼，以藉由使用 [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 函式與 [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL 陳述式來依照類別查詢前 20 項產品。 sqlsrv_query 函式可用來擷取對 SQL Database 執行之查詢的結果集。 此函式會接受查詢並傳回結果集，而您可以使用 [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php) 反覆查詢結果集。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
     FROM [SalesLT].[ProductCategory] pc
     JOIN [SalesLT].[Product] p
     ON pc.productcategoryid = p.productcategoryid";
$getResults= sqlsrv_query($conn, $tsql);
echo ("Reading data from table" . PHP_EOL);
if ($getResults == FALSE)
    echo (sqlsrv_errors());
while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
}
sqlsrv_free_stmt($getResults);
?>
```


## <a name="insert-data"></a>插入資料
使用下列程式碼，藉由使用 [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 函式與 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 陳述式將新產品插入 SalesLT.Product 資料表中。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>更新資料
使用 [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 函式和 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 陳述式，以使用下列程式碼更新 Azure SQL Database 中的資料。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>刪除資料
使用下列程式碼，藉由使用 [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 函式與 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 陳述式來刪除您先前新增的產品。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>後續步驟
- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/)
- [回報問題/發問](https://github.com/Microsoft/msphpsql/issues)


