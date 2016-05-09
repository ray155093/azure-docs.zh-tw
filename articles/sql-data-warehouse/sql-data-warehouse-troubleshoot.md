<properties
   pageTitle="疑難排解 |Microsoft Azure"
   description="疑難排解 SQL 資料倉儲的問題。"
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
   ms.date="04/20/2016"
   ms.author="mausher;sonyama;barbkess"/>

# 疑難排解
下列主題列出一些客戶在使用 SQL 資料倉儲時常見的問題。

## 連線能力
連接到 Azure SQL 資料倉儲可能會因為下列幾個常見的原因而失敗：

- 未設定防火牆規則
- 使用不支援的工具/通訊協定

### 防火牆規則
為確保只有已知的 IP 位址可以存取資料庫，Azure SQL 資料庫受到伺服器及資料庫層級的防火牆所保護。防火牆預設受到保護，亦即您必須允許您的 IP 位址存取，才能連線。

若要設定防火牆的存取，請執行 [[佈建][]] 頁面中的[設定您用戶端 IP 的伺服器防火牆存取][]一節。

### 使用不支援的工具/通訊協定
SQL 資料倉儲支援 [Visual Studio 2013/2015年][] 作為開發環境及 [SQL Server Native Client 10/11 (ODBC)][] 來連接用戶端。

若要深入了解，請參閱我們 [[連接][]] 頁面。

## 查詢效能

### 統計資料

[統計資料][]是包含資料庫資料行中，值之範圍與頻率的相資訊的物件。查詢引擎會使用這些統計資料最佳化查詢執行，以及改善查詢效能。不像 SQL Server 或 SQL DB，SQL 資料倉儲不會自動建立或自動更新統計資料。所有資料表的統計資料都必須以手動方式維護。

您可以使用下列查詢，判斷每份資料表上次更新了哪些統計資料。

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

### 叢集資料行存放區區段品質

叢集資料行存放區區段品質對叢集資料行存放區資料表的最佳查詢效能很重要。壓縮的資料列群組中的資料列數目可以測量區段品質。下列查詢會識別出資料行存放區索引區段健康狀態不佳的資料表，並產生 T-SQL 來重建這些資料表的資料行存放區索引。這個查詢結果的第一欄會提供您重建每個索引的 T-SQL。第二欄會提供最基本的資源類別建議，用以最佳化壓縮。
 
**步驟 1：**對每個 SQL 資料倉儲資料庫執行這個查詢，識別出任何次佳的叢集資料行存放區索引。若未傳回任何資料列，則這個迴歸並未影響您，不需要執行任何動作。

```sql
SELECT 
     'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;' AS [T-SQL to Rebuild Index]
    ,CASE WHEN n.nbr_nodes < 3 THEN 'xlargerc' WHEN n.nbr_nodes BETWEEN 4 AND 6 THEN 'largerc' ELSE 'mediumrc' END AS [Resource Class Recommendation]
    ,s.name AS [Schema Name]
    ,t.name AS [Table Name]
    ,AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) AS [Ave Rows in Compressed Row Groups]
FROM 
    sys.pdw_nodes_column_store_row_groups rg
    JOIN sys.pdw_nodes_tables pt 
        ON rg.object_id = pt.object_id AND rg.pdw_node_id = pt.pdw_node_id AND pt.distribution_id = rg.distribution_id
    JOIN sys.pdw_table_mappings tm 
        ON pt.name = tm.physical_name
    INNER JOIN sys.tables t 
        ON tm.object_id = t.object_id
INNER JOIN sys.schemas s
    ON t.schema_id = s.schema_id
CROSS JOIN (SELECT COUNT(*) nbr_nodes  FROM sys.dm_pdw_nodes WHERE type = 'compute') n
GROUP BY 
    n.nbr_nodes, s.name, t.name
HAVING 
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000
ORDER BY 
    s.name, t.name
```
 
**步驟 2：**提高使用者的資源類別，這個使用者有權限可以將這份資料表上的索引重建為上述查詢第二欄建議的資源類別。

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  上述的 LoadUser 應為建立來執行 ALTER INDEX 陳述式的有效使用者。無法變更 db\_owner 使用者的資源類別。如需資源類別以及如何建立新使用者的詳細資訊，請參閱以下連結的內容。

 
**步驟 3：**以步驟 2 的使用者身分登入 (例如，“LoadUser”)，他現在使用較高的資源類別，執行步驟 1 查詢所產生的 ALTER INDEX 陳述式。請確定這個使用者擁有步驟 1 查詢識別出的資料表 ALTER 權限。
 
**步驟 4：**重新執行步驟 1 的查詢。如果有效建置了索引，這個查詢就不應該傳回任何資料列。若未傳回任何資料列，表示完成作業。如果您有多個 SQL DW 資料庫，則要對每個資料庫都重複這個程序。如果傳回資料列，請繼續執行步驟 5。
 
**步驟 5：** 如果重新執行步驟 1 的查詢會傳回資料列，某些資料表可能有特寬的資料列，需要大量的記憶體才能以最佳方式建置叢集資料行存放區索引。如果是這種情況，請使用 xlargerc 類別對這些資料表再試一次這個程序。若要變更資源類別，請使用 xlargerc 重複步驟 2。然後，針對仍有次佳索引的資料表重複步驟 3。如果您使用的是 DW100-DW300，也使用了 xlargerc，您就可以選擇讓索引保持原樣，或暫時增加 DWU 以為這項作業提供更多的記憶體。
 
**最後一個步驟︰**前文中指定的資源類別，是建立最高品質資料行存放區索引的建議基本資源類別。我們建議您保留這項設定，供使用者載入您的資料。不過，如果您想要復原步驟 2 中的變更，您可以使用下列命令執行這項作業。

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```


CCI 資料表負載的基本資源類別指引，是 DW100-DW300 使用 xlargerc、DW400-DW600 使用 largerc，且自 DW1000 起全部使用 mediumrc。這項指引對多數工作負載都是很好的做法。目標是提供每個索引建置作業 400 MB 或更多的記憶體。不過，一種大小無法滿足所有需求。最佳化資料行存放區索引所需要的記憶體視要載入的資料而定，主要受資料列大小所影響。資料列寬度較小的資料表需要較少的記憶體，寬度較寬則需要較多記憶體。如果想要實驗，您可以使用步驟 1 的查詢，看看較小的記憶體配置是否得到最佳的資料行存放區索引。每個資料列群組平均至少要超過 100K 資料列。500K 以上更好。您看到的最大值會是每個資料列群組 1 百萬個資料列。如需如何管理資源類別和並行，請參閱下面的連結內容。


### 關鍵效能概念

請參閱下列文章，以瞭解一些關鍵效能與調整的其他概念：

- [效能和延展性][]
- [並行模型][]
- [設計資料表][]
- [為資料表選擇雜湊散發索引鍵][]

## 後續步驟
請參閱[開發概觀][]文章，以取得有關建置 SQL 資料倉儲解決方案的一些指導。

<!--Image references-->

<!--Article references-->
[效能和延展性]: sql-data-warehouse-performance-scale.md
[並行模型]: sql-data-warehouse-develop-concurrency.md
[設計資料表]: sql-data-warehouse-develop-table-design.md
[為資料表選擇雜湊散發索引鍵]: sql-data-warehouse-develop-hash-distribution-key
[開發概觀]: sql-data-warehouse-overview-develop.md
[佈建]: sql-data-warehouse-get-started-provision.md
[設定您用戶端 IP 的伺服器防火牆存取]: sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip
[Visual Studio 2013/2015年]: sql-data-warehouse-get-started-connect.md
[連接]: sql-data-warehouse-get-started-connect.md
[統計資料]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=AcomDC_0427_2016-->