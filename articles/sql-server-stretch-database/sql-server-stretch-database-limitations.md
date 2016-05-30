<properties
	pageTitle="Stretch Database 的限制 |Microsoft Azure"
	description="了解 Stretch Database 的限制。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Stretch Database 的限制

了解啟用 Stretch 的資料表的相關限制，以及目前使您無法為資料表啟用 Stretch 的相關限制。

##  <a name="Caveats"></a>Stretch 資料表的限制

啟用 Stretch 的資料表有下列限制。

### 條件約束

-   在包含移轉的資料之 Azure 資料表中，UNIQUE (唯一) 條件約束和 PRIMARY KEY (主索引鍵) 條件約束並無強制唯一性。

### DML 作業

-   在啟用 Stretch 的資料表中，或是包含啟用 Stretch 的資料表之檢視中，您無法 UPDATE (更新) 或 DELETE (刪除) 資料列。

-   您無法將資料列 INSERT (插入) 連結伺服器上啟用 Stretch 的資料表。

### 索引數

-   您無法建立其中包含 Stretch 資料表的檢視表索引。

-   SQL Server 索引上的篩選不會傳播至遠端資料表。

##  <a name="Limitations"></a>目前使您無法為資料表啟用 Stretch 的相關限制

下列項目目前會使您無法為資料表啟用 Stretch

### 資料表屬性

-   有超過 1,023 個資料行或超過 998 個索引的資料表

-   包含 FILESTREAM 資料的檔案資料表或資料表

-   複製的資料表，或目前正在使用變更追蹤或異動資料擷取的資料表

-   記憶體最佳化資料表

### 資料類型

-   text、ntext 和 image

-   timestamp

-   sql\_variant

-   XML

-   CLR 資料類型包括 geometry、geography、hierarchyid 和 CLR 使用者定義類型

### 資料行類型

-   COLUMN\_SET

-   計算資料行

### 條件約束

-   預設條件約束和檢查條件約束

-   參考資料表的外部索引鍵條件約束。在父子式關聯性 (例如，Order 和 Order\_Detail) 中，您可以為子資料 (Order\_Detail) 表啟用 Stretch，父資料表 (Order) 則不行。

### 索引數

-   全文檢索索引

-   XML 索引

-   空間索引

-   參考資料表的索引檢視表

## 另請參閱

[透過執行 Stretch Database Advisor 為資料庫和資料表識別 Stretch Database](sql-server-stretch-database-identify-databases.md)

[為資料庫啟用 Stretch Database](sql-server-stretch-database-enable-database.md)

[為資料表啟用 Stretch Database](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0518_2016-->