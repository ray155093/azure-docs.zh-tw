<properties
    pageTitle="跨相應放大的雲端資料庫報告 | Microsoft Azure"
    description="如何設定水平資料分割的彈性查詢"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="torsteng;sidneyh" />

# 跨相應放大的雲端資料庫報告 (預覽)

![跨分區查詢][1]

分區化資料庫跨相應放大的資料層發佈資料列。所有參與的資料庫都有相同的結構描述，也稱為水平資料分割。使用彈性查詢，您可以建立跨越分區化資料庫中所有資料庫的報告。

如需快速啟動，請參閱[分區化的彈性資料庫查詢 (水平資料分割)](sql-database-elastic-query-getting-started.md)。

若為非分區資料庫，請參閱[跨資料庫查詢 (垂直資料分割) 的彈性資料庫查詢](sql-database-elastic-query-vertical-partitioning.md)。

 
## 必要條件

* 使用彈性資料庫用戶端程式庫，建立分區對應。請參閱[分區對應管理](sql-database-elastic-scale-shard-map-management.md)。或使用[開始使用彈性資料庫工具](sql-database-elastic-scale-get-started.md)中的範例應用程式。
* 或者，請參閱[轉換現有的資料庫以使用彈性資料庫工具](sql-database-elastic-convert-to-use-elastic-tools.md)。
* 使用者必須擁有 ALTER ANY EXTERNAL DATA SOURCE 權限。這個權限包含在 ALTER DATABASE 權限中。
* 需有 ALTER ANY EXTERNAL DATA SOURCE 權限，才能參考基礎資料來源。

## 概觀

這些陳述式可在彈性查詢資料庫中建立分區化資料層的中繼資料表示法。


1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [建立資料庫範圍認證](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 

## 1\.1 建立資料庫範圍的主要金鑰和認證 

彈性查詢使用認證連接到遠端資料庫。

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**注意**︰請確定 *<username>* 不含任何 *"@servername"* 後置詞。

## 1\.2 建立外部資料來源

語法：

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### 範例 

	CREATE EXTERNAL DATA SOURCE MyExtSrc 
	WITH 
	( 
		TYPE=SHARD_MAP_MANAGER,
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ShardMapDatabase', 
		CREDENTIAL= SMMUser, 
		SHARD_MAP_NAME='ShardMap' 
	);
 
擷取目前的外部資料來源清單︰

	select * from sys.external_data_sources; 

外部資料來源會參考分區對應。彈性查詢會接著使用外部資料來源和基礎分區對應來列舉參與資料層的資料庫。在彈性查詢處理期間，使用相同的認證來讀取分區對應和存取分區上的資料。

## 1\.3 建立外部資料表 
 
語法：

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**範例**

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

從目前的資料庫擷取外部資料表清單：

	SELECT * from sys.external_tables; 

捨棄外部資料表：

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### 備註

DATA\_SOURCE 子句會定義用於外部資料表的外部資料來源 (分區對應)。

SCHEMA\_NAME 和 OBJECT\_NAME 子句會將外部資料表定義對應至不同結構描述中的資料表。如果省略，即會假設遠端物件的結構描述為 "dbo" 並假設其名稱與所定義的外部資料表名稱相同。如果您的遠端資料表名稱已存在於您要建立外部資料表的資料庫中，這會很有用。例如，您想要定義外部資料表以取得相應放大的資料層上目錄檢視或 DMV 的彙總檢視。由於目錄檢視和 DMV 已經存在於本機，所以您無法將其名稱使用於外部資料表定義。而是，在 SCHEMA\_NAME 和/或 OBJECT\_NAME 子句中使用不同的名稱和使用目錄檢視或 DMV 的名稱。(請參閱下面的範例)。

DISTRIBUTION 子句會指定用於此資料表的資料散發。查詢處理器會利用 DISTRIBUTION 子句中提供的資訊來建置最有效率的查詢計劃。

1. **SHARDED** 表示跨資料庫水平分割資料。資料散發的分割索引鍵是 **<sharding_column_name>** 參數。
2. **REPLICATED** 表示資料表的相同複本會存在於每個資料庫上。您必須負責確保複本在所有資料庫上都相同。
3. **ROUND\_ROBIN** 表示使用應用程式相依的發佈方法水平分割資料表。 

**資料層參考**：外部資料表 DDL 指的是外部資料來源。外部資料來源會指定分區對應，以將找出資料層中的所有資料庫所需的資訊提供給外部資料表。


### 安全性考量 

可存取外部資料表的使用者可以在外部資料來源定義中所提供的認證下，自動取得基礎遠端資料表的存取權。避免透過外部資料來源認證提高不想提高的權限。對外部資料表使用「授與」或「撤銷」，就像它是一般的資料表一樣。

一旦您已定義外部資料來源和外部資料表，現在您可以對外部資料表使用完整的 T-SQL。

## 範例︰查詢水平資料分割的資料庫 

下列查詢在倉儲、訂單及訂單明細之間執行三方聯結，並使用數個彙總和選擇性篩選。其假設 (1) 水平資料分割 (分區化) 以及 (2) 倉儲、訂單及訂單明細依倉儲識別碼資料行分區，而彈性查詢可以將聯結共置於分區上以及平行處理分區上成本較高的查詢部分。

	select  
		 w_id as warehouse,
		 o_c_id as customer,
		 count(*) as cnt_orderline,
		 max(ol_quantity) as max_quantity,
		 avg(ol_amount) as avg_amount, 
		 min(ol_delivery_d) as min_deliv_date
	from warehouse 
	join orders 
	on w_id = o_w_id
	join order_line 
	on o_id = ol_o_id and o_w_id = ol_w_id 
	where w_id > 100 and w_id < 200 
	group by w_id, o_c_id 
 
## 遠端 T-SQL 執行的預存程序：sp\_execute\_remote

彈性查詢也會介紹可供直接存取分區的預存程序。預存程序稱為 **sp\_execute\_remote**，而且可用來在遠端資料庫上執行遠端預存程序或 T-SQL 程式碼。它需要以下參數：

* 資料來源名稱 (nvarchar)：RDBMS 類型的外部資料來源名稱。 
* 查詢 (nvarchar)：對每個分區執行的 T-SQL 查詢。 
* 參數宣告 (nvarchar) - 選用：含有查詢參數 (如 sp\_executesql) 中所用參數的資料類型定義的字串。 
* 參數值清單 - 選用：以逗號分隔的參數值清單 (如 sp\_executesql)。

sp\_execute\_remote 使用引動過程參數中提供的外部資料來源，在遠端資料庫上執行指定的 T-SQL 陳述式。它會使用外部資料來源的認證連接 shardmap 管理員資料庫和遠端資料庫。

範例：

	EXEC sp_execute_remote
		N'MyExtSrc',
		N'select count(w_id) as foo from warehouse' 

## 工具的連線能力  

使用一般 SQL Server 連接字串，將您的應用程式、BI 和資料整合工具連接到包含外部資料表定義的資料庫。請確定 SQL Server 可支援做為您的工具的資料來源。然後像任何其他連接到工具的 SQL Server 資料庫一樣，參考彈性查詢資料庫，並如同本機資料表一樣，從您的工具或應用程式使用外部資料表。

## 最佳作法 

* 請確定分區對應資料庫和所有分區都已透過 SQL DB 防火牆取得彈性查詢端點資料庫的存取權。  

* 驗證或強制執行外部資料表所定義的資料分佈。如果您的實際資料分佈與資料表定義中指定的散發不同，您的查詢可能會產生非預期的結果。

* 彈性查詢目前不會執行分區刪除，因為分區化索引鍵的述詞允許安全地排除處理某些分區。

* 彈性查詢最適合可在分區完成大部分運算的查詢。使用可在分區上評估的選擇性篩選述詞，或在所有分區上以資料分割對齊方式來聯結資料分割索引鍵，通常可以獲得最佳查詢效能。其他查詢模式可能需要從分區載入大量資料至前端節點，但效能可能不佳。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->

<!---HONumber=AcomDC_0427_2016-->