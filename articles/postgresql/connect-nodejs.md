---
title: "從 Node.js 連線至 Azure Database for PostgreSQL | Microsoft Docs"
description: "本快速入門提供 Node.js 程式碼範例，供您在從 Azure Database for PostgreSQL 連線及查詢資料時使用。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6a51fcf4f4494e5b32ccf6dabb19f8d004bb20d4
ms.contentlocale: zh-tw
ms.lasthandoff: 06/26/2017

---

<a id="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data" class="xliff"></a>

# Azure Database for PostgreSQL︰使用 Node.js 連線及查詢資料
本快速入門示範如何從 Windows、Ubuntu Linux 和 Mac 平台使用 [Node.js](https://nodejs.org/) 來連線到 Azure Database for PostgreSQL。 它會顯示如何使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。 本文中的步驟假設您已熟悉使用 Node.js 進行開發，但不熟悉 Azure Database for PostgreSQL。

<a id="prerequisites" class="xliff"></a>

## 必要條件
本快速入門使用在以下任一指南中建立的資源作為起點︰
- [建立 DB - 入口網站](quickstart-create-server-database-portal.md)
- [建立 DB - CLI](quickstart-create-server-database-azure-cli.md)

您也需要：
- 安裝 [Node.js](https://nodejs.org)
- 安裝 [pg](https://www.npmjs.com/package/pg) 套件。 

<a id="install-nodejs" class="xliff"></a>

## 安裝 Node.js 
根據您的平台安裝 Node.js：

<a id="mac-os" class="xliff"></a>

### **Mac OS**
輸入下列命令以安裝 **brew**、適用於 Mac OS X 的簡易使用封裝管理員和 **Node.js**。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
輸入下列命令，以安裝 **Node.js** 和適用於 Node.js 的 **npm** 封裝管理員。

```bash
sudo apt-get install -y nodejs npm
```

<a id="windows" class="xliff"></a>

### **Windows**
造訪 [Node.js 下載頁面](https://nodejs.org/en/download/)，並選取您需要的 Windows 安裝程式選項。

<a id="install-pg-client" class="xliff"></a>

## 安裝 pg 用戶端
安裝 [pg](https://www.npmjs.com/package/pg)，這是 node.js 的純 JavaScript 非封鎖用戶端，有助於連線及查詢 PostgreSQL。

若要這樣做，請從命令列執行適用於 JavaScript 的節點套件管理員 (npm) 以安裝 pg 用戶端。
```bash
npm install pg
```

列出已安裝的套件以確認安裝。
```bash
npm list
```
此清單命令輸出可確認每個元件的版本。 
```
`-- pg@6.2.3
  +-- buffer-writer@1.0.1
  +-- packet-reader@0.3.1
etc...
```

<a id="get-connection-information" class="xliff"></a>

## 取得連線資訊
取得連線到 Azure Database for PostgreSQL 所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您剛建立的伺服器 **mypgserver-20170401**。
3. 按一下伺服器名稱 [mypgserver-20170401]。
4. 選取伺服器的 [概觀] 頁面。 記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。
 ![Azure Database for PostgreSQL - 伺服器管理員登入](./media/connect-nodejs/1-connection-string.png)
5. 如果您忘記伺服器登入資訊，請瀏覽至 [概觀] 頁面來檢視伺服器管理員登入名稱，並視需要重設密碼。

<a id="running-the-javascript-code-in-nodejs" class="xliff"></a>

## 在 Node.js 中執行 JavaScript 程式碼
您可藉由輸入 `node` 以從 bash shell 或 Windows 命令提示字元啟動 Node.js，然後複製範例 JavaScript 程式碼並將其貼至提示字元，以互動方式執行。 或者，您可以將 JavaScript 程式碼儲存成文字檔並以檔案名稱作為參數來啟動 `node filename.js`，進而執行它。

<a id="connect-create-table-and-insert-data" class="xliff"></a>

## 連線、建立資料表及插入資料
使用下列程式碼搭配 **CREATE TABLE** 和 **INSERT INTO** SQL 陳述式來連線和載入資料。
[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 物件用來與 PostgreSQL 伺服器連接。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函式用來建立伺服器連線。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函式用來對 PostgreSQL 資料庫執行 SQL 查詢。 

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }
});

function queryDatabase()
{
    client.query(
        ' \
            DROP TABLE IF EXISTS inventory; \
            CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER); \
            INSERT INTO inventory (name, quantity) VALUES (\'banana\', 150); \
            INSERT INTO inventory (name, quantity) VALUES (\'orange\', 154); \
            INSERT INTO inventory (name, quantity) VALUES (\'apple\', 100); \
        ',
        function (err)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;

                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()
        }
    });
}
```

<a id="read-data" class="xliff"></a>

## 讀取資料
使用下列程式碼搭配 **SELECT** SQL 陳述式來連線和讀取資料。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 物件用來與 PostgreSQL 伺服器連接。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函式用來建立伺服器連線。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函式用來對 PostgreSQL 資料庫執行 SQL 查詢。 

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};


const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;

    else
    {
        console.log("Connected to Azure Database for PostgreSQL server:" + config.host);
        queryDatabase();
    }
});

function queryDatabase()
{
    // Declare array to hold query result set
    const results = [];

    console.log("Running query to PostgreSQL server:" + config.host);

    // Perform query
    var query = client.query('SELECT * FROM inventory;');

    // Print result set
    query.on('row', function(row)
    {
        console.log("Read " + JSON.stringify(row));
    });

    // Exit program after execution
    query.on('end', function(row)
    {
        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="update-data" class="xliff"></a>

## 更新資料
使用下列程式碼搭配 **UPDATE** SQL 陳述式來連線和讀取資料。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 物件用來與 PostgreSQL 伺服器連接。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函式用來建立伺服器連線。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函式用來對 PostgreSQL 資料庫執行 SQL 查詢。 

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('UPDATE inventory SET quantity= 1000 WHERE name=\'banana\';', function (err, result)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });             
        }

        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="delete-data" class="xliff"></a>

## 刪除資料
使用下列程式碼搭配 **DELETE** SQL 陳述式來連線和讀取資料。 [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 物件用來與 PostgreSQL 伺服器連接。 [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 函式用來建立伺服器連線。 [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 函式用來對 PostgreSQL 資料庫執行 SQL 查詢。 

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('DELETE FROM inventory WHERE name=\'apple\';', function (err, result)
    {
        console.log("Connection established");
        
        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()          
        }
    }); 
}
```

<a id="next-steps" class="xliff"></a>

## 後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./howto-migrate-using-export-and-import.md)

