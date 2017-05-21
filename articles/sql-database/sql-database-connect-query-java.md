---
title: "使用 Java 來連接到 Azure SQL Database | Microsoft Docs"
description: "提供可用來連線及查詢 Azure SQL Database 的 Java 程式碼範例。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/07/2017
ms.author: andrela;carlrab;sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: defb32835971fe423e77a11817fce057fa7c929d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017


---
# <a name="azure-sql-database-use-java-to-connect-and-query-data"></a>Azure SQL Database︰使用 Java 來連接及查詢資料

此快速入門示範如何使用 [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 來連線至 Azure SQL Database，然後從 Mac OS、Ubuntu Linux 和 Windows 平台使用 Transact-SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)

## <a name="install-java-software"></a>安裝 Java 軟體

本節中的步驟假設您已熟悉使用 Java 進行開發且不熟悉 Azure SQL Database。 如果您不熟悉使用 Java 進行開發，請前往[使用 SQL Server 建置應用程式](https://www.microsoft.com/en-us/sql-server/developer-get-started/)並選取 **Java**，然後選取您的作業系統。

### <a name="mac-os"></a>**Mac OS**
開啟您的終端機，然後瀏覽至您打算在其中建立 Java 專案的目錄。 輸入下列命令來安裝 **brew** 和 **Maven**： 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
開啟您的終端機，然後瀏覽至您打算在其中建立 Java 專案的目錄。 輸入下列命令來安裝 **Maven**：

```bash
sudo apt-get install maven
```

### <a name="windows"></a>**Windows**
使用官方安裝程式來安裝 [Maven](https://maven.apache.org/download.cgi)。 請使用 Maven 來協助管理相依性、建置、測試及執行您的 Java 專案。 

## <a name="get-connection-information"></a>取得連線資訊

取得連線到 Azure SQL Database 所需的連線資訊。 您在下一個程序中需要完整的伺服器名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [概觀] 頁面上，檢閱如下圖所示的完整伺服器名稱。 您可將滑鼠移至伺服器名稱上，以帶出 [按一下以複製] 選項。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果您忘記伺服器登入資訊，請瀏覽至 [SQL Database 伺服器] 頁面來檢視伺服器系統管理員名稱，並視需要重設密碼。
5. 按一下 [顯示資料庫連接字串]。

6. 檢閱完整的 **JDBC** 連接字串。

    ![JDBC 連接字串](./media/sql-database-connect-query-jdbc/jdbc-connection-string.png)    

### <a name="create-maven-project"></a>**建立 Maven 專案**
從終端機，建立新的 Maven 專案。 
```bash
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

使用下列程式碼，以藉由使用[連線](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection)類別搭配 [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL 陳述式來依照類別查詢前 20 項產品。 將 hostHame、dbName、user 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 

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
        String hostName = "your_server.database.windows.net";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
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
                
                try (Statement statement = connection.createStatement();
                    ResultSet resultSet = statement.executeQuery(selectSql)) {

                        // Print results from select statement
                        System.out.println("Top 20 categories:");
                        while (resultSet.next())
                        {
                            System.out.println(resultSet.getString(1) + " "
                                + resultSet.getString(2));
                        }
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="insert-data"></a>插入資料

使用[準備陳述式](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)類別搭配 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 陳述式，以使用下列程式碼將新產品插入至 SalesLT.Product 資料表。 將 hostHame、dbName、user 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server.database.windows.net";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, " 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                try (PreparedStatement prep = connection.prepareStatement(insertSql)) {
                        prep.setString(1, "BrandNewProduct");
                        prep.setInt(2, 200989);
                        prep.setString(3, "Blue");
                        prep.setDouble(4, 75);
                        prep.setDouble(5, 80);
                        prep.setTimestamp(6, sqlTimeStamp);

                        int count = prep.executeUpdate();
                        System.out.println("Inserted: " + count + " row(s)");
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## <a name="update-data"></a>更新資料

使用[準備陳述式](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)類別搭配 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 陳述式來更新 Azure SQL Database 中的資料，以使用下列程式碼更新先前新增的產品。 將 hostHame、dbName、user 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server.database.windows.net";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
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

                try (PreparedStatement prep = connection.prepareStatement(updateSql)) {
                        prep.setString(1, "500");
                        prep.setString(2, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Updated: " + count + " row(s)")
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## <a name="delete-data"></a>刪除資料

使用下列程式碼，藉由使用[準備陳述式](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)搭配 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 陳述式來刪除您先前新增的產品。 將 hostHame、dbName、user 和 password 參數，取代為您使用 AdventureWorksLT 範例資料建立資料庫時所指定的值。 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server.database.windows.net";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
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

                try (PreparedStatement prep = connection.prepareStatement(deleteSql)) {
                        prep.setString(1, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Deleted: " + count + " row(s)");
                }
        }        
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft JDBC Driver for SQL Server](https://github.com/microsoft/mssql-jdbc)
- [回報問題/發問](https://github.com/microsoft/mssql-jdbc/issues)


