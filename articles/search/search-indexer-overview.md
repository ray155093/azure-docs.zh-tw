<properties
	pageTitle="Azure 搜尋服務中的索引子 | Microsoft Azure | 雲端託管搜尋服務"
	description="耙梳 Azure SQL Database、DocumentDB 或 Azure 儲存體，以擷取可搜尋的資料並填入 Azure 搜尋服務索引。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/08/2016"
	ms.author="heidist"/>

# Azure 搜尋服務中的索引子
> [AZURE.SELECTOR]
- [概觀](search-indexer-overview.md)
- [入口網站](search-import-data-portal.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Blob 儲存體 (預覽)](search-howto-indexing-azure-blob-storage.md)
- [表格儲存體 (預覽)](search-howto-indexing-azure-tables.md)

Azure 搜尋服務中的**索引子**是一種編目程式，其可從外部資料來源擷取可搜尋的資料和中繼資料，並根據索引和資料來源之間的欄位對欄位對應填入索引。這種方法有時稱為「提取模型」，因為您不需要撰寫任何程式碼來將資料推送至索引，服務就會自行提取資料。

您可以使用索引子做為擷取資料的唯一手段，或結合使用多項技術 (包含使用索引子) 來僅載入索引中的某些欄位。

您可以依需要執行索引子，也可以依週期性的資料重新整理排程，最多每十五分鐘執行一次。若想更頻繁地進行更新，則 Azure 搜尋服務和外部資料來源中都必須要有可同時更新資料的發送模型。

## 建立與管理索引子的方法

對於 Azure SQL 或 DocumentDB 等公開上市的索引子，您可以使用下列方法來建立及管理索引子︰

- [入口網站 > 匯入資料精靈](search-get-started-portal.md)
- [服務 REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

預覽索引子 (如 Azure Blob 或資料表儲存體) 需要程式碼和預覽 API，如[適用於索引子的 Azure 搜尋服務預覽 REST API](search-api-indexers-2015-02-28-preview.md)。入口網站工具通常不適用於預覽功能。

## 基本組態步驟

索引子可以提供資料來源特有的功能。在這方面，索引子或資料來源組態的某些層面會因索引子類型而所有不同。不過，所有索引子都有共用的的基本組成和需求。下文涵蓋所有的索引子的通用步驟。

### 步驟 1：建立索引

索引子能將有關資料擷取的某些工作自動化，不過不包括建立索引。若要滿足必要條件，您必須擁有預先定義的索引，且欄位必須與外部資料來源中的欄位相符。如需建構索引的詳細資訊，請參閱[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)。

### 步驟 2：建立資料來源

索引子會從保有連接字串等資訊的**資料來源**提取資料。目前支援下列資料來源：

- [Azure SQL Database 或 Azure 虛擬機器中的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob 儲存體 (預覽)](search-howto-indexing-azure-blob-storage.md) 可用來擷取 PDF、Office 文件、HTML 或 XML 中的文字
- [Azure 表格儲存體 (預覽)](search-howto-indexing-azure-tables.md)

資料來源和使用資料來源的索引子是各自獨立設定與管理，這表示多個索引子可使用同一個資料來源來一次載入多個索引。

### 步驟 3：建立和排程索引子

索引子定義是指定索引、資料來源和排程的結構。索引子可以參考另一個服務的資料來源，只要該資料來源來自相同訂閱即可。如需建構索引的詳細資訊，請參閱[建立索引子 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn946899.aspx)。

## 後續步驟

既然您已瞭解基本概念，下一個步驟是檢閱需求和每個資料來源類型特有的工作。

- [Azure SQL Database 或 Azure 虛擬機器中的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob 儲存體 (預覽)](search-howto-indexing-azure-blob-storage.md) 可用來擷取 PDF、Office 文件、HTML 或 XML 中的文字
- [Azure 表格儲存體 (預覽)](search-howto-indexing-azure-tables.md)
- [使用 Azure 搜尋服務 Blob 索引子編製索引 CSV Blob (預覽)](search-howto-index-csv-blobs.md)
- [使用 Azure 搜尋服務 Blob 索引子編製索引 JSON Blob (預覽)](search-howto-index-json-blobs.md)

<!---HONumber=AcomDC_0810_2016-->