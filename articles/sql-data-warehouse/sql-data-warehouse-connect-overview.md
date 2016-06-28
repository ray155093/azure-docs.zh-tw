<properties
   pageTitle="連線到 Azure SQL 資料倉儲 | Microsoft Azure"
   description="連接到 Azure SQL 資料倉儲的連線概觀"
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
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# 連接到 Azure SQL 資料倉儲

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-connect-overview.md)
- [驗證](sql-data-warehouse-authentication.md)
- [驅動程式](sql-data-warehouse-connection-strings.md)

連接到 Azure SQL 資料倉儲的概觀。

## 從入口網站探索連接字串

您的 SQL 資料倉儲會與 Azure SQL 伺服器相關聯。若要進行連接，您需要伺服器的完整名稱 (**servername**.database.windows.net*)。

若要尋找完整的伺服器名稱：

1. 移至 [Azure 入口網站][]。
2. 按一下 [SQL 資料庫]，再按一下您想要連接的資料庫。此範例使用 AdventureWorksDW 範例資料庫。
3. 找出完整的伺服器名稱。

    ![完整伺服器名稱][1]

## 連線設定
SQL 資料倉儲會在連線和建立物件期間標準化一些設定。這些不會被覆寫。

| 資料庫設定 | 值 |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | 開啟 |
| QUOTED\_IDENTIFIERS | 開啟 |
| NO\_COUNT | 關閉 |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| 資料庫定序 | SQL\_Latin1\_General\_CP1\_CI\_AS |

## 工作階段和要求
建立連線和工作階段之後，您就可以撰寫查詢並將其提交至 SQL 資料倉儲。

每個查詢都會由一或多個要求識別碼表示。在該連線上提交的所有查詢都是單一工作階段的一部分，因此無法由單一工作階段識別碼表示。

不過，SQL 資料倉儲是散發式的 MPP (大量平行處理) 系統，相較於 SQL Server，工作階段和要求識別碼的公開方式都有點不同。

工作階段和要求皆由其各自的識別碼以邏輯方式表示。

| 識別碼 | 範例值 |
| :--------- | :------------ |
| 工作階段識別碼 | SID123456 |
| 要求 ID | QID123456 |

請注意，工作階段識別碼的前置詞為 SID - 工作階段識別碼的速記格式 - 而要求的前置詞為 QID，此為查詢識別碼的速記格式。

您將需要此資訊，幫助您在監視查詢效能時識別您的查詢。您可以使用 [Azure 入口網站]和動態管理檢視來監視查詢效能。

此查詢會識別目前的工作階段。

```sql
SELECT SESSION_ID()
;
```

若要檢視目前正在執行或近期曾針對資料倉儲執行的所有查詢，您可以使用下列範例。這會建立檢視，然後執行此檢視。

```sql
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;

SELECT * FROM dbo.vSessionRequests;
```

## 後續步驟

若要透過 Visual Studio 和其他應用程式開始查詢您的資料倉儲，請參閱[透過 Visual Studio 查詢][]。


<!--Arcticles-->

[透過 Visual Studio 查詢]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Azure 入口網站]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0622_2016-->