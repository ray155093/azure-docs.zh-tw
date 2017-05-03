---
title: "使用 Python 來連接到 Azure SQL Database | Microsoft Docs"
description: "提供可用來連線及查詢 Azure SQL Database 的 Python 程式碼範例。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/17/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f055f1eb2c6f1c0fa9f032f033929299e224de2e
ms.lasthandoff: 04/21/2017


---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL Database︰使用 Python 來連接及查詢資料

 此快速入門示範如何使用 [Python](https://python.org) 來連線至 Azure SQL Database，然後從 Mac OS、Ubuntu Linux 和 Windows 平台使用 Transact-SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)

## <a name="install-the-python-and-database-communication-libraries"></a>安裝 Python 與資料庫通訊程式庫

本節中的步驟假設您已熟悉使用 Python 進行開發且不熟悉 Azure SQL Database。 如果您不熟悉使用 Python 進行開發，請前往[使用 SQL Server 建置應用程式](https://www.microsoft.com/en-us/sql-server/developer-get-started/)並選取 **Python**，然後選取您的作業系統。

### <a name="mac-os"></a>**Mac OS**
開啟您的終端機，並巡覽至您打算用來建立 Python 指令碼的目錄。 輸入下列命令以安裝 **brew**、**Microsoft ODBC Driver for Mac**和 **pyodbc**。 pyodbc 會使用 Linux 上的 Microsoft ODBC Driver 來連線到 SQL Database。

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
開啟您的終端機，並巡覽至您打算用來建立 Python 指令碼的目錄。 輸入下列命令以安裝 **Microsoft ODBC Driver for Linux** 和 **pyodbc**。 pyodbc 會使用 Linux 上的 Microsoft ODBC Driver 來連線到 SQL Database。

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
安裝 [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339) (若出現提示請升級驅動程式)。 Pyodbc 會使用 Linux 上的 Microsoft ODBC Driver 連線至 SQL Database。 

然後會使用 pip 安裝 **pyodbc**。

```cmd
pip install pyodbc==3.1.1
```

您可以在[這裡](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)找到如何啟用以使用 pip 的指示

## <a name="get-connection-information"></a>取得連線資訊

取得連線到 Azure SQL Database 所需的連線資訊。 您在下一個程序中需要完整的伺服器名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [概觀] 頁面上，檢閱如下圖所示的完整伺服器名稱。 您可將滑鼠移至伺服器名稱上，以帶出 [按一下以複製] 選項。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果您忘記 Azure SQL Database 伺服器的登入資訊，請瀏覽至 [SQL Database 伺服器] 頁面來檢視伺服器系統管理員名稱，並視需要重設密碼。     
   
## <a name="select-data"></a>選取資料

使用下列程式碼，以藉由使用 [pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) 函式與 [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL 陳述式來依照類別查詢前 20 項產品。 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函式會用來擷取對 SQL Database 查詢的結果集。 此函式會接受查詢並傳回結果集，而您可以使用 [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html) 反覆查詢結果集。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>插入資料
使用下列程式碼，藉由使用 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函式與 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 陳述式將新產品插入 SalesLT.Product 資料表中。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>更新資料
使用下列程式碼，藉由使用 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函式與 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 陳述式來更新您先前新增的產品。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>刪除資料
使用下列程式碼，藉由使用 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函式與 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 陳述式來刪除您先前新增的產品。 將 server、database、username 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>後續步驟

- 深入了解 [Microsoft Python Driver for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)。
- 瀏覽 [Azure Python 開發人員中心](/develop/python/)。
- 若要使用 SQL Server Management Studio 來連線和查詢，請參閱[使用 SSMS 連線及查詢](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio 進行連線和查詢，請參閱[使用 Visual Studio Code 進行連線和查詢](sql-database-connect-query-vscode.md)。
- 若要使用 .NET 進行連線和查詢，請參閱[使用 .NET 進行連線和查詢](sql-database-connect-query-dotnet.md)。
- 若要使用 PHP 進行連線和查詢，請參閱[使用 PHP 進行連線和查詢](sql-database-connect-query-php.md)。
- 若要使用 Node.js 進行連線和查詢，請參閱[使用 Node.js 進行連線和查詢](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 進行連線和查詢，請參閱[使用 Java 進行連線和查詢](sql-database-connect-query-java.md)。
- 若要使用 Ruby 進行連線和查詢，請參閱[使用 Ruby 進行連線和查詢](sql-database-connect-query-ruby.md)。

