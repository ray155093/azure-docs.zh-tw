<properties
   pageTitle="適用於 SQL 資料倉儲的驅動程式 | Microsoft Azure"
   description="適用於 SQL 資料倉儲的連接字串和驅動程式"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>


# 適用於 Azure SQL 資料倉儲的驅動程式

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-connect-overview.md)
- [驗證](sql-data-warehouse-authentication.md)
- [驅動程式](sql-data-warehouse-connection-strings.md)


您可以使用下列任一個應用程式通訊協定連線到 SQL 資料倉儲：

- ADO.NET
- ODBC
- PHP
- JDBC 

以下是每個通訊協定連接字串的一些範例。您也可以使用 Azure 入口網站來協助您設定連接字串。只要瀏覽至您在 Azure 入口網站上的資料庫。按一下 [程式集] 下的 [顯示資料庫連接字串]。

## 範例 ADO.NET 連接字串

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## 範例 ODBC 連接字串

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## 範例 PHP 連接字串

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## 範例 JDBC 連接字串

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## 後續步驟

若要透過 Visual Studio 和其他應用程式開始查詢您的資料倉儲，請參閱 [透過 Visual Studio 查詢][]。

<!--Image references-->

<!--Azure.com references-->


<!--MSDN references-->

<!--Other references-->

<!---HONumber=AcomDC_0622_2016-->