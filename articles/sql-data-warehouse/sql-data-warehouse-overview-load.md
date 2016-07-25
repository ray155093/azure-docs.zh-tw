   <properties
   pageTitle="將資料載入 Azure SQL 資料倉儲 | Microsoft Azure"
   description="了解將資料載入 SQL 資料倉儲的常見案例這包含使用 PolyBase、Azure Blob 儲存體、一般檔案及寄送磁碟。您也可以使用協力廠商工具。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# 將資料載入 Azure SQL 資料倉儲

將資料載入 SQL 資料倉儲之案例選項和建議的摘要。

為載入準備資料通常是載入資料最困難的部分。Azure 可將 Azure Blob 儲存體做為許多服務的一般資料存放區使用，並使用 Azure Data Factory 來協調 Azure 服務之間的通訊和資料移動來簡化載入。這些程序會與 PolyBase 技術整合，該技術使用巨量平行處理 (MPP) 來從 Azure Blob 儲存體將資料平行載入到 SQL 資料倉儲。

如需載入範例資料庫的教學課程，請參閱[載入範例資料庫][]。

## 從 Azure Blob 儲存體載入
將資料匯入 SQL 資料倉儲的最快方式，是使用 PolyBase 從 Azure Blob 儲存體載入資料。PolyBase 使用 SQL 資料倉儲的巨型平行處理 (MPP) 設計，來從 Azure Blob 儲存體平行載入資料。若要使用 PolyBase，您可以使用 T-SQL 命令或 Azure Data Factory 管線。

### 1\.使用 PolyBase 和 T-SQL

載入流程的摘要：

2. 將您的資料格式化為 UTF-8，因為 PolyBase 目前並不支援 UTF-16。
2. 將您的資料移到 Azure Blob 儲存體，並將它儲存於文字檔中。
3. 設定 SQL 資料倉儲中的外部物件，以定義資料的位置和格式
4. 執行 T-SQL 命令以將資料平行載入新的資料庫資料表。

<!-- 5. Schedule and run a loading job. --> 

如需教學課程，請參閱[從 Azure Blob 儲存體將資料載入 SQL 資料倉儲 (PolyBase)][]。

### 2\.使用 Azure Data Factory

如需使用 PolyBase 較簡易的方法，您可以建立使用 PolyBase 將資料從 Azure Blob 儲存體載入 SQL 資料倉儲的 Azure Data Factory 管線。由於您不需要定義 T-SQL 物件，使您可以快速完成設定。如果您需要在不匯入外部資料之下對它做出查詢，請使用 T-SQL。

載入流程的摘要：

2. 將您的資料格式化為 UTF-8，因為 PolyBase 目前並不支援 UTF-16。
2. 將您的資料移到 Azure Blob 儲存體，並將它儲存於文字檔中。
3. 建立 Azure Data Factory 管線以內嵌資料。使用 PolyBase 選項。
4. 排程並執行管線。

如需教學課程，請參閱[從 Azure blob 儲存體將資料載入 Azure SQL 資料倉儲 (Azure Data Factory)][]。


## 從 SQL Server 載入
若要將資料從 SQL Server 載入 SQL 資料倉儲，您可以使用 Integration Services (SSIS)、傳輸一般檔案，或寄送磁碟給 Microsoft。繼續閱讀以查看不同載入程序的摘要，以及教學課程的連結。

若要規劃從 SQL Server 到 SQL 資料倉儲的完整資料移轉，請參閱[移轉概觀][]。

### 使用 Integration Services (SSIS)
如果您已經在使用 Integration Services (SSIS) 套件來載入至 SQL Server，您可以更新套件以使用 SQL Server 做為來源，並使用 SQL 資料倉儲做為目的地。這是一項快速且簡單的作業，也很適合在您不想移轉載入程序以使用已存在於雲端中之資料的情況下使用。缺點是載入的速度將會比使用 PolyBase 的情況還慢，因為此 SSIS 並不會以平行的方式執行載入。

載入流程的摘要：

1. 修改您的 Integration Services 套件以將來源指向 SQL Server 執行個體，並將目的地指向 SQL 資料倉儲資料庫。
2. 如果您的結構描述尚未移轉至 SQL 資料倉儲，請執行此動作。
3. 將您套件中的對應變更為僅使用 SQL 資料倉儲所支援的資料類型。
3. 排程並執行套件。

如需教學課程，請參閱[從 SQL Server 將資料載入 Azure SQL 資料倉儲 (SSIS)][]。

### 使用 AZCopy (建議於資料 < 10 TB 的情況使用)
如果您的資料大小 < 10 TB，您可以從 SQL Server 將資料匯出成一般檔案，將檔案複製到 Azure Blob 儲存體，然後使用 PolyBase 將資料載入至 SQL 資料倉儲

載入流程的摘要：

1. 使用 bcp 命令列公用程式從 SQL Server 將資料匯出成一般檔案。
2. 使用 AZCopy 命令列公用程式將資料從一般檔案複製到 Azure Blob 儲存體。
3. 使用 PolyBase 來載入 SQL 資料倉儲。

如需教學課程，請參閱[從 Azure Blob 儲存體將資料載入 SQL 資料倉儲 (PolyBase)][]。

### 使用 bcp
如果您只有少量的資料，您可以使用 bcp 將資料直接載入至 Azure SQL 資料倉儲。

載入流程的摘要：
1. 使用 bcp 命令列公用程式從 SQL Server 將資料匯出成一般檔案。
2. 使用 bcp 將資料從一般檔案直接載入 SQL 資料倉儲。

如需教學課程，請參閱[從 SQL Server 將資料載入 Azure SQL 資料倉儲 (bcp)][]。


### 使用匯入/匯出 (建議於資料 > 10 TB 的情況使用)
如果您的資料大小 > 10 TB，且您想將它移至 Azure，我們建議您使用我們的磁碟寄送服務：[匯入/匯出][]。

載入流程的摘要
2. 使用 bcp 命令列公用程式從 SQL Server 將資料以一般檔案的形式匯出至可轉移磁碟上。
3. 將磁碟寄送給 Microsoft。
4. Microsoft 將資料載入 SQL 資料倉儲

## 從 HDInsight 載入
SQL 資料倉儲支援從 HDInsight 透過 PolyBase 載入資料。此程序和從 Azure Blob 儲存體載入資料相同 - 使用 PolyBase 連接到 HDInsight 以載入資料。

### 1\.使用 PolyBase 和 T-SQL

載入流程的摘要：

2. 將您的資料格式化為 UTF-8，因為 PolyBase 目前並不支援 UTF-16。
2. 將您的資料移到 HDInsight，並將它儲存為文字檔、ORC 或 Parquet 格式。
3. 設定 SQL 資料倉儲中的外部物件，以定義資料的位置和格式。
4. 執行 T-SQL 命令以將資料平行載入新的資料庫資料表。

如需教學課程，請參閱[從 Azure Blob 儲存體將資料載入 SQL 資料倉儲 (PolyBase)][]。

## 建議

我們有許多合作夥伴皆提供載入解決方案。若要深入了解，請參閱我們的[解決方案合作夥伴][]清單。

如果您的資料是來自非關聯式來源，且您想要將它載入 SQL 資料倉儲，您在載入之前必須先將它轉換成資料列和資料行。已轉換的資料並不需要儲存在資料庫中，而可以儲存在文字檔案中。

建立新載入資料的統計資料。Azure 資料倉儲尚未支援自動建立或自動更新統計資料。為了獲得查詢的最佳效能，在首次載入資料，或是資料中發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。如需詳細資料，請參閱[統計資料][]。


## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[從 Azure Blob 儲存體將資料載入 SQL 資料倉儲 (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[從 Azure blob 儲存體將資料載入 Azure SQL 資料倉儲 (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[從 SQL Server 將資料載入 Azure SQL 資料倉儲 (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[從 SQL Server 將資料載入 Azure SQL 資料倉儲 (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[載入範例資料庫]: ./sql-data-warehouse-load-sample-databases.md
[移轉概觀]: ./sql-data-warehouse-overview-migrate.md
[解決方案合作夥伴]: ./sql-data-warehouse-partner-business-intelligence.md
[開發概觀]: ./sql-data-warehouse-overview-develop.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[匯入/匯出]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/

<!---HONumber=AcomDC_0713_2016-->