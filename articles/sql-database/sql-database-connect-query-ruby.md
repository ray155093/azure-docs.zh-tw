---
title: "使用 Ruby 來連接到 Azure SQL Database | Microsoft Docs"
description: "提供可用來連線及查詢 Azure SQL Database 的 Ruby 程式碼範例。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: b25ef8333a2836f976a974d6ea6e7fdcea2745e3
ms.contentlocale: zh-tw
ms.lasthandoff: 07/08/2017

---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Azure SQL Database︰使用 Ruby 來連接及查詢資料

此快速入門示範如何使用 [Ruby](https://www.ruby-lang.org) 來連線至 Azure SQL Database，然後從 Mac OS 和 Ubuntu Linux 平台使用 Transact-SQL 陳述式查詢、插入、更新和刪除資料庫中的資料。

## <a name="prerequisites"></a>必要條件

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)
- [建立 DB - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-ruby-and-database-communication-libraries"></a>安裝 Ruby 與資料庫通訊程式庫

本節中的步驟假設您已熟悉使用 Ruby 進行開發且不熟悉 Azure SQL Database。 如果您不熟悉使用 Ruby 進行開發，請前往[使用 SQL Server 建置應用程式](https://www.microsoft.com/en-us/sql-server/developer-get-started/)並選取 **Ruby**，然後選取您的作業系統。

### <a name="mac-os"></a>**Mac OS**
開啟您的終端機，然後瀏覽至您打算在其中建立 Ruby 指令碼的目錄。 輸入下列命令以安裝 **brew**、**FreeTDS** 及 **TinyTDS**。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
開啟您的終端機，然後瀏覽至您打算在其中建立 Ruby 指令碼的目錄。 輸入下列命令以安裝 **FreeTDS** 和 **TinyTDS**。

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="sql-server-connection-information"></a>SQL Server 連線資訊

取得連線到 Azure SQL Database 所需的連線資訊。 您在下一個程序中需要完整的伺服器名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [概觀] 頁面上，檢閱如下圖所示的完整伺服器名稱。 您可將滑鼠移至伺服器名稱上，以帶出 [按一下以複製] 選項。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果您忘記伺服器登入資訊，請瀏覽至 [SQL Database 伺服器] 頁面來檢視伺服器系統管理員名稱，並視需要重設密碼。
    

## <a name="select-data"></a>選取資料
使用下列程式碼，以藉由使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式與 [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL 陳述式來依照類別查詢前 20 項產品。 TinyTDS::Client 函式會接受查詢並傳回結果集。 結果集可使用 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds)重複列舉。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>插入資料
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式搭配 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 陳述式，以使用下列程式碼將新產品插入至 SalesLT.Product 資料表中。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

本範例展示如何安全地執行 INSERT 陳述式、傳遞可保護您應用程式來防禦 [SQL 插入](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx)弱點的參數，以及擷取自動產生的[主索引鍵](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints)值。    
  
若要搭配使用 TinyTDS 和 Azure，建議您執行多個 `SET` 陳述式來變更目前工作階段處理特定資訊的方式。 建議使用程式碼範例中所提供的 `SET` 陳述式。 例如，即使未明確陳述資料行的可為 null 狀態， `SET ANSI_NULL_DFLT_ON` 也可讓新建立的資料行允許 null 值。  
  
為了與 Microsoft SQL Server [日期時間](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql)格式一致，請使用 [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 函式來轉換成對應的日期時間格式。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>更新資料
使用下列程式碼，藉由使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式與 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 陳述式來更新您先前新增的產品。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>刪除資料
使用下列程式碼，藉由使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式與 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 陳述式來刪除您先前新增的產品。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>後續步驟
- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)
- [適用於 TinyTDS 的 GitHub 存放庫](https://github.com/rails-sqlserver/tiny_tds)
- [回報問題/發問](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby Drivers for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)


