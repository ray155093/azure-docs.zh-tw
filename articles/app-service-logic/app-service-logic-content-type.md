---
title: Logic Apps 內容類型處理 | Microsoft Docs
description: 了解 Logic Apps 如何在設計階段與執行階段處理內容類型
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/03/2016
ms.author: jehollan

---
# Logic Apps 內容類型處理
有許多不同類型的內容可以在整個邏輯應用程式中流動 (包括 JSON、XML、一般檔案及二進位資料)儘管所有內容類型都受到支援，但有些是透過 Logic Apps 引擎進行原生了解，而有些則可能要視需要進行轉型或轉換。下列文章將說明引擎如何處理不同的內容類型，以及如何能夠視需要正確處理它們。

## Content-Type 標頭
從簡單的開始，讓我們看看下列這兩個 `Content-Types`，它們不需要任何轉換或轉型，就能在邏輯應用程式中使用 - `application/json` 和 `text/plain`。

### Application/json
工作流程引擎依賴來自 HTTP 呼叫的 `Content-Type` 標頭來決定適當的處理。內容類型為 `application/json` 的任何要求都會以 JSON 物件的形式儲存並加以處理。此外，JSON 內容預設就能剖析，不需要任何轉換。因此 content-type 標頭為 `application/json ` 的要求如下所示︰

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

可以在工作流程中使用運算式 (例如 `@body('myAction')['foo'][0]`) 進行剖析來取得值 (在此案例中為 `bar`)。不需要任何額外的轉換。如果您正在使用格式為 JSON 的資料，但並未指定標頭，則可使用 `@json()` 函數手動將它轉換為 JSON (例如︰`@json(triggerBody())['foo']`)。

### Text/plain
類似於 `application/json`，若所收到的 HTTP 訊息中 `Content-Type` 標頭為 `text/plain`，就會以它的原始格式來儲存。此外，如果要求包含於後續動作中而不需任何轉換，則它將會與 `Content-Type`: `text/plain` 標頭一同送出。例如，如果使用一般檔案，您可能會收到下列 HTTP 內容︰

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

(以 `text/plain` 形式)。如果在下一個動作中，您將它做為另一個要求的主體 (`@body('flatfile')`) 來傳送，則要求必須含有 `text/plain` Content-type 標頭。如果您正在使用格式為純文字的資料，但並未指定標頭，則可使用 `@string()` 函數手動將它轉換為文字 (例如︰`@string(triggerBody())`)。

### Application/xml 和 Application/octet-stream 以及轉換器函數
邏輯應用程式引擎一律會保留在 HTTP 要求或回應上接收到的 `Content-Type`。這表示如果收到 `Content-Type` 為 `application/octet-stream` 的內容，在後續不會執行轉換的動作中包括該內容將會產生含有 `Content-Type`: `application/octet-stream` 的要求。如此一來，引擎就可以保證資料在整個工作流程中移動時不會遺失。不過，當它流經整個工作流程時，動作狀態 (輸入及輸出) 會儲存於 JSON 物件中。這表示為了保留某些資料類型，引擎會將內容轉換為二進位 Base64 編碼的字串，並包含保留 `$content` 和 `$content-type` 的適當中繼資料 - 它將會自動轉換。您也可以使用內建的轉換函數，手動在內容類型之間進行轉換︰

* `@json()` - 將資料轉換為 `application/json`
* `@xml()` - 將資料轉換為 `application/xml`
* `@binary()` - 將資料轉換為 `application/octet-stream`
* `@string()` - 將資料轉換為 `text/plain`
* `@base64()` -將內容轉換為 Base64 字串
* `@base64toString()` - 將 Base64 編碼的字串轉換為 `text/plain`
* `@base64toBinary()` - 將 Base64 編碼的字串轉換為 `application/octet-stream`
* `@encodeDataUri()` - 將字串編碼為 dataUri 位元組陣列
* `@decodeDataUri()` - 將 dataUri 解碼為位元組陣列

例如，如果您收到的 HTTP 要求且 `Content-Type`: `application/xml` 如下︰

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

我可以轉換，並且可在稍後搭配像是 `@xml(triggerBody())` 的項目使用，或者在像是 `@xpath(xml(triggerBody()), '/CustomerName')` 的函數內使用。

### 其他內容類型
有其他內容類型也受到支援，而且可與邏輯應用程式一起運作，但是可能需要藉由解碼 `$content` 來手動擷取訊息本文。例如，如果我觸發了看起來如下的 `application/x-www-url-formencoded` 要求︰

```
CustomerName=Frank&Address=123+Avenue
```

因為這不是純文字或 JSON，所以它會以下列形式儲存於動作中︰

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

其中 `$content` 是編碼為 Base64 字串的承載，可保留所有資料。由於目前沒有任何適用於 form-data 的原生函數，我仍然可以透過使用類似 `@string(body('formdataAction'))` 的函數手動存取資料，在工作流程內使用此資料。如果我希望我的連出要求也具有 `application/x-www-url-formencoded` content-type 標頭，我可以直接將它新增到動作本文，而不需使用任何像是 `@body('formdataAction')` 的轉換。不過，唯有當 body 是 `body` 輸入中的唯一參數時，這才能運作。如果您嘗試在 `application/json` 要求內執行 `@body('formdataAction')`，將會發生執行階段錯誤，因為它將傳送編碼的內文。

<!---HONumber=AcomDC_0803_2016-->