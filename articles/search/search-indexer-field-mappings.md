---
title: "Azure 搜尋服務索引子中的欄位對應"
description: "設定 Azure 搜尋服務索引子欄位對應交代欄位名稱和資料表示的差異"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 57e91f070d9a42882a56e708f12b1ce238ed9191

---

# <a name="field-mappings-in-azure-search-indexers"></a>Azure 搜尋服務索引子中的欄位對應
使用 Azure 搜尋服務索引子時，您偶爾會發現置身於輸入資料不完全符合目標索引結構描述的情況中。 在這些情況下，您可以使用 **欄位對應** 將您的資料轉換成所需的形狀。

欄位對應在某些情況下很有用︰

* 您的資料來源有 `_id`欄位，但 Azure 搜尋服務不允許以底線開頭的欄位名稱。 欄位對應允許您「重新命名」欄位。
* 例如，您想要用相同的資料來源資料填入索引中的多個欄位，因為您想要將不同的分析器套用到這些欄位。 欄位對應讓您「分岔」資料來源欄位。
* 您必須以 Base64 格式編碼或解碼資料。 欄位對應支援數個 **對應函式**，包括 Base64 編碼和解碼的函式。   

## <a name="setting-up-field-mappings"></a>設定欄位對應
您可以在建立新的索引子時，使用 [建立索引子](https://msdn.microsoft.com/library/azure/dn946899.aspx) API 加入欄位對應。 您可以使用 [更新索引子](https://msdn.microsoft.com/library/azure/dn946892.aspx) API 管理編製索引子的欄位對應。

欄位對應包含 3 個部分︰

1. `sourceFieldName`，表示資料來源中的欄位。 這是必要屬性。
2. 選擇性的 `targetFieldName`，表示搜尋索引中的欄位。 如果省略，則會使用資料來源中的相同名稱。
3. 選擇性的 `mappingFunction`，可以使用數個預先定義的函式之一轉換您的資料。 函式的完整清單 [如下](#mappingFunctions)。

欄位對應會加入索引子定義上的 `fieldMappings` 陣列中。

例如，以下是容納欄位名稱差異的方式︰

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

索引子可以有多個欄位對應。 例如，以下是「分岔」欄位的方式︰

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" },
]
```

> [!NOTE]
> Azure 搜尋服務會使用不區分大小寫的比較，來解析欄位對應的欄位和函式名稱。 這很方便 (大小寫不需要完全正確)，但這表示資料來源或索引不能有只以大小寫區分的欄位。  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>欄位對應函式
目前支援的函式如下︰

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode"></a>base64Encode
執行輸入字串的安全 URL  Base64 編碼。 假設輸入以 UTF-8 編碼。

#### <a name="sample-use-case"></a>範例使用案例
Azure 搜尋服務文件索引鍵只顯示安全的 URL 字元 (因為，例如，客戶必須能夠使用查閱 API 處理文件)。 如果資料包含不安全的 URL 字元，而且您想要使用它填入搜尋索引中的索引鍵欄位，請使用這個函式。   

#### <a name="example"></a>範例
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Path",
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

<a name="base64DecodeFunction"></a>

### <a name="base64decode"></a>base64Decode
執行輸入字串的 Base64 解碼。 輸入假定為安全的 URL  Base64 編碼字串。

#### <a name="sample-use-case"></a>範例使用案例
Blob 的自訂中繼資料值必須以 ASCII 編碼。 您可以使用 Base64 編碼表現 Blob 自訂中繼資料中任意的 Unicode 字串。 不過，若要進行有意義的搜尋，您可以在填入搜尋索引時，使用這個函式將編碼的資料變回「一般」字串。  

#### <a name="example"></a>範例
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" }
  }]
```

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition"></a>extractTokenAtPosition
使用指定的分隔符號分割字串欄位，並在分割結果的指定位置挑選權杖。

例如，如果輸入是 `Jane Doe`，而 `delimiter` 是 `" "` (空格) 且 `position` 為 0，則結果是 `Jane`；如果 `position` 為 1，則結果是 `Doe`。 如果位置參考不存在的權杖，則會傳回錯誤。

#### <a name="sample-use-case"></a>範例使用案例
資料來源包含 `PersonName` 欄位，而您想要將它編製為 `FirstName` 和 `LastName` 兩個不同欄位的索引。 您可以使用這個函式來分割以空格字元作為分隔符號的輸入。

#### <a name="parameters"></a>參數
* `delimiter`︰分割輸入字串時，用為分隔符號的字串。
* `position`：分割輸入字串後，要挑選的權杖以零為基底位置的整數。    

#### <a name="example"></a>範例
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
將格式化為字串 JSON 陣列的字串，轉換為可用來填入索引中 `Collection(Edm.String)` 欄位的字串陣列。

例如，若輸入字串是 `["red", "white", "blue"]`，則 `Collection(Edm.String)` 類型的目標欄位會填入下列三個值：`red`、`white` 與 `blue`。 若為無法剖析為 JSON 字串陣列的輸入值，會傳回錯誤。

#### <a name="sample-use-case"></a>範例使用案例
Azure SQL 資料庫沒有內建資料類型，不能自然對應至 Azure 搜尋服務的 `Collection(Edm.String)` 欄位。 若要填入字串集合欄位，請將來源資料格式化為 JSON 字串陣列，並使用這個函式。

#### <a name="example"></a>範例
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```

## <a name="help-us-make-azure-search-better"></a>協助我們改進 Azure 搜尋服務
如果您有功能要求或改進的想法，請在我們的 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)與我們連絡。



<!--HONumber=Nov16_HO3-->


