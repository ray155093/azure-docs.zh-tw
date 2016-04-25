<properties
	pageTitle="在 Windows 上搭配 JDBC 使用 Java 連接到 SQL Database"
	description="提供可用來連接到 Azure SQL Database 的 Java 程式碼範例。這個範例使用在 Windows 用戶端電腦上執行的 JDBC。"
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="andrela"/>


# 在 Windows 上搭配 JDBC 使用 Java 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供可用來連接到 Azure SQL Database 的 Java 程式碼範例。Java 範例需要 Java Development Kit (JDK) 1.8 版。這個範例使用 JDBC 驅動程式連接到 Azure SQL Database。

## 步驟 1︰設定開發環境

安裝驅動程式和程式庫：

- [Microsoft JDBC 4.2 Driver for SQL Server](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774)。
- 執行 [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 的任何作業系統平台。

## 步驟 2：建立 SQL Database

請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立範例資料庫。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。

## 步驟 3：取得連接字串

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] 如果您使用 JTDS JDBC 驅動程式，則您必須在連接字串的 URL 加入 "ssl=require"，然後您需要設定 JVM 的下列選項 "-Djsse.enableCBCProtection=false"。此 JVM 選項會停用安全性漏洞修正程式，因此請確定您了解會涉及到哪些風險，才能設定此選項。

## 步驟 4︰連接

使用連接類別連接到 SQL Database。

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## 步驟 5：執行查詢
在此範例中，連接到 Azure SQL Database、執行 SELECT 陳述式，並傳回選取的資料列。

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute a SELECT SQL statement.
					String selectSql = "SELECT TOP 10 Title, FirstName, LastName from SalesLT.Customer";
					statement = connection.createStatement();
					resultSet = statement.executeQuery(selectSql);
	
					// Print results from select statement
					while (resultSet.next()) 
					{
						System.out.println(resultSet.getString(2) + " "
							+ resultSet.getString(3));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## 步驟 6：插入資料列
在此範例中，執行 INSERT 陳述式、傳遞參數，並且擷取自動產生的主索引鍵值。

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
				PreparedStatement prepsInsertProduct = null;
				
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute an INSERT SQL prepared statement.
					String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES "
						+ "('NewBike', 'BikeNew', 'Blue', 50, 120, '2016-01-01');";
	
					prepsInsertProduct = connection.prepareStatement(
						insertSql,
						Statement.RETURN_GENERATED_KEYS);
					prepsInsertProduct.execute();
					
					// Retrieve the generated key from the insert.
					resultSet = prepsInsertProduct.getGeneratedKeys();
					
					// Print the ID of the inserted row.
					while (resultSet.next()) {
						System.out.println("Generated: " + resultSet.getString(1));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (prepsInsertProduct != null) try { prepsInsertProduct.close(); } catch(Exception e) {}
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}


## 後續步驟

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

<!---HONumber=AcomDC_0413_2016-->