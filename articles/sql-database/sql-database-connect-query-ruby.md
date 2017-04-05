---
title: "使用 Ruby 來連接到 Azure SQL Database | Microsoft Docs"
description: "您可以使用這個快速入門中的範例程式碼，以 Ruby 建置現代化的應用程式，並受到具有 Azure SQL Database 之雲端中強大的關聯式資料庫支援。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 03/28/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 8e01a717cbef6b5f6d26a8191c44e249bf7a9567
ms.lasthandoff: 03/30/2017


---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Azure SQL Database︰使用 Ruby 來連接及查詢資料

使用 [Ruby](https://Ruby.org) 來連接及查詢 Azure SQL Database。 這個快速入門詳細說明如何使用 Ruby 來連接到 Azure SQL Database，然後執行查詢、插入、更新和刪除陳述式。

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>設定開發環境

下列各節詳細說明如何設定您的現有 Mac OS 和 Linux(Ubuntu) 開發環境，以與 Azure SQL Database 搭配運作。

### <a name="mac-os"></a>**Mac OS**
開啟您的終端機，然後瀏覽至您打算在其中建立 Ruby 指令碼的目錄。 輸入下列命令以安裝 **brew**、**FreeTDS** 及 **TinyTDS**。

```ruby
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
開啟您的終端機，然後瀏覽至您打算在其中建立 Ruby 指令碼的目錄。 輸入下列命令以安裝 **FreeTDS** 和 **TinyTDS**。

```ruby
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>取得連線資訊

在 Azure 入口網站中取得連接字串。 您可使用連接字串連線到 Azure SQL Database。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [基本資訊] 窗格中，檢閱完整的伺服器名稱。

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="connection strings" style="width: 780px;" />
    

## <a name="select-data"></a>選取資料
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式搭配 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 陳述式，以查詢 Azure SQL Database 中的資料。 TinyTDS::Client 函式會接受查詢並傳回結果集。 結果集可使用 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds)重複列舉。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
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
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式搭配 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 陳述式，以將資料插入到 Azure SQL Database 中。

在這個範例中，您將了解如何安全地執行 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) 陳述式、傳遞可保護您應用程式來防禦 [SQL 插入](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx)弱點的參數，以及擷取自動產生的[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。    
  
若要搭配使用 TinyTDS 和 Azure，建議您執行多個 `SET` 陳述式來變更目前工作階段處理特定資訊的方式。 建議使用程式碼範例中所提供的 `SET` 陳述式。 例如，即使未明確陳述資料行的可為 null 狀態， `SET ANSI_NULL_DFLT_ON` 也可讓新建立的資料行允許 null 值。  
  
為了與 Microsoft SQL Server [日期時間](http://msdn.microsoft.com/library/ms187819.aspx)格式一致，請使用 [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 函式來轉換成對應的日期時間格式。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON"")
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
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式搭配 [UPDATE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 陳述式，以更新 Azure SQL Database 中的資料。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
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
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函式搭配 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 陳述式，以刪除 Azure SQL Database 中的資料。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
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
* 檢閱 [SQL Database 開發概觀](sql-database-develop-overview.md)。
* 深入了解[適用於 SQL Server 的 Ruby 驅動程式 (英文)](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)。
* 瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)。

