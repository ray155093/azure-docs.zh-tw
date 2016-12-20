---
title: "了解如何在邏輯應用程式中使用 SharePoint Online 連接器 | Microsoft Docs"
description: "使用 SharePoint Online 連接器建立邏輯應用程式來管理 SharePoint 上的清單。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 76a37a567ae077e3d0fee7a1bc7f763d4d1c7cf2


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>開始使用 SharePoint Online 連接器
使用 SharePoint Online 連接器來管理 SharePoint 清單。  

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)來開始。

## <a name="connect-to-sharepoint-online"></a>連接至 SharePoint Online
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。  

### <a name="create-a-connection-to-sharepoint-online"></a>建立至 SharePoint Online 的連線
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]
> 
> 

## <a name="use-a-sharepoint-online-trigger"></a>使用 SharePoint Online 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]
> 
> 

## <a name="use-a-sharepoint-online-action"></a>使用 SharePoint Online 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]
> 
> 

## <a name="technical-details"></a>技術詳細資料
以下是有關這個連接支援的觸發程序、動作和回應的詳細資料︰

## <a name="sharepoint-online-triggers"></a>SharePoint Online 觸發程序
SharePoint 具有下列觸發程序︰  

| 觸發程序 | 說明 |
| --- | --- |
| [當檔案建立時](connectors-create-api-sharepointonline.md#when-a-file-is-created) |當 SharePoint 資料夾中有新檔案建立時，此作業就會觸發流程。 |
| [當檔案遭到修改時](connectors-create-api-sharepointonline.md#when-a-file-is-modified) |當 SharePoint 資料夾中有檔案遭到修改時，此作業就會觸發流程。 |
| [建立新項目時](connectors-create-api-sharepointonline.md#when-a-new-item-is-created) |當 SharePoint 清單中有新項目建立時，此作業就會觸發流程。 |
| [當現有項目遭到修改時](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified) |當 SharePoint 清單中現有項目遭到修改時，此作業就會觸發流程。 |

## <a name="sharepoint-online-actions"></a>SharePoint Online 動作
SharePoint 具有下列觸發動作︰

| 動作 | 說明 |
| --- | --- |
| [取得檔案中繼資料](connectors-create-api-sharepointonline.md#get-file-metadata) |這項作業會使用檔案識別碼取得檔案中繼資料。 |
| [更新檔案](connectors-create-api-sharepointonline.md#update-file) |這項作業會更新檔案內容。 |
| [刪除檔案](connectors-create-api-sharepointonline.md#delete-file) |這項作業會刪除檔案。 |
| [使用路徑來取得檔案中繼資料](connectors-create-api-sharepointonline.md#get-file-metadata-using-path) |這項作業會使用檔案路徑取得檔案中繼資料。 |
| [使用路徑來取得檔案內容](connectors-create-api-sharepointonline.md#get-file-content-using-path) |這項作業會使用檔案路徑取得檔案內容。 |
| [取得檔案內容](connectors-create-api-sharepointonline.md#get-file-content) |這項作業會使用檔案識別碼取得檔案內容。 |
| [建立檔案](connectors-create-api-sharepointonline.md#create-file) |這項作業會將檔案上傳至 SharePoint 網站。 |
| [複製檔案](connectors-create-api-sharepointonline.md#copy-file) |這項作業會將檔案複製至 SharePoint 網站。 |
| [列出資料夾](connectors-create-api-sharepointonline.md#list-folder) |這項作業會取得包含在 SharePoint 資料夾中的檔案。 |
| [列出根資料夾](connectors-create-api-sharepointonline.md#list-root-folder) |這項作業會取得根 SharePoint 資料夾中的檔案。 |
| [解壓縮到資料夾](connectors-create-api-sharepointonline.md#extract-folder) |這項作業會將封存檔案解壓縮到 SharePoint 資料夾 (範例︰.zip)。 |
| [取得項目](connectors-create-api-sharepointonline.md#get-items) |這項作業會取得 SharePoint 清單的項目。 |
| [建立項目](connectors-create-api-sharepointonline.md#create-item) |這項作業會在 SharePoint 清單建立新的項目。 |
| [取得項目](connectors-create-api-sharepointonline.md#get-item) |這項作業會從 SharePoint 清單，藉由項目的識別碼取得單一項目。 |
| [刪除項目](connectors-create-api-sharepointonline.md#delete-item) |這項作業會刪除 SharePoint 清單的項目。 |
| [更新項目](connectors-create-api-sharepointonline.md#update-item) |這項作業會更新 SharePoint 清單的項目。 |
| [取得實體的值](connectors-create-api-sharepointonline.md#get-entity-values) |這項作業會取得 SharePoint 實體的可能值。 |
| [取得清單](connectors-create-api-sharepointonline.md#get-lists) |這項作業會取得網站的 SharePoint 清單。 |

### <a name="action-details"></a>動作詳細資料
以下是此連接器動作和觸發程序以及其回應的詳細資料︰

### <a name="get-file-metadata"></a>取得檔案中繼資料
這項作業會使用檔案識別碼取得檔案中繼資料。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| 識別碼* |檔案識別碼 |選取檔案 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
| --- | --- | --- |
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

### <a name="update-file"></a>更新檔案
這項作業會更新檔案內容。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| 識別碼* |檔案識別碼 |選取檔案 |
| body* |檔案內容 |檔案的內容 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
| --- | --- | --- |
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

### <a name="delete-file"></a>刪除檔案
這項作業會刪除檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| 識別碼* |檔案識別碼 |選取檔案 |

* 表示這是必要屬性

### <a name="get-file-metadata-using-path"></a>使用路徑來取得檔案中繼資料
這項作業會使用檔案路徑取得檔案中繼資料。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| path* |檔案路徑 |選取檔案 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
| --- | --- | --- |
| id |字串 |
| 名稱 |字串 |
| displayName |字串 |
| 路徑 |字串 |
| LastModified |字串 |
| 大小 |integer |
| MediaType |字串 |
| IsFolder |布林值 |
| ETag |字串 |
| FileLocator |string |

### <a name="get-file-content-using-path"></a>使用路徑來取得檔案內容
這項作業會使用檔案路徑取得檔案內容。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| path* |檔案路徑 |選取檔案 |

* 表示這是必要屬性

### <a name="get-file-content"></a>取得檔案內容
這項作業會使用檔案識別碼取得檔案內容。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| 識別碼* |檔案識別碼 |選取檔案 |

* 表示這是必要屬性

### <a name="create-file"></a>建立檔案
這項作業會將檔案上傳至 SharePoint 網站。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| folderPath* |資料夾路徑 |選取檔案 |
| name* |檔案名稱 |檔案名稱 |
| body* |檔案內容 |檔案的內容 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
| --- | --- | --- |
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

### <a name="copy-file"></a>複製檔案
這項作業會將檔案複製至 SharePoint 網站。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| source* |來源檔案路徑 |來源檔案的路徑 |
| destination* |目的地檔案路徑 |目的檔案的路徑 |
| overwrite |覆寫旗標 |是否覆寫目的地檔案 (若存在) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
| --- | --- | --- |
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

### <a name="when-a-file-is-created"></a>當檔案建立時
當 SharePoint 資料夾中有新檔案建立時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL |
| folderId* |資料夾識別碼 |選取資料夾 |

* 表示這是必要屬性

### <a name="when-a-file-is-modified"></a>當檔案遭到修改時
當 SharePoint 資料夾中有檔案遭到修改時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL |
| folderId* |資料夾識別碼 |選取資料夾 |

* 表示這是必要屬性

### <a name="list-folder"></a>列出資料夾
這項作業會取得包含在 SharePoint 資料夾中的檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| 識別碼* |檔案識別碼 |資料夾的唯一識別碼 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
| --- | --- | --- |
| id |字串 |
| 名稱 |字串 |
| displayName |字串 |
| 路徑 |字串 |
| LastModified |字串 |
| 大小 |integer |
| MediaType |字串 |
| IsFolder |布林值 |
| ETag |字串 |
| FileLocator |string |

### <a name="list-root-folder"></a>列出根資料夾
這項作業會取得根 SharePoint 資料夾中的檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
| --- | --- | --- |
| id |字串 |
| 名稱 |字串 |
| displayName |字串 |
| 路徑 |字串 |
| LastModified |字串 |
| 大小 |integer |
| MediaType |字串 |
| IsFolder |布林值 |
| ETag |字串 |
| FileLocator |string |

### <a name="extract-folder"></a>解壓縮到資料夾
這項作業會將封存檔案解壓縮到 SharePoint 資料夾 (範例︰.zip)。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL，例如 http://contoso.sharepoint.com/sites/mysite |
| source* |來源檔案路徑 |來源檔案的路徑 |
| destination* |目的地資料夾路徑 |目的資料夾的路徑 |
| overwrite |覆寫旗標 |是否覆寫目的地檔案 (若存在) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
| --- | --- | --- |
| id |字串 |
| 名稱 |字串 |
| displayName |字串 |
| 路徑 |字串 |
| LastModified |字串 |
| 大小 |integer |
| MediaType |字串 |
| IsFolder |布林值 |
| ETag |字串 |
| FileLocator |string |

### <a name="when-a-new-item-is-created"></a>當建立新項目時
當 SharePoint 清單中有新項目建立時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL (範例: http://contoso.sharepoint.com/sites/mysite) |
| 資料表 * |清單名稱 |SharePoint 清單名稱 |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要擷取的項目數目上限 (預設值 = 256) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

### <a name="when-an-existing-item-is-modified"></a>當現有項目遭到修改時
當 SharePoint 清單中現有項目遭到修改時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL (範例: http://contoso.sharepoint.com/sites/mysite) |
| 資料表 * |清單名稱 |SharePoint 清單名稱 |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要擷取的項目數目上限 (預設值 = 256) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

### <a name="get-items"></a>取得項目
這項作業會取得 SharePoint 清單的項目。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL (範例: http://contoso.sharepoint.com/sites/mysite) |
| 資料表 * |清單名稱 |SharePoint 清單名稱 |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要擷取的項目數目上限 (預設值 = 256) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

### <a name="create-item"></a>建立項目
這項作業會在 SharePoint 清單建立新的項目。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL (範例: http://contoso.sharepoint.com/sites/mysite) |
| 資料表 * |清單名稱 |SharePoint 清單名稱 |
| 項目 * |項目 |要建立的項目 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
項目

| 屬性名稱 | 資料類型 |
| --- | --- |
| ItemInternalId |string |

### <a name="get-item"></a>取得項目
這項作業會從 SharePoint 清單，藉由項目的識別碼取得單一項目。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL (範例: http://contoso.sharepoint.com/sites/mysite) |
| 資料表 * |清單名稱 |SharePoint 清單名稱 |
| 識別碼* |識別碼 |要擷取之項目的唯一識別碼 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
項目

| 屬性名稱 | 資料類型 |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-item"></a>刪除項目
這項作業會刪除 SharePoint 清單的項目。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL (範例: http://contoso.sharepoint.com/sites/mysite) |
| 資料表 * |清單名稱 |SharePoint 清單名稱 |
| 識別碼* |識別碼 |要刪除之項目的唯一識別碼 |

* 表示這是必要屬性

### <a name="update-item"></a>更新項目
這項作業會更新 SharePoint 清單的項目。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL (範例: http://contoso.sharepoint.com/sites/mysite) |
| 資料表 * |清單名稱 |SharePoint 清單名稱 |
| 識別碼* |識別碼 |要更新之項目的唯一識別碼 |
| 項目 * |項目 |屬性已更新的項目 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
項目

| 屬性名稱 | 資料類型 |
| --- | --- |
| ItemInternalId |string |

### <a name="get-entity-values"></a>取得實體的值
這項作業會取得 SharePoint 實體的可能值。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |SharePoint 網站 URL |SharePoint 網站 URL |
| 資料表 * |資料表名稱 |資料表名稱 |
| 識別碼* |實體識別碼 |實體識別碼 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
### <a name="get-lists"></a>取得清單
這項作業會取得網站的 SharePoint 清單。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |網站 URL |SharePoint 網站 URL (範例: http://contoso.sharepoint.com/sites/mysite) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
TablesList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

## <a name="http-responses"></a>HTTP 回應
上述動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰ 

| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤。 |
| 預設值 |作業失敗。 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


