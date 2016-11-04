---
title: 在您的 Logic Apps 中使用 SharePoint Online 連接器 | Microsoft Docs
description: 開始在邏輯應用程式中使用 Azure App Service SharePoint Online 連接器。
services: ''
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe

---
# 開始使用 SharePoint Online 連接器
SharePoint 連接器提供一種方式，讓您能夠使用 SharePoint 上的清單。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 我們來談談觸發程序及動作。
SharePoint 連接器可當做動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

SharePoint 連接器提供下列動作及/或觸發程序：

### SharePoint 的動作
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

### SharePoint 的觸發程序
您可以接聽下列事件：

| 觸發程序 | 說明 |
| --- | --- |
| OnNewFile |當某個 SharePoint 資料夾中有新檔案建立時，就會觸發某個流程。 |
| OnUpdatedFile |當某個 SharePoint 資料夾中有檔案遭到修改時，就會觸發某個流程。 |
| GetOnNewItems |當 SharePoint 清單中有新項目建立時 |
| GetOnUpdatedItems |當 SharePoint 清單中的現有項目遭到修改時 |

## 建立至 SharePoint 的連線
如要使用 SharePoint 連接器，您必須先建立**連線**，然後提供下列屬性的詳細資料：

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 SharePoint 的認證 |

若要連接到 **SharePoint Online**，您需要向 SharePoint online 提供您的身分識別 (使用者名稱和密碼、智慧卡認證等)。通過驗證之後，您就可以在邏輯應用程式中使用 SharePoint Online 連接器。

在邏輯應用程式的設計工具中，請遵循下列步驟來登入 SharePoint，以便建立在邏輯應用程式中使用的 **connection** 連線：

1. 在搜尋方塊中輸入 SharePoint，並等候搜尋傳回所有名稱中有 SharePoint 的項目：![設定 SharePoint][1]
2. 選取 [SharePoint Online - 當檔案建立時]
3. 選取 [登入 SharePoint Online]：![設定 SharePoint][2]
4. 提供您的 SharePoint 認證來登入，以向 SharePoint 進行驗證 ![設定 SharePoint][3]
5. 驗證完成後，只要設定 SharePoint 的 **[當檔案建立時]** 對話方塊，系統會將您重新導向至邏輯應用程式並完成作業。![設定 SharePoint][4]
6. 接著，您可以新增所需的其他觸發和動作來完成邏輯應用程式。
7. 選取上方功能表列的 [儲存] 來儲存您的工作。

## SharePoint REST API 參考
#### 本文件適用的版本：1.0
### 用來取得文件庫中的檔案中繼資料
**```GET: /datasets/{dataset}/files/{id}```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| id |string |yes |路徑 |無 |檔案的唯一識別碼 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 用來更新文件庫中的檔案
**```PUT: /datasets/{dataset}/files/{id}```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| id |string |yes |路徑 |無 |檔案的唯一識別碼 |
| body | |yes |body |無 |檔案的內容 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 用來刪除文件庫中的檔案
**```DELETE: /datasets/{dataset}/files/{id}```**

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| id |string |yes |路徑 |無 |檔案的唯一識別碼 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 用來取得文件庫中的檔案中繼資料
**```GET: /datasets/{dataset}/GetFileByPath```**

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| 路徑 |string |yes |query |無 |檔案的路徑 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 用來取得文件庫中的檔案
**```GET: /datasets/{dataset}/GetFileContentByPath```**

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| 路徑 |string |yes |query |無 |檔案的路徑 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 用來取得文件庫中的檔案
**```GET: /datasets/{dataset}/files/{id}/content```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| id |string |yes |路徑 |無 |檔案的唯一識別碼 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 用來上傳文件庫中的檔案
**```POST: /datasets/{dataset}/files```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| folderPath |string |yes |query |無 |資料夾的路徑。 |
| 名稱 |string |yes |query |無 |檔案名稱 |
| body | |yes |body |無 |檔案的內容 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 用來複製文件庫中的檔案
**```POST: /datasets/{dataset}/copyFile```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| 來源 |string |yes |query |無 |來源檔案的路徑 |
| 目的地 |string |yes |query |無 |目的檔案的路徑 |
| overwrite |布林值 |no |query |false |是否要覆寫現有的檔案。 |

### 下列為可能的回應：
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 當某個 SharePoint 資料夾中有新檔案建立時，就會觸發某個流程。
**```GET: /datasets/{dataset}/triggers/onnewfile```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL |
| folderId |string |yes |query |無 |SharePoint 中資料夾的唯一識別碼 |

### 下列為可能的回應：
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 當某個 SharePoint 資料夾中有檔案遭到修改時，就會觸發某個流程。
**```GET: /datasets/{dataset}/triggers/onupdatedfile```**

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL |
| folderId |string |yes |query |無 |SharePoint 中資料夾的唯一識別碼 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 用來解壓縮文件庫中的資料夾
**```POST: /datasets/{dataset}/extractFolderV2```**

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite |
| 來源 |string |yes |query |無 |來源檔案的路徑 |
| 目的地 |string |yes |query |無 |目的資料夾的路徑 |
| overwrite |布林值 |no |query |false |是否要覆寫現有的檔案。 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 當 SharePoint 清單中有新項目建立時
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```**

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |string |yes |路徑 |無 |SharePoint 清單名稱 |
| $skip |integer |no |query |無 |要略過的項目數目 (預設值 = 0) |
| $top |integer |no |query |無 |要擷取的項目數目上限 (預設值 = 256) |
| $filter |string |no |query |無 |用來限制項目數目的 ODATA filter 查詢 |
| $orderby |string |no |query |無 |用來指定項目順序的 ODATA orderBy 查詢 |

### 下列為可能的回應：
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 當 SharePoint 清單中的現有項目遭到修改時
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |string |yes |路徑 |無 |SharePoint 清單名稱 |
| $skip |integer |no |query |無 |要略過的項目數目 (預設值 = 0) |
| $top |integer |no |query |無 |要擷取的項目數目上限 (預設值 = 256) |
| $filter |string |no |query |無 |用來限制項目數目的 ODATA filter 查詢 |
| $orderby |string |no |query |無 |用來指定項目順序的 ODATA orderBy 查詢 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 在 SharePoint 清單中建立新項目
**```POST: /datasets/{dataset}/tables/{table}/items```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |string |yes |路徑 |無 |SharePoint 清單名稱 |
| item | |yes |body |無 |要建立的項目 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 擷取 SharePoint 清單中的單一項目
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |string |yes |路徑 |無 |SharePoint 清單名稱 |
| id |integer |yes |路徑 |無 |要擷取之項目的唯一識別碼 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 刪除 SharePoint 清單中的項目
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |string |yes |路徑 |無 |SharePoint 清單名稱 |
| id |integer |yes |路徑 |無 |要刪除之項目的唯一識別碼 |

### 下列為可能的回應：
| Name | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
### 更新 SharePoint 清單中的項目
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| 資料集 |string |yes |路徑 |無 |SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite) |
| 資料表 |string |yes |路徑 |無 |SharePoint 清單名稱 |
| id |integer |yes |路徑 |無 |要更新之項目的唯一識別碼 |
| item | |yes |body |無 |屬性已更新的項目 |

### 下列為可能的回應：
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

- - -
## 物件定義：
 **DataSetsMetadata**：

DataSetsMetadata 的必要屬性：

這些屬性都是不必要的。

**所有屬性**：

| 名稱 | 資料類型 |
| --- | --- |
| tabular |沒有定義 |
| blob |沒有定義 |

 **TabularDataSetsMetadata**：

TabularDataSetsMetadata 的必要屬性：

這些屬性都是不必要的。

**所有屬性**：

| Name | 資料類型 |
| --- | --- |
| 來源 |string |
| displayName |string |
| urlEncoding |string |
| tableDisplayName |string |
| tablePluralName |string |

 **BlobDataSetsMetadata**：

BlobDataSetsMetadata 的必要屬性：

這些屬性都是不必要的。

**所有屬性**：

| 名稱 | 資料類型 |
| --- | --- |
| 來源 |string |
| displayName |string |
| urlEncoding |string |

 **BlobMetadata**：

BlobMetadata 的必要屬性：

這些屬性都是不必要的。

**所有屬性**：

| 名稱 | 資料類型 |
| --- | --- |
| 識別碼 |string |
| 名稱 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 大小 |integer |
| MediaType |string |
| IsFolder |布林值 |
| ETag |string |
| FileLocator |string |

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
| 名稱 |string |
| title |string |
| x-ms-permission |string |
| 結構描述 |沒有定義 |

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

| Name | 資料類型 |
| --- | --- |
| 名稱 |string |
| DisplayName |string |

 **資料表**：

Table 的必要屬性：

這些屬性都是不必要的。

**所有屬性**：

| 名稱 | 資料類型 |
| --- | --- |
| Name |string |
| DisplayName |string |

 **Item**：

Item 的必要屬性：

這些屬性都是不必要的。

**所有屬性**：

| 名稱 | 資料類型 |
| --- | --- |
| ItemInternalId |string |

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

## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png

<!---HONumber=AcomDC_0824_2016-->