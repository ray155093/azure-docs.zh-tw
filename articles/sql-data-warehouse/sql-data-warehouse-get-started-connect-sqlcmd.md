<properties
   pageTitle="使用 SQLCMD 進行查詢 | Microsoft Azure"
   description="使用 SQLCMD 查詢資料倉儲。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/09/2016"
   ms.author="mausher;barbkess;sonyama"/>

# 使用 SQLCMD 進行查詢

> [AZURE.SELECTOR]
- [Power BI][]
- [Azure Machine Learning][]
- [SQLCMD][]

本逐步解說會說明如何使用 sqlcmd.exe 公用程式查詢 Azure SQL 資料倉儲。

## 必要條件

+ 若要下載 [sqlcmd.exe][]，請參閱[適用於 SQL Server 的 Microsoft 命令列公用程式 11][]。

## 連線

若要開始使用 sqlcmd，請開啟命令提示字元，然後輸入 **sqlcmd** 並在後面加上 SQL 資料倉儲資料庫的連接字串。連接字串需要下列必要參數：

+ **伺服器 (-S)：** 採用 `<`Server Name`>`.database.windows.net 格式的伺服器
+ **資料庫 (-d)：**資料庫名稱。
+ **使用者 (-U)：**`<`使用者`>`表單中的伺服器使用者
+ **密碼 (-P)：**與使用者相關聯的密碼。
+ **啟用引號識別碼 (-I)：**必須啟用引號識別碼，才能連接到 SQL 資料倉儲執行個體。

例如，您的連接字串看起來可能如下所示︰

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] 目前需要可啟用引號識別碼的 -I 選項，才能連線到 SQL 資料倉儲。

## 查詢

連線之後，您可以對執行個體發出任何支援的 Transact-SQL 陳述式。此範例會在互動模式中提交查詢。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

後續的這些範例會說明如何使用 -Q 選項或以管線將 SQL 傳送到 sqlcmd，來執行批次模式查詢。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
C:\>"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## 後續步驟

若要了解所有 sqlcmd 選項，請參閱 [sqlcmd 文件][sqlcmd.exe]。

<!--Articles-->
[connecting with PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Azure Machine Learning]: ./sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/zh-TW/library/ms162773.aspx
[適用於 SQL Server 的 Microsoft 命令列公用程式 11]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portal]: https://portal.azure.com

<!--Image references-->

<!---HONumber=AcomDC_0615_2016-->