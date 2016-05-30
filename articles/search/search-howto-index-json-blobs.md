<properties
pageTitle="使用 Azure 搜尋服務 Blob 索引子編製索引 JSON Blob"
description="了解如何使用 Azure 搜尋服務編製索引 JSON Blob"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="04/20/2016"
ms.author="eugenesh" />

# 使用 Azure 搜尋服務 Blob 索引子編製索引 JSON Blob 

根據預設，[Azure 搜尋服務 Blob 索引子](search-howto-indexing-azure-blob-storage.md)會將 JSON Blob 剖析為單一的文字區塊。通常，您會想要保留 JSON 文件的結構。例如，假設 JSON 文件

	{ 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

您可以將它剖析成搜尋索引中的 "text"、"datePublished" 和 "tags" 欄位。

本文說明如何設定 Azure 搜尋服務 Blob 索引子以進行 JSON 剖析。編製索引快樂！

> [AZURE.IMPORTANT] 這項功能目前為預覽版本。僅適用於使用 **2015-02-28-Preview** 版本的 REST API。請記住，預覽 API 是針對測試與評估，不應該用於生產環境。

## 設定 JSON 編製索引

若要編製索引 JSON Blob，請在「JSON 剖析」模式中使用 Blob 索引子。在索引子定義的 `parameters` 屬性啟用 `useJsonParser` 組態設定︰

	{
	  "name" : "my-json-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "useJsonParser" : true } }
	}

如果需要，請使用**欄位對應**來挑選用來填入您目標搜尋索引的來源 JSON 文件屬性。以下有更詳細的描述。

> [AZURE.IMPORTANT] 當您使用 JSON 剖析模式時，Azure 搜尋服務會假設您的資料來源中所有 Blob 都為 JSON。如果您需要支援在相同的資料來源中混用 JSON 和非 JSON Blob，請在 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)讓我們知道。

## 使用欄位對應建置搜尋文件 

目前，Azure 搜尋服務無法直接編製索引任意 JSON 文件，因為它只支援基本資料類型、字串陣列和 GeoJSON 點。不過，您可以使用**欄位對應**挑選 JSON 文件的部分，並將它們「抬」到搜尋文件的最上層欄位。若要了解欄位對應基礎概念，請參閱 [Azure 搜尋服務索引子欄位對應會橋接資料來源和搜尋索引之間的差異](search-indexer-field-mappings.md)。

回到我們的範例 JSON 文件︰

    { 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

假設您的搜尋索引有下列欄位︰Edm.String 類型的 `text`、Edm.DateTimeOffset 類型的 `date`，以及 Collection(Edm.String) 類型的 `tags`。若要將 JSON 對應到所需形狀，請使用下列欄位對應︰

	"fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	]

使用 [JSON 指標](http://tools.ietf.org/html/rfc6901)標記法指定對應中的來源欄位名稱。以正斜線開始參考 JSON 文件的根目錄，然後使用正斜線分隔的路徑向下切入所需的屬性 (使用任意層級的巢狀結構)。

您也可以使用以零為起始的索引來參考個別陣列元素。比方說，若要從上述範例挑選 "tags" 陣列的第一個元素，請如下所示使用欄位對應︰

	{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] 如果欄位對應路徑中的來源欄位名稱參考在 JSON 中不存在的屬性，則會略過該對應且不會產生錯誤。這麼做是為了讓我們可支援具有不同結構描述 (這是常見的使用案例) 的文件。因為沒有任何驗證，您必須小心避免在欄位對應規格中出現錯字。

如果您的 JSON 文件只包含簡單的最上層屬性，可能完全不需要欄位對應。比方說，如果您的 JSON 如下所示，最上層的屬性 "text"、"datePublished" 和 "標記" 會直接對應到搜尋索引中的對應欄位 ︰
 
	{ 
	   "text" : "A hopefully useful article explaining how to parse JSON blobs",
	   "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
 	}

> [AZURE.NOTE] Azure 搜尋服務目前僅支援將一個 JSON Blob 剖析成一個搜尋文件。如果您的 Blob 包含您想要剖析成多個搜尋文件的 JSON 陣列，請投票支持[這項 UserVoice 建議](https://feedback.azure.com/forums/263029-azure-search/suggestions/13431384-parse-blob-containing-a-json-array-into-multiple-d)，協助我們確定這項工作的優先順序。

## 要求範例

總而言之，以下是完整的裝載範例。

資料來源：

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "optional, my-folder" }
	}   

索引子：

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-json-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	  ]
	}

## 協助我們改進 Azure 搜尋服務

如果您有功能要求或改進的想法，請在我們的 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)與我們連絡。

<!---HONumber=AcomDC_0518_2016-->