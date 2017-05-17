---
title: "如何將應用程式連線至適用於 MySQL 的 Azure 資料庫 | Microsoft Docs"
description: "本文件列出目前支援讓應用程式與「適用於 MySQL 的 Azure 資料庫」連線的所有連接字串"
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>如何將應用程式連線至適用於 MySQL 的 Azure 資料庫
本文件列出「適用於 MySQL 的 Azure 資料庫」支援的所有連接字串類型，以及範本和範例。 在連接字串中，您可以使用不同的參數和不同的設定。

- 請參閱[如何設定 SSL](./howto-configure-ssl.md) 文件以取得憑證
- {your_host} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

例如，假設伺服器名稱是 **wpdemo**、資料庫名稱是 **wpdb**、使用者名稱是 **WPAdmin**、密碼是 **orcas!2**。

所以，連接字串應該為：

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>從入口網站取得連接字串
在 [Azure 入口網站](https://portal.azure.com)中，移至適用於 MySQL 的 Azure 資料庫，然後按一下 [連接字串]，取得您的執行個體可用的字串清單︰![入口網站上的連接字串](./media/howto-connection-strings/connection-strings-on-portal.png)

此字串會提供詳細資料，例如驅動程式、伺服器和其他資料庫連線參數。 依需要複製連接字串，然後修改為您自己的參數，例如資料庫名稱、密碼等。接著，您就可以使用此字串來連線至伺服器。

## <a name="next-steps"></a>後續步驟
- 如需有關連線程式庫的詳細資訊，請參閱[概念 - 連線庫](./concepts-connection-libraries.md)

