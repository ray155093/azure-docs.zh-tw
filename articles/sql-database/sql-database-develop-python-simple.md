---
title: "使用 Python 連接到 SQL Database | Microsoft Docs"
description: "提供可用來連接到 Azure SQL Database 的 Python 程式碼範例。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 01/03/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 631baac839f4045c4b0fcf23810d9459c45a4998
ms.openlocfilehash: 558d6660235a76bc7f5d23e7b28025496c2d8271


---
# <a name="connect-to-sql-database-by-using-python"></a>使用 Python 連接到 SQL Database
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題說明如何使用 Python 來連接及查詢 Azure SQL Database。 您可以從 Windows、Ubuntu Linux 或 Mac 平台執行這個範例。

## <a name="step-1-create-a-sql-database"></a>步驟 1：建立 SQL Database
請參閱 [快速入門頁面](sql-database-get-started.md) ，以了解如何建立範例資料庫。  請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 以下所示的範例僅適用於 **AdventureWorks 結構描述**。 在您建立資料庫之後，請確定您有依照[開始頁面](sql-database-get-started.md)中的描述，啟用防火牆規則來啟用對 IP 位址的存取

## <a name="step-2-configure-development-environment"></a>步驟 2︰設定開發環境
### <a name="mac-os"></a>**Mac OS**
開啟您的終端機，並巡覽至您打算用來建立 Python 指令碼的目錄。 輸入下列命令以安裝 **brew**、**FreeTDS** 及 **pyodbc**。 pyodbc 會使用 MacOS 上的 FreeTDS 來連線到 SQL Database。

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew uninstall FreeTDS #if you have an existing installed FreeTDS
    brew update
    brew doctor
    brew install freetds --with-unixodbc
    sudo pip install pyodbc==3.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
開啟您的終端機，並巡覽至您打算用來建立 Python 指令碼的目錄。 輸入下列命令以安裝 **Microsoft ODBC Driver for Linux** 和 **pyodbc**。 pyodbc 會使用 Linux 上的 Microsoft ODBC Driver 來連線到 SQL Database。

    sudo su
    curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
    curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
    exit
    sudo apt-get update
    sudo apt-get install msodbcsql mssql-tools unixodbc-dev-utf16
    sudo pip install pyodbc==3.1.1

### <a name="windows"></a>**Windows**
安裝 [Microsoft ODBC Driver 13.1](https://www.microsoft.com/en-us/download/details.aspx?id=53339)。 pyodbc 會使用 Linux 上的 Microsoft ODBC Driver 來連線到 SQL Database。 

然後使用 pip 來安裝 pyodbc

    pip install pyodbc==3.1.1

您可以在[這裡](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)找到如何啟用以使用 pip 的指示

## <a name="step-3-run-sample-code"></a>步驟 3︰執行範例程式碼
建立名為 **sql_sample.py** 的檔案並將以下程式碼張貼在該檔案內。 您可以從命令列執行此作業，方法是使用：

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>連接到您的 SQL Database
[pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) 函式可用來連線到 SQL Database。

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux of windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    if row:
        print row

### <a name="execute-an-sql-select-statement"></a>執行 SQL SELECT 陳述式
[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函式可用來擷取對 SQL Database 查詢的結果集。 這個函式基本上會接受任何查詢並傳回結果集，您可以使用 [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html)反覆查詢結果集。

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])     
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>插入資料列、傳遞參數及擷取產生的主索引鍵
在 SQL Database 中，[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。 

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>交易
這個程式碼範例示範如何使用交易，您將：

* 開始交易
* 插入一列資料
* 回復您的交易以復原插入 

在 sql_sample.py 內部貼上以下程式碼。

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>後續步驟
* 檢閱 [SQL Database 開發概觀](sql-database-develop-overview.md)
* 更多有關 [Microsoft Python Driver for SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* 瀏覽 [Azure Python 開發人員中心](/develop/python/)。

## <a name="additional-resources"></a>其他資源
* [多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)



<!--HONumber=Jan17_HO1-->


