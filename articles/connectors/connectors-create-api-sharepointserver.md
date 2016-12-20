---
title: "在邏輯應用程式中使用 SharePoint Online 連接器 | Microsoft Docs"
description: "開始在邏輯應用程式中使用 Azure App Service SharePoint Online 連接器。"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 76b6f627efdf189b873d9a6bdd722e73e837077d


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>開始使用 SharePoint Online 連接器
SharePoint 連接器提供一種方式，讓您能夠使用 SharePoint 上的清單。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

如要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="lets-talk-about-triggers-and-actions"></a>我們來談談觸發程序及動作。
SharePoint 連接器可當做動作使用，它有觸發程序。 所有連接器都支援 JSON 和 XML 格式的資料。 

SharePoint 連接器提供下列動作及/或觸發程序：

### <a name="sharepoint-actions"></a>SharePoint 的動作
您可以採取下列動作：

| 動作 | 說明 |
| --- | --- |
| GetFileMetadata |用來取得文件庫中的檔案中繼資料 |
| UpdateFile |用來更新文件庫中的檔案 |
| DeleteFile |用來刪除文件庫中的檔案 |
| GetFileMetadataByPath |用來取得文件庫中的檔案中繼資料 |
| GetFileContentByPath |用來取得文件庫中的檔案 |
| GetFileContent |用來取得文件庫中的檔案 |
| CreateFile |用來上傳文件庫中的檔案 |
| CopyFile |用來複製文件庫中的檔案 |
| ExtractFolderV2 |用來解壓縮文件庫中的資料夾 |
| PostItem |在 SharePoint 清單中建立新項目 |
| GetItem |擷取 SharePoint 清單中的單一項目 |
| DeleteItem |刪除 SharePoint 清單中的項目 |
| PatchItem |更新 SharePoint 清單中的項目 |

### <a name="sharepoint-triggers"></a>SharePoint 的觸發程序
您可以接聽下列事件：

| 觸發程序 | 說明 |
| --- | --- |
| OnNewFile |當某個 SharePoint 資料夾中有新檔案建立時，就會觸發某個流程。 |
| OnUpdatedFile |當某個 SharePoint 資料夾中有檔案遭到修改時，就會觸發某個流程。 |
| GetOnNewItems |當 SharePoint 清單中有新項目建立時 |
| GetOnUpdatedItems |當 SharePoint 清單中的現有項目遭到修改時 |

## <a name="create-a-connection-to-sharepoint"></a>建立至 SharePoint 的連線
如要使用 SharePoint 連接器，您必須先建立 **連線** ，然後提供下列屬性的詳細資料： 

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 SharePoint 的認證 |

若要連接到 **SharePoint Online**，您需要向 SharePoint online 提供您的身分識別 (使用者名稱和密碼、智慧卡認證等)。 通過驗證之後，您就可以在邏輯應用程式中使用 SharePoint Online 連接器。 

在邏輯應用程式的設計工具中，請依照下列步驟來登入 SharePoint，以便建立 **連接** 供您的邏輯應用程式使用：

1. 在搜尋方塊中輸入 SharePoint，等候搜尋傳回名稱中有 SharePoint 的所有項目：   
   ![設定 SharePoint][1]  
2. 選取 [SharePoint Online - 當檔案建立時]    
3. 選取 [登入 SharePoint Online]：   
   ![設定 SharePoint][2]    
4. 提供您的 SharePoint 認證來登入向 SharePoint 進行驗證    
   ![設定 SharePoint][3]     
5. 驗證完成後，您會被重新導向至邏輯應用程式，設定 SharePoint 的 [當檔案建立時]  對話方塊後即可完成。          
   ![設定 SharePoint][4]  
6. 接著，您可以新增所需的其他觸發和動作來完成邏輯應用程式。   
7. 選取上方功能表列的 [儲存]  來儲存您的工作。  

## <a name="sharepoint-rest-api-reference"></a>SharePoint REST API 參考
#### <a name="this-documentation-is-for-version-10"></a>本文件適用的版本：1.0
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>用來取得文件庫中的檔案中繼資料
**```GET: /datasets/{dataset}/files/{id}```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| id |字串 |是 |路徑 |無 |檔案的唯一識別碼 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>用來更新文件庫中的檔案
**```PUT: /datasets/{dataset}/files/{id}```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| id |字串 |是 |路徑 |無 |檔案的唯一識別碼 |
| body | |是 |body |無 |檔案的內容 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>用來刪除文件庫中的檔案
**```DELETE: /datasets/{dataset}/files/{id}```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| id |字串 |是 |路徑 |無 |檔案的唯一識別碼 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>用來取得文件庫中的檔案中繼資料
**```GET: /datasets/{dataset}/GetFileByPath```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| 路徑 |字串 |yes |query |無 |檔案的路徑 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>用來取得文件庫中的檔案
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| 路徑 |字串 |yes |query |無 |檔案的路徑 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>用來取得文件庫中的檔案
**```GET: /datasets/{dataset}/files/{id}/content```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| id |字串 |是 |路徑 |無 |檔案的唯一識別碼 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>用來上傳文件庫中的檔案
**```POST: /datasets/{dataset}/files```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| folderPath |字串 |yes |query |無 |資料夾的路徑。 |
| 名稱 |字串 |yes |query |無 |檔案名稱 |
| body | |是 |body |無 |檔案的內容 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>用來複製文件庫中的檔案
**```POST: /datasets/{dataset}/copyFile```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| 來源 |字串 |yes |query |無 |來源檔案的路徑 |
| 目的地 |字串 |yes |query |無 |目的檔案的路徑 |
| overwrite |布林值 |no |query |false |是否要覆寫現有的檔案。 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>當某個 SharePoint 資料夾中有新檔案建立時，就會觸發某個流程。
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL |
| folderId |字串 |yes |query |無 |SharePoint 中資料夾的唯一識別碼 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>當某個 SharePoint 資料夾中有檔案遭到修改時，就會觸發某個流程。
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL |
| folderId |字串 |yes |query |無 |SharePoint 中資料夾的唯一識別碼 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>用來解壓縮文件庫中的資料夾
**```POST: /datasets/{dataset}/extractFolderV2```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL。 例如 http://contoso.sharepoint.com/sites/mysite |
| 來源 |字串 |yes |query |無 |來源檔案的路徑 |
| 目的地 |字串 |yes |query |無 |目的資料夾的路徑 |
| overwrite |布林值 |no |query |false |是否要覆寫現有的檔案。 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>當 SharePoint 清單中有新項目建立時
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL (範例：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |字串 |是 |路徑 |無 |SharePoint 清單名稱 |
| $skip |integer |no |query |None |要略過的項目數目 (預設值 = 0) |
| $top |integer |no |query |None |要擷取的項目數目上限 (預設值 = 256) |
| $filter |字串 |no |query |None |用來限制項目數目的 ODATA filter 查詢 |
| $orderby |字串 |no |query |None |用來指定項目順序的 ODATA orderBy 查詢 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>當 SharePoint 清單中的現有項目遭到修改時
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL (範例：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |字串 |是 |路徑 |無 |SharePoint 清單名稱 |
| $skip |integer |no |query |None |要略過的項目數目 (預設值 = 0) |
| $top |integer |no |query |None |要擷取的項目數目上限 (預設值 = 256) |
| $filter |字串 |no |query |None |用來限制項目數目的 ODATA filter 查詢 |
| $orderby |字串 |no |query |None |用來指定項目順序的 ODATA orderBy 查詢 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>在 SharePoint 清單中建立新項目
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL (範例：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |字串 |是 |路徑 |無 |SharePoint 清單名稱 |
| item | |是 |body |無 |要建立的項目 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>擷取 SharePoint 清單中的單一項目
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL (範例：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |字串 |是 |路徑 |無 |SharePoint 清單名稱 |
| id |integer |是 |路徑 |無 |要擷取之項目的唯一識別碼 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>刪除 SharePoint 清單中的項目
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL (範例：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |字串 |是 |路徑 |無 |SharePoint 清單名稱 |
| id |integer |是 |路徑 |無 |要刪除之項目的唯一識別碼 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>更新 SharePoint 清單中的項目
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |字串 |是 |路徑 |無 |SharePoint 網站 URL (範例：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |字串 |是 |路徑 |無 |SharePoint 清單名稱 |
| id |integer |是 |路徑 |無 |要更新之項目的唯一識別碼 |
| item | |是 |body |無 |屬性已更新的項目 |

### <a name="here-are-the-possible-responses"></a>下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
## <a name="object-definitions"></a>物件定義：
 **DataSetsMetadata**：

DataSetsMetadata 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| tabular |未定義 |
| blob |未定義 |

 **TabularDataSetsMetadata**：

TabularDataSetsMetadata 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| 來源 |字串 |
| displayName |字串 |
| urlEncoding |字串 |
| tableDisplayName |字串 |
| tablePluralName |字串 |

 **BlobDataSetsMetadata**：

BlobDataSetsMetadata 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| 來源 |字串 |
| displayName |字串 |
| urlEncoding |字串 |

 **BlobMetadata**：

BlobMetadata 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| id |字串 |
| 名稱 |字串 |
| displayName |字串 |
| 路徑 |字串 |
| LastModified |字串 |
| 大小 |integer |
| MediaType |字串 |
| IsFolder |布林值 |
| ETag |字串 |
| FileLocator |字串 |

 **Object**：

Object 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
|  | |

 **TableMetadata**：

TableMetadata 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| 名稱 |字串 |
| title |字串 |
| x-ms-permission |字串 |
| 結構描述 |未定義 |

 **DataSetsList**：

DataSetsList 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| value |array |

 **DataSet**：

DataSet 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| 名稱 |字串 |
| displayName |字串 |

 **資料表**：

Table 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| 名稱 |字串 |
| displayName |字串 |

 **Item**：

Item 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| ItemInternalId |字串 |

 **ItemsList**：

ItemsList 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| value |array |

 **TablesList**：

TablesList 的必要屬性：

這些屬性都是不必要的。 

**所有屬性**： 

| 名稱 | 資料類型 |
| --- | --- |
| value |array |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png



<!--HONumber=Nov16_HO3-->


