---
title: OneDrive for Business | Microsoft Docs
description: "使用 Azure App Service 建立邏輯應用程式。 連接到 OneDrive for Business 來管理您的檔案。 您可以對檔案執行各種動作，例如上傳、更新、取得及刪除。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cf9484e9-7a20-4de0-93c8-0fa132221f2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 992b4b7bab8878355cbe936b75768e67b086286e


---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>開始使用 OneDrive for Business 連接器
連接到 OneDrive for Business 來管理您的檔案。 您可以對檔案執行各種動作，例如上傳、更新、取得及刪除。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
OneDrive for Business 連接器可當成動作使用，它有觸發程序。 所有連接器都支援 JSON 和 XML 格式的資料。

 OneDrive for Business 連接器提供下列動作及/或觸發程序：

### <a name="onedrive-for-business-actions"></a>OneDrive for Business 動作
您可以採取下列動作：

| 動作 | 說明 |
| --- | --- |
| [GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata) |使用識別碼來擷取 OneDrive for Business 中的檔案中繼資料 |
| [UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile) |更新 OneDrive for Business 中的檔案 |
| [DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile) |刪除 OneDrive for Business 中的檔案 |
| [GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath) |使用路徑來擷取 OneDrive for Business 中的檔案中繼資料 |
| [GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath) |使用路徑來擷取 OneDrive for Business 中的檔案內容 |
| [GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent) |使用識別碼來擷取 OneDrive for Business 中的檔案內容 |
| [CreateFile](connectors-create-api-onedriveforbusiness.md#createfile) |把檔案上傳到 OneDrive for Business |
| [CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile) |將檔案複製到 OneDrive for Business |
| [ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder) |列出 OneDrive for Business 資料夾中的檔案 |
| [ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder) |列出 OneDrive for Business 根資料夾中的檔案 |
| [ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2) |將資料夾解壓縮到 OneDrive for Business |

### <a name="onedrive-for-business-triggers"></a>OneDrive for Business 觸發程序
您可以接聽下列事件：

| 觸發程序 | 說明 |
| --- | --- |
| 當檔案建立時 |在 OneDrive for Business 資料夾中建立新檔案時，就會觸發流程。 |
| 當檔案遭到修改時 |在 OneDrive for Business 資料夾中修改檔案時，就會觸發流程。 |

## <a name="create-a-connection-to-onedrive-for-business"></a>建立 OneDrive for Business 的連線
若要使用 OneDrive for Business 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 OneDrive for Business 認證 |

建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 
> [!TIP]
> 您可以在其他邏輯應用程式中使用這個連接。
> 
> 

## <a name="reference-for-onedrive-for-business"></a>OneDrive for Business 的參考
適用的版本：1.0

## <a name="getfilemetadata"></a>GetFileMetadata
使用識別碼取得檔案中繼資料：使用識別碼來擷取 OneDrive for Business 中的檔案中繼資料

```GET: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |指定檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="updatefile"></a>UpdateFile
更新檔案：更新 OneDrive for Business 中的檔案

```PUT: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |指定要更新的檔案 |
| body | |是 |body |無 |OneDrive for Business 中要更新的檔案內容 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="deletefile"></a>DeleteFile
刪除檔案：刪除 OneDrive for Business 中的檔案

```DELETE: /datasets/default/files/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |指定要刪除的檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
使用路徑取得檔案中繼資料：使用路徑來擷取 OneDrive for Business 中的檔案中繼資料

```GET: /datasets/default/GetFileByPath```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 路徑 |字串 |yes |query |無 |OneDrive for Business 中檔案的唯一路徑 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="getfilecontentbypath"></a>GetFileContentByPath
使用路徑取得檔案內容：使用路徑來擷取 OneDrive for Business 中的檔案內容

```GET: /datasets/default/GetFileContentByPath```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 路徑 |字串 |yes |query |無 |OneDrive for Business 中檔案的唯一路徑 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="getfilecontent"></a>GetFileContent
使用識別碼取得檔案內容：使用識別碼來擷取 OneDrive for Business 中的檔案內容

```GET: /datasets/default/files/{id}/content```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |指定檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="createfile"></a>CreateFile
建立檔案：把檔案上傳到 OneDrive for Business

```POST: /datasets/default/files```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |字串 |yes |query |無 |把檔案上傳到 OneDrive for Business 的資料夾路徑 |
| 名稱 |字串 |yes |query |無 |要在 OneDrive for Business 中建立的檔案名稱 |
| body | |是 |body |無 |要上傳到 OneDrive for Business 的檔案內容 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="copyfile"></a>CopyFile
複製檔案：將檔案複製到 OneDrive for Business

```POST: /datasets/default/copyFile```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 來源 |字串 |yes |query |無 |來源檔案的 URL |
| 目的地 |字串 |yes |query |無 |OneDrive for Business 中的目的檔案路徑，包括目標檔案名稱 |
| overwrite |布林值 |no |query |false |如果設定為「True」，則會覆寫目的檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="onnewfile"></a>OnNewFile
建立檔案時：在 OneDrive for Business 資料夾中建立新檔案時，就會觸發流程。

```GET: /datasets/default/triggers/onnewfile```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| folderId |字串 |yes |query |無 |指定資料夾 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="onupdatedfile"></a>OnUpdatedFile
修改檔案時：在 OneDrive for Business 資料夾中修改檔案時，就會觸發流程。

```GET: /datasets/default/triggers/onupdatedfile```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| folderId |字串 |yes |query |無 |指定資料夾 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="listfolder"></a>ListFolder
列出資料夾中的檔案：列出 OneDrive for Business 資料夾中的檔案

```GET: /datasets/default/folders/{id}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| id |字串 |是 |路徑 |無 |指定資料夾 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="listrootfolder"></a>ListRootFolder
列出根資料夾：列出 OneDrive for Business 根資料夾中的檔案

```GET: /datasets/default/folders```

這個呼叫沒有參數

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="extractfolderv2"></a>ExtractFolderV2
解壓縮資料夾：將資料夾解壓縮到 OneDrive for Business

```POST: /datasets/default/extractFolderV2```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 來源 |字串 |yes |query |無 |封存檔案的路徑 |
| 目的地 |字串 |yes |query |無 |解壓縮封存內容的 OneDrive for Business 路徑 |
| overwrite |布林值 |no |query |false |如果設定為「True」，則會覆寫目的檔案 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
### <a name="datasetsmetadata"></a>DataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 表格式 |沒有定義 |否 |
| blob |沒有定義 |否 |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 來源 |string |否 |
| displayName |string |否 |
| urlEncoding |string |否 |
| tableDisplayName |string |否 |
| tablePluralName |string |否 |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 來源 |string |否 |
| displayName |string |否 |
| urlEncoding |string |否 |

### <a name="blobmetadata"></a>BlobMetadata
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |string |否 |
| 名稱 |string |否 |
| DisplayName |string |否 |
| Path |string |否 |
| LastModified |string |否 |
| 大小 |integer |否 |
| MediaType |string |否 |
| IsFolder |布林值 |否 |
| ETag |string |否 |
| FileLocator |string |否 |

### <a name="object"></a>Object
## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


