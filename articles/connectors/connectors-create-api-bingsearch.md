---
title: "新增 Bing 搜尋連接器邏輯應用程式 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Bing 搜尋連接器的概觀"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: a7f530e8-1573-4612-8899-c9c84aa2de6d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: dbb920f212d46365233e83ba1e0a0ae99917e405


---
# <a name="get-started-with-the-bing-search-connector"></a>開始使用 Bing 搜尋連接器
連線到 Bing 搜尋來搜尋新聞、搜尋影片等等。 您可以利用 Bing 搜尋來： 

* 根據您透過搜尋所取得的資料，來建置您的商務流程。 
* 使用動作來搜尋圖像、搜尋新聞等等。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，您可以搜尋某支影片，然後利用 Twitter 把該影片張貼在某個 Twitter 摘要上。

如要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Bing 搜尋包含下列動作， 但不包含觸發程序。 

| 觸發程序 | 動作 |
| --- | --- |
| None |<ul><li>搜尋網站</li><li>搜尋影片</li><li>搜尋圖像</li><li>搜尋新聞</li><li>搜尋相關結果</li><li>搜尋拼字</li><li>搜尋所有項目</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參考
適用的版本：1.0。

### <a name="search-web"></a>搜尋網站
擷取某次 Bing 搜尋結果中的網站。  
```GET: /Web```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要搜尋的文字 (例如「xbox」) |
| maxResult |integer |no |query |None |要傳回的結果數目上限 |
| startOffset |integer |no |query |None |要略過的結果數目 |
| adultContent |字串 |no |query |None |成人內容篩選器。 有效值： <ul><li>關閉</li><li>中度</li><li>Strict</li></ul> |
| market |字串 |no |query |None |用來縮小搜尋範圍的市場或區域 (例如：en-US) |
| 經度 |number |no |query |None |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450) |
| 緯度 |number |no |query |None |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696) |
| webFileType |字串 |no |query |None |用來縮小搜尋範圍的檔案類型 (例如：「DOC」) |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="search-videos"></a>搜尋影片
擷取某次 Bing 搜尋結果中的影片。  
```GET: /Video```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要搜尋的文字 (例如「xbox」) |
| maxResult |integer |no |query |None |要傳回的結果數目上限 |
| startOffset |integer |no |query |None |要略過的結果數目 |
| adultContent |字串 |no |query |None |成人內容篩選器。 有效值： <ul><li>關閉</li><li>中度</li><li>Strict</li></ul> |
| market |字串 |no |query |None |用來縮小搜尋範圍的市場或區域 (例如：en-US) |
| 經度 |number |no |query |None |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450) |
| 緯度 |number |no |query |None |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696) |
| videoFilters |字串 |no |query |None |根據影片的大小、外觀比例、色彩、樣式、方向，或上述條件的任何組合來篩選搜尋。  有效值： <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>例如：'Duration:Short+Resolution:High' |
| videoSortBy |字串 |no |query |無 |搜尋結果的排序次序。 有效值： <ul><li>日期</li><li>相關性</li></ul> <p>日期的排序次序必須為遞減。</p> |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="search-images"></a>搜尋圖像
擷取某次 Bing 搜尋結果中的圖像。  
```GET: /Image```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要搜尋的文字 (例如「xbox」) |
| maxResult |integer |no |query |None |要傳回的結果數目上限 |
| startOffset |integer |no |query |None |要略過的結果數目 |
| adultContent |字串 |no |query |None |成人內容篩選器。 有效值： <ul><li>關閉</li><li>中度</li><li>Strict</li></ul> |
| market |字串 |no |query |None |用來縮小搜尋範圍的市場或區域 (例如：en-US) |
| 經度 |number |no |query |None |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450) |
| 緯度 |number |no |query |None |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696) |
| imageFilters |字串 |no |query |None |根據影片的大小、外觀比例、色彩、樣式、方向，或上述條件的任何組合來篩選搜尋。 有效值： <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>例如：'Size:Small+Aspect:Square' |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="search-news"></a>搜尋新聞
擷取某次 Bing 搜尋結果中的新聞。  
```GET: /News```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要搜尋的文字 (例如「xbox」) |
| maxResult |integer |no |query |None |要傳回的結果數目上限 |
| startOffset |integer |no |query |None |要略過的結果數目 |
| adultContent |字串 |no |query |None |成人內容篩選器。 有效值： <ul><li>關閉</li><li>中度</li><li>Strict</li></ul> |
| market |字串 |no |query |None |用來縮小搜尋範圍的市場或區域 (例如：en-US) |
| 經度 |number |no |query |None |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450) |
| 緯度 |number |no |query |None |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696) |
| newsSortBy |字串 |no |query |無 |搜尋結果的排序次序。 有效值： <ul><li>日期</li><li>相關性</li></ul> <p>日期的排序次序必須為遞減。</p> |
| newsCategory |字串 |no |query | |用來縮小搜尋範圍的新聞類別 (例如：「rt_Business」) |
| newsLocationOverride |字串 |no |query |None |覆寫 Bing 位置偵測的結果。 此參數僅適用於 en-US 市場。 輸入的格式為 US./<state /> (例如：'US.WA') |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="search-spellings"></a>搜尋拼字
擷取拼字建議。  
```GET: /SpellingSuggestions```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要搜尋的文字 (例如「xbox」) |
| maxResult |integer |no |query |None |要傳回的結果數目上限 |
| startOffset |integer |no |query |None |要略過的結果數目 |
| adultContent |字串 |no |query |None |成人內容篩選器。 有效值： <ul><li>關閉</li><li>中度</li><li>Strict</li></ul> |
| market |字串 |no |query |None |用來縮小搜尋範圍的市場或區域 (例如：en-US) |
| 經度 |number |no |query |None |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450) |
| 緯度 |number |no |query |None |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696) |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="search-related"></a>搜尋相關結果
擷取某次 Bing 搜尋結果中的相關搜尋結果。  
```GET: /RelatedSearch```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要搜尋的文字 (例如「xbox」) |
| maxResult |integer |no |query |None |要傳回的結果數目上限 |
| startOffset |integer |no |query |None |要略過的結果數目 |
| adultContent |字串 |no |query |None |成人內容篩選器。 有效值： <ul><li>關閉</li><li>中度</li><li>Strict</li></ul> |
| market |字串 |no |query |None |用來縮小搜尋範圍的市場或區域 (例如：en-US) |
| 經度 |number |no |query |None |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450) |
| 緯度 |number |no |query |None |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696) |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="search-all"></a>搜尋所有項目
擷取某次 Bing 搜尋結果中的所有網站、視訊、圖像等等。  
```GET: /CompositeSearch```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要搜尋的文字 (例如「xbox」) |
| maxResult |integer |no |query |None |要傳回的結果數目上限 |
| startOffset |integer |no |query |None |要略過的結果數目 |
| adultContent |字串 |no |query |None |成人內容篩選器。 有效值： <ul><li>關閉</li><li>中度</li><li>Strict</li></ul> |
| market |字串 |no |query |None |用來縮小搜尋範圍的市場或區域 (例如：en-US) |
| 經度 |number |no |query |None |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450) |
| 緯度 |number |no |query |None |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696) |
| webFileType |字串 |no |query |None |用來縮小搜尋範圍的檔案類型 (例如：「DOC」) |
| videoFilters |字串 |no |query |None |根據影片的大小、外觀比例、色彩、樣式、方向，或上述條件的任何組合來篩選搜尋。  有效值： <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>例如：'Duration:Short+Resolution:High' |
| videoSortBy |字串 |no |query |無 |搜尋結果的排序次序。 有效值： <ul><li>日期</li><li>相關性</li></ul> <p>日期的排序次序必須為遞減。</p> |
| imageFilters |字串 |no |query |None |根據影片的大小、外觀比例、色彩、樣式、方向，或上述條件的任何組合來篩選搜尋。 有效值： <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>例如：'Size:Small+Aspect:Square' |
| newsSortBy |字串 |no |query |無 |搜尋結果的排序次序。 有效值： <ul><li>日期</li><li>相關性</li></ul> <p>日期的排序次序必須為遞減。</p> |
| newsCategory |字串 |no |query |無 |用來縮小搜尋範圍的新聞類別 (例如：「rt_Business」) |
| newsLocationOverride |字串 |no |query |None |覆寫 Bing 位置偵測的結果。 此參數僅適用於 en-US 市場。 輸入的格式為 US./<state /> (例如：'US.WA') |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
#### <a name="webresultmodel-bing-web-search-results"></a>WebResultModel：Bing 的網頁搜尋結果
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 課程名稱 |字串 |no |
| 說明 |字串 |no |
| DisplayUrl |字串 |no |
| 識別碼 |字串 |no |
| FullUrl |字串 |no |

#### <a name="videoresultmodel-bing-video-search-results"></a>VideoResultModel：Bing 的影片搜尋結果
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 課程名稱 |字串 |no |
| DisplayUrl |字串 |no |
| 識別碼 |字串 |no |
| MediaUrl |字串 |no |
| 執行階段 |integer |no |
| 縮圖 |沒有定義 |no |

#### <a name="thumbnailmodel-thumbnail-properties-of-the-multimedia-element"></a>ThumbnailModel：多媒體元素的縮圖屬性
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| MediaUrl |字串 |no |
| ContentType |字串 |no |
| 寬度 |integer |no |
| 高度 |integer |no |
| FileSize |integer |no |

#### <a name="imageresultmodel-bing-image-search-results"></a>ImageResultModel：Bing 的圖像搜尋結果
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 課程名稱 |字串 |no |
| DisplayUrl |字串 |no |
| 識別碼 |字串 |no |
| MediaUrl |字串 |no |
| SourceUrl |字串 |no |
| 縮圖 |沒有定義 |no |

#### <a name="newsresultmodel-bing-news-search-results"></a>NewsResultModel：Bing 的新聞搜尋結果
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 課程名稱 |字串 |no |
| 說明 |字串 |no |
| DisplayUrl |字串 |no |
| 識別碼 |字串 |no |
| 來源 |字串 |no |
| Date |字串 |no |

#### <a name="spellresultmodel-bing-spelling-suggestions-results"></a>SpellResultModel：Bing 的拼字建議結果
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| 值 |字串 |no |

#### <a name="relatedsearchresultmodel-bing-related-search-results"></a>RelatedSearchResultModel：Bing 的相關搜尋結果
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 課程名稱 |字串 |no |
| 識別碼 |字串 |no |
| BingUrl |字串 |no |

#### <a name="compositesearchresultmodel-bing-composite-search-results"></a>CompositeSearchResultModel：Bing 的綜合搜尋結果
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| WebResultsTotal |integer |no |
| ImageResultsTotal |integer |no |
| VideoResultsTotal |integer |no |
| NewsResultsTotal |integer |no |
| SpellSuggestionsTotal |integer |no |
| WebResults |array |no |
| ImageResults |array |no |
| VideoResults |array |no |
| NewsResults |array |no |
| SpellSuggestionResults |array |no |
| RelatedSearchResults |array |no |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

返回 [API 清單](apis-list.md)。




<!--HONumber=Jan17_HO3-->


