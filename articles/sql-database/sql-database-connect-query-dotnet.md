---
title: "使用 .NET (C#) 連線到 Azure SQL Database | Microsoft Docs"
description: "提供可用來連線及查詢 Azure SQL Database 的 .NET 程式碼範例"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: c3949447d40fe7e72c6490827fae4bdab6db48be
ms.contentlocale: zh-tw
ms.lasthandoff: 06/22/2017


---
# Azure SQL Database︰使用 .NET (C#) 連接及查詢資料
<a id="azure-sql-database-use-net-c-to-connect-and-query-data" class="xliff"></a>

此快速入門示範如何使用 [C# 和 ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx) 來連線至 Azure SQL Database，然後從 Windows、Mac OS 和 Ubuntu Linux 平台使用 Transact-SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。

> [!TIP]
> 如需使用 C# 建立及查詢新資料庫的教學課程，請參閱[使用 C# 設計第一個資料庫](sql-database-design-first-database-csharp.md)。
>

## 必要條件
<a id="prerequisites" class="xliff"></a>

本快速入門可做為在其中一個快速入門中建立之資源的起點︰

- [建立 DB - 入口網站](sql-database-get-started-portal.md)
- [建立 DB - CLI](sql-database-get-started-cli.md)
- [建立 DB - PowerShell](sql-database-get-started-powershell.md)

## 安裝 .NET
<a id="install-net" class="xliff"></a>

本節中的步驟假設您已熟悉使用 .NET 進行開發，且不熟悉 Azure SQL Database。 如果您不熟悉使用 .NET 進行開發，請前往[使用 SQL Server 建置應用程式](https://www.microsoft.com/sql-server/developer-get-started/)並選取 **C#**，然後選取您的作業系統。

### **Windows .NET Framework 和 .NET Core**
<a id="windows-net-framework-and-net-core" class="xliff"></a>

Visual Studio 2017 Community 是功能完整且可擴充的免費 IDE，用以建立適用於 Android、iOS、Windows 以及 Web 和資料庫應用程式和雲端服務的新式應用程式。 您可以安裝完整的 .NET Framework 或只安裝 .NET Core。 快速入門中的程式碼片段均可搭配使用。 如果您已在電腦上安裝 Visual Studio，請略過後續幾個步驟。

1. 下載 [Visual Studio 2017 安裝程式](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)。 
2. 執行安裝程式並依照安裝提示來完成安裝。

### **Mac OS**
<a id="mac-os" class="xliff"></a>
開啟您的終端機，然後瀏覽至您打算在其中建立 .NET Core 專案的目錄。 輸入下列命令以安裝 **brew**、**OpenSSL** 和 **.NET Core**。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

在 macOS 上安裝 .NET Core。 下載[正式安裝程式](https://go.microsoft.com/fwlink/?linkid=843444)。 此安裝程式會安裝一些工具，並將這些工具放在您的 PATH，以便您從主控台執行 dotnet

### **Linux (Ubuntu)**
<a id="linux-ubuntu" class="xliff"></a>
開啟您的終端機，然後瀏覽至您打算在其中建立 .NET Core 專案的目錄。 輸入下列命令以安裝 **.NET Core**。

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.1
```

## 取得連線資訊
<a id="get-connection-information" class="xliff"></a>

取得連線到 Azure SQL Database 所需的連線資訊。 您在下一個程序中需要完整的伺服器名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [概觀] 頁面上，檢閱如下圖所示的完整伺服器名稱。 您可將滑鼠移至伺服器名稱上，以帶出 [按一下以複製] 選項。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果您忘記 Azure SQL Database 伺服器登入資訊，請瀏覽至 [SQL Database 伺服器] 頁面來檢視伺服器系統管理員名稱，並視需要重設密碼。

5. 按一下 [顯示資料庫連接字串]。

6. 檢閱完整的 **ADO.NET** 連接字串。

    ![ADO.NET 連接字串](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)
  
## 新增 System.Data.SqlClient
<a id="add-systemdatasqlclient" class="xliff"></a>
使用 .NET Core 時，將 System.Data.SqlClient 新增至專案的 ***csproj*** 檔案作為相依項目。

```xml
<ItemGroup>
    <PackageReference Include="System.Data.SqlClient" Version="4.3.0" />
</ItemGroup>
```

## 選取資料
<a id="select-data" class="xliff"></a>

1. 在開發環境中，開啟空白的程式碼檔案。
2. 將 ```using System.Data.SqlClient``` 新增至您的程式碼檔案 ([System.Data.SqlClient 命名空間](https://msdn.microsoft.com/library/system.data.sqlclient.aspx))。 

3. 使用下列程式碼，可藉由使用 [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) 命令搭配 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 陳述式來依照類別查詢前 20 項產品。 為您的伺服器、資料庫、使用者和密碼新增適當的值。

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## 插入資料
<a id="insert-data" class="xliff"></a>

使用下列程式碼，藉由使用 [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) 搭配 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 陳述式將新產品插入至 SalesLT.Product 資料表。 為您的伺服器、資料庫、使用者和密碼新增適當的值。

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nInsert data example:");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("INSERT INTO [SalesLT].[Product] ([Name], [ProductNumber], [Color], [StandardCost], [ListPrice], [SellStartDate]) ");
                    sb.Append("VALUES (@Name, @ProductNumber, @Color, @StandardCost, @ListPrice, @SellStartDate);");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ProductNumber", "200989");
                        command.Parameters.AddWithValue("@Color", "Blue");
                        command.Parameters.AddWithValue("@StandardCost", 75);
                        command.Parameters.AddWithValue("@ListPrice", 80);
                        command.Parameters.AddWithValue("@SellStartDate", "7/1/2016");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) inserted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## 更新資料
<a id="update-data" class="xliff"></a>

使用下列程式碼，藉由使用 [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) 搭配 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 陳述式更新您先前新增的產品。 為您的伺服器、資料庫、使用者和密碼新增適當的值。

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nUpdate data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("UPDATE [SalesLT].[Product] SET ListPrice = @ListPrice WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ListPrice", 500);
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) updated");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## 刪除資料
<a id="delete-data" class="xliff"></a>

使用下列程式碼，藉由使用 [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) 搭配 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 陳述式刪除您先前新增的產品。 為您的伺服器、資料庫、使用者和密碼新增適當的值。

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nDelete data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("DELETE FROM SalesLT.Product WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) deleted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## 後續步驟
<a id="next-steps" class="xliff"></a>
- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)
- [.NET 文件](https://docs.microsoft.com/dotnet/)。
- [使用 SSMS 連接及查詢](sql-database-connect-query-ssms.md)
- [使用 Visual Studio Code 連線及查詢](sql-database-connect-query-vscode.md)。


