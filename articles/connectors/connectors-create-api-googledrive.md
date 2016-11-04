---
title: 在邏輯應用程式中新增 Google 雲端硬碟連接器 | Microsoft Docs
description: 搭配 REST API 參數來使用 Google 雲端硬碟連接器的概觀
services: ''
suite: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia

---
# 開始使用 Google 雲端硬碟連接器
連線到 Google 雲端硬碟來建立檔案、取得資料列等等。您可以利用 Google 雲端硬碟來：

* 根據您透過搜尋所取得的資料，來建置您的商務流程。
* 使用動作來搜尋圖像、搜尋新聞等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，您可以搜尋某支影片，然後利用 Twitter 把該影片張貼在某個 Twitter 摘要上。

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Google 雲端硬碟包含下列動作，但不包含觸發程序。

| 觸發程序 | 動作 |
| --- | --- |
| None |<ul><li>建立檔案</li><li>插入資料列</li><li>複製檔案</li><li>刪除檔案</li><li>刪除資料列</li><li>將封存檔案解壓縮到資料夾</li><li>使用識別碼來取得檔案內容</li><li>使用路徑來取得檔案內容</li><li>使用識別碼來取得檔案中繼資料</li><li>使用路徑來取得檔案中繼資料</li><li>取得資料列</li><li>更新檔案</li><li>更新資料列</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。

## 建立至 Google 雲端硬碟的連線
當您將這個 API 新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Google 雲端硬碟。

> [!INCLUDE [建立至 googledrive 連線的步驟](../../includes/connectors-create-api-googledrive.md)]
> 
> 

當您建立連線之後，請輸入 Google 雲端硬碟的屬性，例如資料夾路徑或檔案名稱。本主題的 **REST API 參考**一節說明這些屬性。

> [!TIP]
> 您可以在其他的邏輯應用程式中，使用這個相同的 Google 雲端硬碟連線。
> 
> 

## Swagger REST API 參考
適用的版本：1.0。

### 建立檔案
把檔案上傳到 Google 雲端硬碟。```POST: /datasets/default/files```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |yes |query |無 |用來把檔案上傳到 Google 雲端硬碟的資料夾路徑 |
| 名稱 |string |yes |query |無 |要在 Google 雲端硬碟中建立之檔案的名稱 |
| body |字串 (二進位) |yes |body |無 |要上傳到 Google 雲端硬碟之檔案的內容 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 插入資料列
在 Google 工作表中插入資料列。```POST: /datasets/{dataset}/tables/{table}/items```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |Google 試算表檔案的唯一識別碼 |
| 資料表 |string |yes |路徑 |無 |工作表的唯一識別碼。 |
| item |ItemInternalId：字串 |yes |body |無 |要在指定工作表中插入的資料列 |

#### Response
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 複製檔案
複製 Google 雲端硬碟上的檔案。```POST: /datasets/default/copyFile```

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 來源 |string |yes |query |無 |來源檔案的 URL |
| 目的地 |string |yes |query |無 |Google 雲端硬碟中的目的檔案路徑，包括目標檔案名稱 |
| overwrite |布林值 |no |query |無 |如果設定為「True」，則會覆寫目的檔案 |

#### Response
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 刪除檔案
刪除 Google 雲端硬碟中的檔案。```DELETE: /datasets/default/files/{id}```

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |string |yes |路徑 |無 |要從 Google 雲端硬碟刪除之檔案的唯一識別碼 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 刪除資料列
刪除 Google 工作表中的資料列。```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |Google 試算表檔案的唯一識別碼 |
| 資料表 |string |yes |路徑 |無 |工作表的唯一識別碼。 |
| id |string |yes |路徑 |無 |要刪除的資料列的唯一識別碼 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 將封存檔案解壓縮到資料夾
將封存檔案 (例如 .zip) 解壓縮到 Google 雲端硬碟中的資料夾。```POST: /datasets/default/extractFolderV2```

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 來源 |string |yes |query |無 |封存檔案的路徑 |
| 目的地 |string |yes |query |無 |用來把封存檔案內容解壓縮到 Google 雲端硬碟中的路徑 |
| overwrite |布林值 |no |query |無 |如果設定為「True」，則會覆寫目的檔案 |

#### Response
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 使用識別碼來取得檔案內容
使用識別碼來擷取 Google 雲端硬碟中的檔案內容。```GET: /datasets/default/files/{id}/content```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |string |yes |路徑 |無 |要從 Google 雲端硬碟擷取之檔案的唯一識別碼 |

#### Response
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 使用路徑來取得檔案內容
使用路徑來擷取 Google 雲端硬碟中的檔案內容。```GET: /datasets/default/GetFileContentByPath```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 路徑 |string |yes |query |無 |Google 雲端硬碟中檔案的路徑。 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 使用識別碼來取得檔案中繼資料
使用識別碼來擷取 Google 雲端硬碟中的檔案中繼資料。```GET: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |string |yes |路徑 |無 |Google 雲端硬碟中檔案的唯一識別碼 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 使用路徑來取得檔案中繼資料
使用路徑來擷取 Google 雲端硬碟中的檔案中繼資料。```GET: /datasets/default/GetFileByPath```

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 路徑 |string |yes |query |無 |Google 雲端硬碟中檔案的路徑。 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 取得單一資料列
擷取 Google 工作表中的單一資料列。```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |Google 試算表檔案的唯一識別碼 |
| 資料表 |string |yes |路徑 |無 |工作表的唯一識別碼。 |
| id |string |yes |路徑 |無 |要擷取的資料列的唯一識別碼 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 更新檔案
更新 Google 雲端硬碟中的檔案。```PUT: /datasets/default/files/{id}```

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |string |yes |路徑 |無 |Google 雲端硬碟中要更新之檔案的唯一識別碼 |
| body |字串 (二進位) |yes |body |無 |要上傳到 Google 雲端硬碟之檔案的內容 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### 更新資料列
更新 Google 工作表中的資料列。```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |Google 試算表檔案的唯一識別碼 |
| 資料表 |string |yes |路徑 |無 |工作表的唯一識別碼。 |
| id |string |yes |路徑 |無 |要更新的資料列的唯一識別碼 |
| item |ItemInternalId：字串 |yes |body |無 |值已更新的資料列 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## 物件定義
#### DataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 表格式 |沒有定義 |no |
| blob |沒有定義 |no |

#### TabularDataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 來源 |string |no |
| displayName |string |no |
| urlEncoding |string |no |
| tableDisplayName |string |no |
| tablePluralName |string |no |

#### BlobDataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 來源 |string |no |
| displayName |string |no |
| urlEncoding |string |no |

#### BlobMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 識別碼 |string |no |
| 名稱 |string |no |
| DisplayName |string |no |
| Path |string |no |
| LastModified |string |no |
| 大小 |integer |no |
| MediaType |string |no |
| IsFolder |布林值 |no |
| ETag |string |no |
| FileLocator |string |no |

#### TableMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |string |no |
| title |string |no |
| x-ms-permission |string |no |
| 結構描述 |沒有定義 |no |

#### TablesList
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| value |array |no |

#### 資料表
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |string |no |
| DisplayName |string |no |

#### 項目
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| ItemInternalId |string |no |

#### ItemsList
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| value |array |no |

## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

返回 [API 清單](apis-list.md)。

<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0824_2016-->