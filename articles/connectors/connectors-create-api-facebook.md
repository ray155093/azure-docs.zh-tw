<properties
    pageTitle="在您的 Logic Apps 中新增 Facebook 連接器 | Microsoft Azure"
    description="搭配 REST API 參數來使用 Facebook 連接器的概觀"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# 開始使用 Facebook 連接器
連線到 Facebook 並張貼在動態時報上、取得頁面摘要等等。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。


您可以利用 Facebook 來：

- 根據您從 Facebook 所取得的資料，來建置您的商務流程。
- 在接收到新貼文時使用觸發程序。
- 使用會張貼到您的動態時報、取得頁面摘要等等的動作。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當您的動態時報上有新貼文時，您可以取得該貼文，然後把它推送到您的 Twitter 摘要。



如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Facebook 連接器包含下列觸發程序及動作。

| 觸發程序 | 動作|
| --- | --- |
| <ul><li>當我的動態時報上有新貼文時</li></ul> |<ul><li>取得我動態時報的摘要</li><li>張貼到我的動態時報上</li><li>當我的動態時報上有新貼文時</li><li>取得頁面摘要</li><li>取得使用者的動態時報</li><li>張貼到頁面上</li></ul>

所有連接器都支援 JSON 和 XML 格式的資料。

## 建立至 Facebook 的連線
當您將這個連接器新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Facebook。

1. 登入您的 Facebook 帳戶。
2. 選取 [授權]，然後允許您的邏輯應用程式連線並使用您的 Facebook。

>[AZURE.INCLUDE [建立至 Facebook 連線的步驟](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的 Facebook 連線。

## Swagger REST API 參考
適用的版本：1.0。

### 取得我動態時報的摘要
取得已登入使用者之動態時報的摘要。```GET: /me/feed```

| 名稱|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|fields|string|no|query|無 |指定要傳回的欄位。例如：識別碼、名稱、圖片。|
|limit|integer|no|query| 無|要擷取的貼文數目上限|
|取代為|string|no|query| 無|將貼文清單限制為只包含擁有位置資訊的貼文。|
|filter|string|no|query| 無|只擷取符合特定串流篩選器的貼文。|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 張貼到我的動態時報上
將近況訊息張貼到已登入使用者的動態時報上。```POST: /me/feed```

| Name|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|post|string |yes|body|無 |要張貼的新訊息|

#### Response
|Name|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 當我的動態時報上有新貼文時
當已登入使用者的動態時報上有新貼文時，就會觸發某個新流程。```GET: /trigger/me/feed```

沒有參數。

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 取得頁面摘要
取得指定頁面摘要中的貼文。```GET: /{pageId}/feed```

| 名稱|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|pageId|string|yes|路徑| 無|要擷取之貼文所在頁面的識別碼。|
|limit|integer|no|query| 無|要擷取的貼文數目上限|
|include\_hidden|布林值|no|query|無 |是否要包含頁面所隱藏的貼文|
|fields|string|no|query|無 |指定要傳回的欄位。例如：識別碼、名稱、圖片。|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 取得使用者的動態時報
取得使用者的動態時報上的貼文。```GET: /{userId}/feed```

| Name|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userId|string|yes|路徑|無 |要遭到擷取之動態時報擁有者的使用者識別碼。|
|limit|integer|no|query|無 |要擷取的貼文數目上限|
|取代為|string|no|query|無 |將貼文清單限制為只包含擁有位置資訊的貼文。|
|filter|string|no|query| 無|只擷取符合特定串流篩選器的貼文。|
|fields|string|no|query| 無|指定要傳回的欄位。例如：識別碼、名稱、圖片。|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 張貼到頁面上
以已登入使用者的身分，將訊息張貼在 Facebook 頁面上。```POST: /{pageId}/feed```

| 名稱|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|pageId|string|yes|路徑|無 |要張貼貼文之頁面的識別碼。|
|post|多種 |yes|body|無 |要張貼的新訊息。|

#### Response
|Name|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## 物件定義

#### GetFeedResponse

|屬性名稱 | 資料類型 | 必要|
|---|---|---|
|data|array|no|

#### TriggerFeedResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|data|array|no|

#### PostItem：個人檔案摘要中的單一項目
個人檔案可能是使用者、頁面、應用程式或群組。

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|string|no|
|admin\_creator|array|no|
|caption|string|no|
|created\_time|string|no|
|說明|string|no|
|feed\_targeting|沒有定義|no|
|from|沒有定義|no|
|icon|string|no|
|is\_hidden|布林值|no|
|is\_published|布林值|no|
|link|string|no|
|訊息|string|no|
|名稱|string|no|
|object\_id|string|no|
|picture|string|no|
|place|沒有定義|no|
|privacy|沒有定義|no|
|properties|array|no|
|來源|string|no|
|status\_type|string|no|
|story|string|no|
|targeting|沒有定義|no|
|to|array|no|
|類型|string|no|
|updated\_time|string|no|
|with\_tags|沒有定義|no|

#### TriggerItem：個人檔案摘要中的單一項目
個人檔案可能是使用者、頁面、應用程式或群組。

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|string|no|
|created\_time|string|no|
|from|沒有定義|no|
|訊息|string|no|
|類型|string|no|

#### AdminItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|string|no|
|link|string|no|

#### PropertyItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|名稱|string|no|
|文字|string|no|
|href|string|no|

#### UserPostFeedRequest

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|訊息|string|yes|
|link|string|no|
|picture|string|no|
|名稱|string|no|
|caption|string|no|
|說明|string|no|
|place|string|no|
|tags|string|no|
|privacy|沒有定義|no|
|object\_attachment|string|no|

#### PagePostFeedRequest

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|訊息|string|yes|
|link|string|no|
|picture|string|no|
|名稱|string|no|
|caption|string|no|
|說明|string|no|
|actions|array|no|
|place|string|no|
|tags|string|no|
|object\_attachment|string|no|
|targeting|沒有定義|no|
|feed\_targeting|沒有定義|no|
|published|布林值|no|
|scheduled\_publish\_time|string|no|
|backdated\_time|string|no|
|backdated\_time\_granularity|string|no|
|child\_attachments|array|no|
|multi\_share\_end\_card|布林值|no|

#### PostFeedResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|string|no|

#### ProfileCollection

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|data|array|no|

#### UserItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|string|no|
|first\_name|string|no|
|last\_name|string|no|
|名稱|string|no|
|gender|string|no|
|about|string|no|

#### ActionItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|名稱|string|no|
|link|string|no|

#### TargetItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|countries|array|no|
|locales|array|no|
|regions|array|no|
|cities|array|no|

#### FeedTargetItem：控制以此貼文為目標之新聞摘要的物件
這些群組中所有人看到此貼文的機會比較高，其他人看到的機會則比較低。僅適用於頁面。

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|countries|array|no|
|regions|array|no|
|cities|array|no|
|age\_min|integer|no|
|age\_max|integer|no|
|genders|array|no|
|relationship\_statuses|array|no|
|interested\_in|array|no|
|college\_years|array|no|
|interests|array|no|
|relevant\_until|integer|no|
|education\_statuses|array|no|
|locales|array|no|

#### PlaceItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|string|no|
|名稱|string|no|
|overall\_rating|number|no|
|location|沒有定義|no|

#### LocationItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|city|string|no|
|country|string|no|
|緯度|number|no|
|located\_in|string|no|
|經度|number|no|
|名稱|string|no|
|region|string|no|
|state|string|no|
|street|string|no|
|zip|string|no|

#### PrivacyItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|說明|string|no|
|value|string|yes|
|allow|string|no|
|deny|string|no|
|friends|string|no|

#### ChildAttachmentsItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|link|string|no|
|picture|string|no|
|image\_hash|string|no|
|名稱|string|no|
|說明|string|no|

#### PostPhotoRequest

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|url|string|yes|
|caption|string|no|

#### PostPhotoResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|string|yes|
|post\_id|string|yes|

#### PostVideoRequest

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|videoData|string|yes|
|說明|string|yes|
|title|string|yes|
|uploadedVideoName|string|no|

#### GetPhotoResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|data|沒有定義|yes|

#### GetPhotoResponseItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|url|string|yes|
|is\_silhouette|布林值|yes|
|height|string|no|
|width|string|no|

#### GetEventResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|data|array|yes|

#### GetEventResponseItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|string|yes|
|名稱|string|yes|
|start\_time|string|no|
|end\_time|string|no|
|timezone|string|no|
|location|string|no|
|說明|string|no|
|ticket\_uri|string|no|
|rsvp\_status|string|yes|


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0824_2016-->