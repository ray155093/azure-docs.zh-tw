<properties
   pageTitle="針對 Azure SQL 資料倉儲問題進行疑難排解 | Microsoft Azure"
   description="針對 Azure SQL 資料倉儲問題進行疑難排解。"
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
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess"/>

# 針對 Azure SQL 資料倉儲問題進行疑難排解
本主題列出一些您在使用「Azure SQL 資料倉儲」時可能遇到的較常見問題。


##連接失敗

如果您遇到連接問題，以下是一些客戶所回報的較常見問題。

### CTAIP 錯誤
若已在 SQL Server Master 資料庫上建立登入，但未在 SQL 資料倉儲資料庫上建立，則會發生這個錯誤。如果您遇到這個錯誤，請查看[安全性概觀][]一文。這篇文章說明如何在 Master 上建立登入，接著如何在 SQL 資料倉儲資料庫上建立使用者。

### 防火牆規則
為確保只有已知的 IP 位址擁有資料庫的存取權限，Azure SQL 資料庫受到伺服器及資料庫層級的防火牆所保護。防火牆預設將會受到保護，因此您在可以連線之前，必須明確啟用單一 IP 位址或位址範圍。若要設定防火牆的存取，請遵循[佈建指示][]中[設定用戶端 IP 的伺服器防火牆存取][]的步驟。

### 不支援的工具/通訊協定
SQL 資料倉儲建議使用 [Visual Studio 2013 或 2015][] 來查詢您的資料。針對用戶端連接性，建議使用 [SQL Server Native Client 10/11 (ODBC)][]。SQL Server Management Studio (SSMS) 目前尚未受到支援。雖然它的部分功能可以運作，物件總管樹狀結構尚無法搭配 SQL 資料倉儲使用，而查詢功能可能可以在忽略某些錯誤訊息之後運作。


## 效能問題

開始了解如何改善查詢效能的最佳位置是 [SQL 資料倉儲最佳做法][]一文。如果您正試著針對特定查詢進行疑難排解，則這篇關於[了解如何監視查詢][]文章也是個不錯的起點。有時，只要[調整您的 SQL 資料倉儲][]來提升計算能力，即可改善查詢的執行速度。

## 叢集資料行存放區區段品質

叢集資料行存放區區段品質對叢集資料行存放區資料表的最佳查詢效能很重要。壓縮的資料列群組中的資料列數目可以測量區段品質。下列查詢會識別出資料行存放區索引區段健康狀態不佳的資料表，並產生 T-SQL 來重建這些資料表的資料行存放區索引。這個查詢結果的第一欄會提供您重建每個索引的 T-SQL。第二欄會提供最基本的資源類別建議，用以最佳化壓縮。
 
**步驟 1：**對每個 SQL 資料倉儲資料庫執行這個查詢，以找出任何次佳的叢集資料行存放區索引。如果未傳回任何資料列，則不需採取進一步動作。

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
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000 OR
    AVG(CASE WHEN rg.State = 0 THEN rg.Total_rows ELSE NULL END) < 100000

ORDER BY 
    s.name, t.name
```
 
**步驟 2：**提高使用者的資源類別，該使用者有權將此資料表上的索引重建為上述查詢第二欄中建議的資源類別。

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  上述的 LoadUser 應為建立來執行 ALTER INDEX 陳述式的有效使用者。無法變更 db\_owner 使用者的資源類別。如需資源類別以及如何建立新使用者的詳細資訊，請參閱以下連結的內容。

 
**步驟 3：**以步驟 2 的使用者身分登入 (例如「LoadUser」)，他現在使用較高的資源類別，執行步驟 1 查詢所產生的 ALTER INDEX 陳述式。請確定這個使用者擁有步驟 1 查詢識別出的資料表 ALTER 權限。
 
**步驟 4：**重新執行步驟 1 的查詢。如果有效建置了索引，這個查詢就不應該傳回任何資料列。若未傳回任何資料列，表示完成作業。如果您有多個 SQL DW 資料庫，則要對每個資料庫都重複這個程序。如果傳回資料列，請繼續執行步驟 5。
 
**步驟 5：** 如果重新執行步驟 1 的查詢會傳回資料列，某些資料表可能有特寬的資料列，需要大量記憶體才能以最佳方式建置叢集資料行存放區索引。如果是這種情況，請使用 xlargerc 類別對這些資料表再試一次這個程序。若要變更資源類別，請使用 xlargerc 重複步驟 2。然後，針對仍有次佳索引的資料表重複步驟 3。如果您使用的是 DW100-DW300，也使用了 xlargerc，您就可以選擇讓索引保持原樣，或暫時增加 DWU 以為這項作業提供更多的記憶體。
 
**最後一個步驟︰**前文中指定的資源類別，是建立最高品質資料行存放區索引的建議基本資源類別。我們建議您保留這項設定，供使用者載入您的資料。不過，如果您想要復原步驟 2 中的變更，您可以使用下列命令執行這項作業。

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```

CCI 資料表負載的基本資源類別指引，是針對 DW100 到 DW300 使用 xlargerc、針對 DW400 到 DW600 使用 largerc，且自 DW1000 起全部使用 mediumrc。這項指引對多數工作負載都是很好的做法。目標是提供每個索引建置作業 400 MB 或更多的記憶體。不過，一種大小無法滿足所有需求。最佳化資料行存放區索引所需要的記憶體視要載入的資料而定，主要受資料列大小所影響。資料列寬度較小的資料表需要較少的記憶體，寬度較寬則需要較多記憶體。如果想要實驗，您可以使用步驟 1 的查詢，看看較小的記憶體配置是否得到最佳的資料行存放區索引。每個資料列群組平均至少要超過 100K 資料列。500K 以上更好。您看到的最大值會是每個資料列群組 1 百萬個資料列。如需如何管理資源類別和並行，請參閱下面的連結內容。


## 後續步驟

如果您找不到解決上述問題的方法，以下是一些您可以嘗試的其他資源。

- [部落格]
- [功能要求]
- [影片]
- [CAT 小組部落格]
- [建立支援票證]
- [MSDN 論壇]
- [Stack Overflow 論壇]
- [Twitter]

<!--Image references-->

<!--Article references-->
[安全性概觀]: ./sql-data-warehouse-overview-manage-security.md
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[調整您的 SQL 資料倉儲]: ./sql-data-warehouse-manage-compute-overview.md
[了解如何監視查詢]: ./sql-data-warehouse-manage-monitor.md
[佈建指示]: ./sql-data-warehouse-get-started-provision.md
[設定用戶端 IP 的伺服器防火牆存取]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 或 2015]: ./sql-data-warehouse-get-started-connect.md
[SQL 資料倉儲最佳做法]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other Web references-->
[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT 小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 論壇]: https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureSQLDataWarehouse
[Stack Overflow 論壇]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[影片]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0629_2016-->