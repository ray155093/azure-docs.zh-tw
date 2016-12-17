---
title: "將 Box 連接器新增到 Logic Apps | Microsoft Docs"
description: "搭配 REST API 參數來使用 Box 連接器的概觀"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 949579cf-a81c-4790-9ef5-fe39b4fbd0c5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: e211d0878a7f9bd43c95c727935ad883fae0db3a


---
# <a name="get-started-with-the-box-connector"></a>開始使用 Box 連接器
連線到 Box 來建立檔案、刪除檔案等等。 

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以利用 Box 來：

* 根據您從 Box 所取得的資料，來建置您的商務流程。 
* 在檔案建立或更新時使用觸發程序。
* 使用會複製檔案、刪除檔案等等的動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當 Box 中的某個檔案變更時，您可以取得該檔案，並利用 Office 365 來傳送已附加該檔案的電子郵件。

如果要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Box 包含下列觸發程序及動作。

| 觸發程序 | 動作 |
| --- | --- |
| <ul><li>當檔案建立時</li><li>當檔案遭到修改時</li></ul> |<ul><li>建立檔案</li><li>當檔案建立時</li><li>複製檔案</li><li>刪除檔案</li><li>將封存檔案解壓縮到資料夾</li><li>使用識別碼來取得檔案內容</li><li>使用路徑來取得檔案內容</li><li>使用識別碼來取得檔案中繼資料</li><li>使用路徑來取得檔案中繼資料</li><li>更新檔案</li><li>當檔案遭到修改時</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。

## <a name="create-a-connection-to-box"></a>建立至 Box 的連線
當您將這個連接器新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Box。

> [!INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]
> 
> 

當您建立連線之後，請輸入 Box 的屬性。 本主題的＜REST API 參考＞  一節說明這些屬性。

> [!TIP]
> 您可以在其他的邏輯應用程式中，使用這個相同的 Box 連線。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參考
適用的版本：1.0。

### <a name="create-file"></a>建立檔案
把檔案上傳到 Box。  
```POST: /datasets/default/files```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |字串 |是 |query |None |用來把檔案上傳到 Box 的資料夾路徑 |
| 名稱 |字串 |是 |query |None |要在 Box 中建立之檔案的名稱 |
| body |字串 (二進位) |是 |body |None |要上傳到 Box 之檔案的內容 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="when-a-file-is-created"></a>當檔案建立時
當某個 Box 資料夾中有新檔案建立時，就會觸發某個流程。  
```GET: /datasets/default/triggers/onnewfile```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| folderId |字串 |是 |query |None |Box 中資料夾的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="copy-file"></a>複製檔案
把檔案複製到 Box。  
```POST: /datasets/default/copyFile```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 來源 |字串 |是 |query |None |來源檔案的 URL |
| 目的地 |字串 |是 |query |None |Box 中的目的檔案路徑，包括目標檔案名稱 |
| overwrite |布林值 |否 |query |None |如果設定為「True」，則會覆寫目的檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="delete-file"></a>刪除檔案
刪除 Box 中的檔案。  
```DELETE: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |None |要從 Box 刪除之檔案的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="extract-archive-to-folder"></a>將封存檔案解壓縮到資料夾
將封存檔案 (例如 .zip) 解壓縮到 Box 中的資料夾。  
```POST: /datasets/default/extractFolderV2```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 來源 |字串 |是 |query | |封存檔案的路徑 |
| 目的地 |字串 |是 |query | |用來把封存檔案內容解壓縮到 Box 中的路徑 |
| overwrite |布林值 |否 |query | |如果設定為「True」，則會覆寫目的檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-file-content-using-id"></a>使用識別碼來取得檔案內容
使用識別碼來擷取 Box 中的檔案內容。  
```GET: /datasets/default/files/{id}/content```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |None |Box 中檔案的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-file-content-using-path"></a>使用路徑來取得檔案內容
使用路徑來擷取 Box 中的檔案內容。  
```GET: /datasets/default/GetFileContentByPath```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 路徑 |字串 |是 |query |None |Box 中檔案的唯一路徑 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-file-metadata-using-id"></a>使用識別碼來取得檔案中繼資料
使用檔案識別碼來擷取 Box 中的檔案中繼資料。  
```GET: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |None |Box 中檔案的唯一識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-file-metadata-using-path"></a>使用路徑來取得檔案中繼資料
使用路徑來擷取 Box 中的檔案中繼資料。  
```GET: /datasets/default/GetFileByPath```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 路徑 |字串 |是 |query |None |Box 中檔案的唯一路徑 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="update-file"></a>更新檔案
更新 Box 中的檔案。  
```PUT: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |None |Box 中要更新之檔案的唯一識別碼 |
| body |字串 (二進位) |是 |body |None |Box 中要更新之檔案的內容 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="when-a-file-is-modified"></a>當檔案遭到修改時
當某個 Box 資料夾中有檔案遭到修改時，就會觸發某個流程。  
```GET: /datasets/default/triggers/onupdatedfile```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| folderId |字串 |是 |query |None |Box 中資料夾的唯一識別碼 |

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

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。




<!--HONumber=Nov16_HO3-->


