---
title: "使用 Python 讀取適用於 PostgreSQL 的 Azure 資料庫 | Microsoft Docs"
description: "假設讀者不具相關背景知識，說明如何執行 Python 程式碼範例，針對適用於 PostgreSQL 的 Azure 資料庫，在資料表中寫入和讀取資料。"
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>使用 Python 讀取適用於 PostgreSQL 的 Azure 資料庫


本文提供簡短的 Python 程式，使用 SQL SELECT 陳述式從資料表讀取一列資料列。 本文也說明如何尋找和安裝執行 Python 程式所需的所有先決條件。

本文的範例 Python 程式和所述的 Python 相關工具，都同樣適用於各種平台，包括 Linux、Mac 和 Windows。 


## <a name="install-the-python-interpreter"></a>安裝 Python 解譯器


本文中的 Python 程式碼範例是針對 Python 解譯器 2.7 版所撰寫，因此無法以 3.x 版來執行。

從下列位置下載並安裝 Python 解譯器 2.7 版：

- [從 python.org 下載 Python 解譯器 (英文)](https://www.python.org/downloads/)

安裝之後，確認您可以在命令列上找到並執行解譯器。 請使用如下的命令：

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>安裝 pip.exe (Python 模組安裝程式)


安裝 Python 解譯器時，可能也會在名為 *Scripts/* 的子目錄中安裝 pip.exe。 Pip.exe 會安裝特殊的 Python 模組。 確認您可以找到並執行 pip.exe。

`pip.exe`

如果無法執行 pip.exe，請查看您是否有名稱為 **get-pip.py** 的 Python 公用程式檔。 如果您有 get-pip.py，就可以執行它來取得 pip.exe：

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>安裝 psycopg (連線模組)


本文的 Python 程式需要一個連線模組，以便將程式連線到適用於 PostgreSQL 的 Azure 資料庫伺服器。 這個連線模組的名稱是 **psycopg2**。 如果您需要詳細資料，請參閱：

- [psycopg2 網站 (英文)](http://initd.org/psycopg/)

使用下列 pip.exe 安裝命令來安裝 psycopg2：

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器


如果您尚未有適用於 PostgreSQL 的 Azure 資料庫伺服器，以下文件說明建立伺服器的方法：

- [使用 Azure 入口網站建立適用於 PostgreSQL 的 Azure 資料庫](quickstart-create-server-database-portal.md)
- [使用 Azure CLI 建立適用於 PostgreSQL 的 Azure 資料庫](quickstart-create-server-database-azure-cli.md)


## <a name="obtain-the-connection-string-values"></a>取得連接字串值


您可以從 Azure 入口網站中，針對適用於 PostgreSQL 的 Azure 資料庫伺服器，取得連接字串值。 您需要如下表中所述的參數值。

- *名稱*欄：顯示 psycopg2 需要的參數識別項。
- *-符號*欄：顯示範例 Python 程式 (*PythonDriver.py*) 需要的參數識別項。


| 名稱 | -符號 | 值範例 |
| :--  | :--     | :--           |
| host | -h | myazurepostgresql.database.windows.net |
| user | -U | myalias@myazurepostgresql |
| dbname | -d | postgres<br />(所有的 PostgreSQL 伺服器都有名為 **postgres** 的資料庫)。 |
| port | -p | 5432 (可能是 5432 這個特定值)。 |
| password | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>Python 範例程式


本節提供範例 Python 程式的原始程式碼。 在本文稍後您將會執行此程式。

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>執行範例程式的命令列


本節顯示用來測試 Python 範例程式的命令列，以及實際的確認輸出。

針對稍早描述的參數，您必須使用實際的連接字串值來取代此處顯示的範例值。

執行上述範例 Python 程式的確實語法可能稍有差異。 實際的語法會因作業系統與使用的主控台類型而異。


#### <a name="windows-cmdexe-console"></a>Windows cmd.exe 主控台


下列程式碼區塊顯示範例 Python 程式的實際測試回合。 在此使用簡單的 cmd.exe 命令列。 在每個輸入的 '^' 行接續字元之後：

1. 按下 Enter 鍵。
2. 顯示 **More?** 片語。
3. 輸入整行的另一個部分，以此類推。

此處的行接續技巧是為了避免範例變得太寬，而導致顯示或列印時效果不佳。

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

您會看到 **SUCCESS** 行，確認程式已執行。

另一個替代的方法，是將整個命令列放入 .bat 檔案中。 之後，就可以從 cmd.exe 命令列中執行 .bat 檔案。


#### <a name="powershell-file"></a>PowerShell 檔案


在 PowerShell 主控台中，命令列不支援行接續字元。 因此在這個 PowerShell 小節中，我們將命令放入 PowerShell 檔案中。 之後，就可以從 PowerShell 命令列執行檔案。

將下列程式碼複製到名為 *PythonDriverRun.ps1* 的檔案中。

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

如下所示執行 PythonDriverRun.ps1。 您會看到程式印出 **SUCCESS** 行做為確認。

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

如果您想要直接在 PowerShell 命令列上執行 PythonDriverRun.ps1，則必須在前面加上 '&' 字元和一個空格。 若沒有前置 '&'，則確認訊息會太快地消失，以致於看不到。

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>安裝 pgAdmin 來檢查伺服器


當 PythonDriverRun.ps1 程式結束時，它會卸除程式所建立的 testpy1 資料表以自行清除。 您可以選擇使用 '#'，將發出 **DROP TABLE** 陳述式的原始程式行註解化。 選擇這麼做會保留資料表，如此就可以在之後檢查資料表。

pgAdmin 工具可讓您檢查任何的 PostgreSQL 伺服器，以及伺服器中的物件。 Microsoft SQL Server 或 Azure SQL Database 的使用者，會發現 SQL Server Management Studio (SSMS) 和 pgAdmin 之間有相似之處。

如有需要，您可以安裝 **pgAdmin** 來檢查伺服器和 **testpy1** 資料表。


#### <a name="1-install-pgadmin"></a>1.安裝 pgAdmin


pgAdmin 的安裝指示可在下列位置取得：

- [http://www.pgadmin.org/ (英文)](http://www.pgadmin.org/)

可執行檔的名稱可能是 **pgAdmin4.exe**，而不單純是 pgAdmin.exe。

若要在 Windows 電腦上執行 pgAdmin4.exe，請在命令列中輸入類似如下的命令：

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2.將 pgAdmin 連線到您的伺服器

 
在顯示 pgAdmin UI 之後，找出 [Browser (瀏覽器)] 窗格。 接著以滑鼠右鍵按一下 [Servers (伺服器)] > [Create (建立)] > [Servers (伺服器)]。 此處「Create (建立)」一詞代表建立任何現有 PostgreSQL 伺服器的「連線」，包括任何適用於 PostgreSQL 的 Azure 資料庫伺服器。

建立連線後，在 [Browser (瀏覽器)] 窗格中會顯示物件的樹狀目錄。


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>3.在 pgAdmin 樹狀目錄中瀏覽至您的資料表


若要查看您的資料表，請如下所示將樹狀目錄項目展開：

- [Servers (伺服器)] &gt; *[YourServerHere]* &gt; [Databases (資料庫)] &gt; [postgres] &gt; [Schemas (結構描述)] &gt; [public (公用)] &gt; [tables (資料表)] &gt; [testpy1]

![pgAdmin 在樹狀目錄中顯示 testpy1 資料表](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4.卸除 testpy1 資料表


針對最後的清除，以滑鼠右鍵按一下 [testpy1] 節點，然後按一下 [Delete/Drop (刪除/卸除)]。


## <a name="next-steps"></a>後續步驟

- [使用 Python 連線至 Azure SQL Database](../sql-database/sql-database-connect-query-python.md)
- [「適用於 PostgreSQL 的 Azure 資料庫」的連線庫](concepts-connection-libraries.md)

