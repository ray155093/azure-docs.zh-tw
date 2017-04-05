---
title: "使用 Java 來連接到 Azure SQL Database | Microsoft Docs"
description: "提供可用來連接到 Azure SQL Database 的 Java 程式碼範例。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/27/2017
ms.author: andrela;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: a047d4cdf869fff0d2afaf11f124370c0eae98e4
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-java-to-connect-and-query-data"></a>Azure SQL Database︰使用 Java 來連接及查詢資料

使用 [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 來連接及查詢 Azure SQL Database。 本指南詳細說明如何使用 Java 來連接到 Azure SQL Database，然後執行查詢、插入、更新和刪除陳述式。

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>設定開發環境

下列各節詳細說明如何設定您的現有 Mac OS、Linux(Ubuntu) 及 Windows 開發環境，以與 Azure SQL Database 搭配運作。

### <a name="mac-os"></a>**Mac OS**
開啟您的終端機，然後瀏覽至您打算在其中建立 Java 專案的目錄。 輸入下列命令以安裝 **brew** 和 **Maven**。 

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
開啟您的終端機，然後瀏覽至您打算在其中建立 Java 專案的目錄。 輸入下列命令以安裝 **Maven**。 

```
sudo apt-get install maven
```

### <a name="windows"></a>**Windows**
使用官方安裝程式來安裝 [Maven](https://maven.apache.org/download.cgi)。  

## <a name="get-connection-information"></a>取得連線資訊

在 Azure 入口網站中取得連接字串。 您可使用連接字串連線到 Azure SQL Database。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [基本資訊] 窗格中，檢閱完整的伺服器名稱。 

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="server name" style="width: 780px;" />

4. 按一下 [顯示資料庫連接字串]。

5. 檢閱完整的 **JDBC** 連接字串。

    <img src="./media/sql-database-connect-query-jdbc/jdbc-connection-string.png" alt="JDBC connection string" style="width: 780px;" />

### <a name="create-maven-project"></a>**建立 Maven 專案**
從終端機，建立新的 Maven 專案。 
```
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

在 ***pom.xml*** 中將 **Microsoft JDBC Driver for SQL Server** 新增到相依性。 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
</dependency>
```

## <a name="select-data"></a>選取資料

使用[連線](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection)搭配 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 陳述式，以使用 Java 來查詢 Azure SQL Database 中的資料。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.Statement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql);

                // Print results from select statement
                System.out.println("Top 20 categories:");
                while (resultSet.next())
                {
                    System.out.println(resultSet.getString(1) + " "
                        + resultSet.getString(2));
                }
            }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="insert-data"></a>插入資料

使用[準備陳述式](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)搭配 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 陳述式，以將資料插入到 Azure SQL Database 中。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, )" 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                PreparedStatement prep = connection.prepareStatement(insertSql);
                prep.setString(1, "BrandNewProduct");
                prep.setInt(2, 200989);
                prep.setString(3, "Blue");
                prep.setDouble(4, 75);
                prep.setDouble(5, 80);
                prep.setTimestamp(6, sqlTimeStamp);

                int count = prep.executeUpdate();
                System.out.println("Inserted: " + count + " row(s)");
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## <a name="update-data"></a>更新資料

使用[準備陳述式](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)搭配 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 陳述式，以更新 Azure SQL Database 中的資料。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                PreparedStatement prep = connection.prepareStatement(updateSql);
                prep.setString(1, "500");
                prep.setString(2, "BrandNewProduct");

                int count = prep.executeUpdate();
                System.out.println("Updated: " + count + " row(s)")
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## <a name="delete-data"></a>刪除資料

使用[準備陳述式](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)搭配 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 陳述式，以刪除 Azure SQL Database 中的資料。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                PreparedStatement prep = connection.prepareStatement(deleteSql);
                prep.setString(1, "BrandNewProduct");

                int count = prep.executeUpdate();
                System.out.println("Deleted: " + count + " row(s)");
            }        
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
* 檢閱 [SQL Database 開發概觀](sql-database-develop-overview.md)。
* [Microsoft JDBC Driver for SQL Server](https://github.com/microsoft/mssql-jdbc) 的 Github 存放庫。
* [檔案問題/發問](https://github.com/microsoft/mssql-jdbc/issues)。
* 瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)。


