---
title: "在邏輯應用程式中新增 Google 雲端硬碟連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Google 雲端硬碟連接器的概觀"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 497f963870d24a335379a7f015a515c425737a73


---
# <a name="get-started-with-the-google-drive-connector"></a>開始使用 Google 雲端硬碟連接器
連線到 Google 雲端硬碟來建立檔案、取得資料列等等。 您可以利用 Google 雲端硬碟來： 

* 根據您透過搜尋所取得的資料，來建置您的商務流程。 
* 使用動作來搜尋圖像、搜尋新聞等等。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，您可以搜尋某支影片，然後利用 Twitter 把該影片張貼在某個 Twitter 摘要上。

如要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Google 雲端硬碟包含下列動作， 但不包含觸發程序。 

| 觸發程序 | 動作 |
| --- | --- |
| None |<ul><li>建立檔案</li><li>插入資料列</li><li>複製檔案</li><li>刪除檔案</li><li>刪除資料列</li><li>將封存檔案解壓縮到資料夾</li><li>使用識別碼來取得檔案內容</li><li>使用路徑來取得檔案內容</li><li>使用識別碼來取得檔案中繼資料</li><li>使用路徑來取得檔案中繼資料</li><li>取得單一資料列</li><li>更新檔案</li><li>更新資料列</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。

## <a name="create-the-connection-to-google-drive"></a>建立至 Google 雲端硬碟的連線
當您將這個 API 新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Google 雲端硬碟。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

當您建立連線之後，請輸入 Google 雲端硬碟的屬性，例如資料夾路徑或檔案名稱。 本主題的 REST API 參考  會說明這些屬性。

> [!TIP]
> 您可以在其他的邏輯應用程式中，使用這個相同的 Google 雲端硬碟連線。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參考
適用的版本：1.0。

### <a name="create-file"></a>建立檔案
把檔案上傳到 Google 雲端硬碟。  
```POST: /datasets/default/files```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |字串 |yes |query |None |用來把檔案上傳到 Google 雲端硬碟的資料夾路徑 |
| 名稱 |字串 |yes |query |None |要在 Google 雲端硬碟中建立之檔案的名稱 |
| body |字串 (二進位) |是 |body |None |要上傳到 Google 雲端硬碟之檔案的內容 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="insert-row"></a>插入資料列
在 Google 工作表中插入資料列。  
```POST: /datasets/{dataset}/tables/{table}/items```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |None |Google 試算表檔案的唯一識別碼 |
| 資料表 |字串 |是 |路徑 |None |工作表的唯一識別碼。 |
| item |ItemInternalId：字串 |是 |body |None |要在指定工作表中插入的資料列 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="copy-file"></a>複製檔案
複製 Google 雲端硬碟上的檔案。  
```POST: /datasets/default/copyFile```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 來源 |字串 |yes |query |無 |來源檔案的 URL |
| 目的地 |字串 |yes |query |None |Google 雲端硬碟中的目的檔案路徑，包括目標檔案名稱 |
| overwrite |布林值 |no |query |無 |如果設定為「True」，則會覆寫目的檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="delete-file"></a>刪除檔案
刪除 Google 雲端硬碟中的檔案。  
```DELETE: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |None |要從 Google 雲端硬碟刪除之檔案的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="delete-row"></a>刪除資料列
刪除 Google 工作表中的資料列。  
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |None |Google 試算表檔案的唯一識別碼 |
| 資料表 |字串 |是 |路徑 |None |工作表的唯一識別碼。 |
| id |字串 |是 |路徑 |None |要刪除的資料列的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="extract-archive-to-folder"></a>將封存檔案解壓縮到資料夾
將封存檔案 (例如 .zip) 解壓縮到 Google 雲端硬碟中的資料夾。  
```POST: /datasets/default/extractFolderV2```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 來源 |字串 |yes |query |無 |封存檔案的路徑 |
| 目的地 |字串 |yes |query |None |用來把封存檔案內容解壓縮到 Google 雲端硬碟中的路徑 |
| overwrite |布林值 |no |query |無 |如果設定為「True」，則會覆寫目的檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-file-content-using-id"></a>使用識別碼來取得檔案內容
使用識別碼來擷取 Google 雲端硬碟中的檔案內容。  
```GET: /datasets/default/files/{id}/content```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |None |要從 Google 雲端硬碟擷取之檔案的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-file-content-using-path"></a>使用路徑來取得檔案內容
使用路徑來擷取 Google 雲端硬碟中的檔案內容。  
```GET: /datasets/default/GetFileContentByPath```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 路徑 |字串 |yes |query |None |Google 雲端硬碟中檔案的路徑。 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-file-metadata-using-id"></a>使用識別碼來取得檔案中繼資料
使用識別碼來擷取 Google 雲端硬碟中的檔案中繼資料。  
```GET: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |None |Google 雲端硬碟中檔案的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-file-metadata-using-path"></a>使用路徑來取得檔案中繼資料
使用路徑來擷取 Google 雲端硬碟中的檔案中繼資料。  
```GET: /datasets/default/GetFileByPath```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 路徑 |字串 |yes |query |None |Google 雲端硬碟中檔案的路徑。 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-row"></a>取得單一資料列
擷取 Google 工作表中的單一資料列。  
```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |None |Google 試算表檔案的唯一識別碼 |
| 資料表 |字串 |是 |路徑 |None |工作表的唯一識別碼。 |
| id |字串 |是 |路徑 |None |要擷取的資料列的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="update-file"></a>更新檔案
更新 Google 雲端硬碟中的檔案。  
```PUT: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |None |Google 雲端硬碟中要更新之檔案的唯一識別碼 |
| body |字串 (二進位) |是 |body |None |要上傳到 Google 雲端硬碟之檔案的內容 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="update-row"></a>更新資料列
更新 Google 工作表中的資料列。  
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |None |Google 試算表檔案的唯一識別碼 |
| 資料表 |字串 |是 |路徑 |None |工作表的唯一識別碼。 |
| id |字串 |是 |路徑 |None |要更新的資料列的唯一識別碼 |
| item |ItemInternalId：字串 |是 |body |None |值已更新的資料列 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
#### <a name="datasetsmetadata"></a>DataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 表格式 |未定義 |no |
| blob |未定義 |no |

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 來源 |字串 |no |
| displayName |字串 |no |
| urlEncoding |字串 |no |
| tableDisplayName |字串 |no |
| tablePluralName |字串 |no |

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 來源 |字串 |no |
| displayName |字串 |no |
| urlEncoding |字串 |no |

#### <a name="blobmetadata"></a>BlobMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| 名稱 |字串 |no |
| displayName |字串 |no |
| 路徑 |字串 |no |
| LastModified |字串 |no |
| 大小 |integer |no |
| MediaType |字串 |no |
| IsFolder |布林值 |no |
| ETag |字串 |no |
| FileLocator |字串 |no |

#### <a name="tablemetadata"></a>TableMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |字串 |no |
| title |字串 |no |
| x-ms-permission |字串 |no |
| 結構描述 |未定義 |no |

#### <a name="tableslist"></a>TablesList
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| value |array |no |

#### <a name="table"></a>資料表
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 名稱 |字串 |no |
| displayName |字串 |no |

#### <a name="item"></a>item
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| ItemInternalId |字串 |no |

#### <a name="itemslist"></a>ItemsList
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| value |array |no |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

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



<!--HONumber=Jan17_HO3-->


