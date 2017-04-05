---
title: "使用 Python 來連接到 Azure SQL Database | Microsoft Docs"
description: "提供可用來連接到 Azure SQL Database 的 Python 程式碼範例。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 03/27/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 91e1dcd5b4a7dc62a09c9deb26622dacba1dcaa1
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL Database︰使用 Python 來連接及查詢資料

使用 [Python](https://python.org) 來連接及查詢 Azure SQL Database。 本指南詳細說明如何使用 Python 來連接到 Azure SQL Database，然後執行查詢、插入、更新和刪除陳述式。

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>設定開發環境
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
安裝 [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339)。 pyodbc 會使用 Linux 上的 Microsoft ODBC Driver 來連線到 SQL Database。 

然後使用 pip 來安裝 pyodbc

```cmd
pip install pyodbc==3.1.1
```

您可以在[這裡](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)找到如何啟用以使用 pip 的指示

## <a name="get-connection-information"></a>取得連線資訊

在 Azure 入口網站中取得連接字串。 您可使用連接字串連線到 Azure SQL Database。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [基本資訊] 窗格中，檢閱完整的伺服器名稱。 

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="connection strings" style="width: 780px;" />
   
## <a name="select-data"></a>選取資料
使用 [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) 函式搭配 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 陳述式，以查詢 Azure SQL Database 中的資料。 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函式可用來擷取對 SQL Database 查詢的結果集。 這個函式基本上會接受任何查詢並傳回結果集，您可以使用 [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html)反覆查詢結果集。

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
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
在 SQL Database 中，[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。 

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>更新資料
[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函式可用來執行 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 陳述式，以更新 Azure SQL Database 中的資料。

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```


## <a name="delete-data"></a>刪除資料
[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函式可用來執行 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 陳述式，以刪除 Azure SQL Database 中的資料。

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>後續步驟
* 檢閱 [SQL Database 開發概觀](sql-database-develop-overview.md)。
* 深入了解 [Microsoft Python Driver for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)。
* 瀏覽 [Azure Python 開發人員中心](/develop/python/)。
* 瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)。

