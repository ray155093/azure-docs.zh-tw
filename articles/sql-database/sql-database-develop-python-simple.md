---
title: 使用 Python 連接到 SQL Database | Microsoft Docs
description: 提供可用來連接到 Azure SQL Database 的 Python 程式碼範例。
services: sql-database
documentationcenter: ''
author: meet-bhagdev
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/05/2016
ms.author: meetb

---
# <a name="connect-to-sql-database-by-using-python"></a>使用 Python 連接到 SQL Database
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題說明如何使用 Python 來連接及查詢 Azure SQL Database。 您可以從 Windows、Ubuntu Linux 或 Mac 平台執行這個範例。

## <a name="step-1:-create-a-sql-database"></a>步驟 1：建立 SQL Database
請參閱 [快速入門頁面](sql-database-get-started.md) ，以了解如何建立範例資料庫。  請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 以下所示的範例僅適用於 **AdventureWorks 結構描述**。 在您建立資料庫之後，請確定您有依照[開始頁面](sql-database-get-started.md)中的描述，啟用防火牆規則來啟用對 IP 位址的存取

## <a name="step-2:-configure-development-environment"></a>步驟 2︰設定開發環境
### <a name="**mac-os**"></a>**Mac OS**
### <a name="install-the-required-modules"></a>安裝必要的模組
開啟您的終端機並安裝

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="**linux-(ubuntu)**"></a>**Linux (Ubuntu)**
開啟您的終端機，並巡覽至您打算用來建立 Python 指令碼的目錄。 輸入下列命令以安裝 **FreeTDS** 和 **pymssql**。 pymssql 是使用 FreeTDS 來連線 SQL Databases。

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1

### <a name="**windows**"></a>**Windows**
從 [**這裡**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql)安裝 pymssql。 

確定您選擇正確的 whl 檔案。 例如：如果您在 64 位元電腦上使用 Python 2.7，請選擇：pymssql‑2.1.1‑cp27‑none‑win_amd64.whl。 在您下載 .whl 檔案之後，請將它放在 C:/Python27 資料夾中。

現在請從命令列使用 pip 安裝 pymssql 驅動程式。 cd 至 C:/Python27 中，並執行以下

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

您可以在[這裡](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)找到如何啟用以使用 pip 的指示

## <a name="step-3:-run-sample-code"></a>步驟 3︰執行範例程式碼
建立名為 **sql_sample.py** 的檔案並將以下程式碼張貼在該檔案內。 您可以從命令列執行此作業，方法是使用：

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>連接到您的 SQL Database
[pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 函式可用來連接到 SQL Database。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>執行 SQL SELECT 陳述式
[cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 函式可用來擷取對 SQL Database 查詢的結果集。 這個函式基本上會接受任何查詢並傳回結果集，您可以使用 [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone)反覆查詢結果集。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row,-pass-parameters,-and-retrieve-the-generated-primary-key"></a>插入資料列、傳遞參數及擷取產生的主索引鍵
在 SQL Database 中，[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。 

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
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

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
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

<!--HONumber=Oct16_HO2-->


