---
title: 使用 Azure 搜尋服務 Blob 索引子編製索引 JSON Blob
description: 使用 Azure 搜尋服務 Blob 索引子編製索引 JSON Blob
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''

ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/26/2016
ms.author: eugenesh

---
# 使用 Azure 搜尋服務 Blob 索引子編製索引 JSON Blob
本文說明如何設定 Azure 搜尋服務 Blob 索引子，從包含 JSON 的 Blob 中擷取結構化的內容。

## 案例
根據預設，[Azure 搜尋服務 Blob 索引子](search-howto-indexing-azure-blob-storage.md)會將 JSON Blob 剖析為單一的文字區塊。通常，您會想要保留 JSON 文件的結構。例如，假設 JSON 文件

    { 
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

您可以將它剖析成包含 "text"、"datePublished" 與 "tags" 欄位的 Azure 搜尋服務文件。

或者，當您的 Blob 包含 **JSON 物件的陣列**時，您可能希望陣列的各個元素成為個別的 Azure 搜尋服務文件。例如，提供 Blob 此 JSON：

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

您可以將 Azure 搜尋服務索引填入 3 個不同的文件，每個都具有「識別碼」和「文字」欄位。

> [!IMPORTANT]
> 這項功能目前為預覽版本。僅適用於使用 **2015-02-28-Preview** 版本的 REST API。請記住，預覽 API 是針對測試與評估，不應該用於生產環境。
> 
> 

## 設定 JSON 編製索引
若要編製 JSON Blob 的索引，請將 `parsingMode` 組態參數設定為 `json` (將每個 Blob 以單一文件的方式編製索引) 或 `jsonArray` (如果您的 Blob 包含 JSON 陣列)：

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

如果需要，請使用**欄位對應**來挑選用來填入您目標搜尋索引的來源 JSON 文件屬性。以下有更詳細的描述。

> [!IMPORTANT]
> 當您使用 `json` 或 `jsonArray` 剖析模式時，Azure 搜尋服務會假設您資料來源中的所有 Blob 都是 JSON。如果您需要支援在相同的資料來源中混用 JSON 和非 JSON Blob，請在 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)上讓我們知道。
> 
> 

## 使用欄位對應建置搜尋文件
目前，Azure 搜尋服務無法直接編製索引任意 JSON 文件，因為它只支援基本資料類型、字串陣列和 GeoJSON 點。不過，您可以使用**欄位對應**挑選 JSON 文件的幾個部分，並將它們「上移」到搜尋文件的最上層欄位。若要了解欄位對應基礎概念，請參閱 [Azure 搜尋服務索引子欄位對應會橋接資料來源和搜尋索引之間的差異](search-indexer-field-mappings.md)。

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

對應中的來源欄位名稱是使用 [JSON 指標](http://tools.ietf.org/html/rfc6901)標記法指定。以正斜線開始參考 JSON 文件的根目錄，然後使用正斜線分隔的路徑向下切入所需的屬性 (使用任意層級的巢狀結構)。

您也可以使用以零為起始的索引來參考個別陣列元素。比方說，若要從上述範例挑選 "tags" 陣列的第一個元素，請如下所示使用欄位對應︰

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 如果欄位對應路徑中的來源欄位名稱參考在 JSON 中不存在的屬性，則會略過該對應且不會產生錯誤。這麼做是為了讓我們可支援具有不同結構描述 (這是常見的使用案例) 的文件。因為沒有任何驗證，您必須小心避免在欄位對應規格中出現錯字。
> 
> 

如果您的 JSON 文件只包含簡單的最上層屬性，可能完全不需要欄位對應。比方說，如果您的 JSON 如下所示，最上層的屬性 "text"、"datePublished" 和 "標記" 會直接對應到搜尋索引中的對應欄位 ︰

    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
     }

## 編製巢狀 JSON 陣列索引
如果您想要為 JSON 物件陣列編製索引，但是該陣列以巢狀方式位於文件中的某處？ 您可以使用 `documentRoot` 組態屬性選擇哪一個屬性包含陣列。例如，如果您的 Blob 看起來像這樣︰

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

您可以使用這個組態為 "level2" 屬性中包含的陣列編製索引：

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


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

<!---HONumber=AcomDC_0727_2016-->