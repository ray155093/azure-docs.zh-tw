---
title: "了解如何在您的邏輯應用程式中使用 SFTP 連接器 | Microsoft Docs"
description: "使用 Azure App Service 建立邏輯應用程式。 連接到 SFTP API 來傳送及接收檔案。 您可以執行各種作業，例如建立、更新、取得或刪除檔案。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 93b77197d69d8c6261ea786aecb653c69129dc9d


---
# <a name="get-started-with-the-sftp-connector"></a>開始使用 SFTP 連接器
使用 SFTP 連接器存取 SFTP 帳戶來傳送及接收檔案。 您可以執行各種作業，例如建立、更新、取得或刪除檔案。  

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)來開始。

## <a name="connect-to-sftp"></a>連接至 SFTP
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。  

### <a name="create-a-connection-to-sftp"></a>建立至 SFTP 的連線
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>使用 SFTP 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

在此範例中，我將告訴您如何使用 **SFTP - 新增或修改檔案時**觸發程序，在檔案新增或修改於 SFTP 伺服器時起始邏輯應用程式工作流程。 在範例中，您也將學習如何新增會檢查新的或修改檔案內容的條件，並在使用內容之前如果檔案指出它應該要解壓縮時，決定將檔案解壓縮。 最後，您將學習如何新增動作以解壓縮檔案的內容，並將解壓縮的內容放在 SFTP 伺服器上的資料夾。 

在企業範例中，您可以使用此觸發程序來監視代表客戶訂單的新檔案 SFTP 資料夾。  然後，您可以使用 SFTP 連接器動作 (例如**取得檔案內容**) 取得訂單的內容以進一步處理並儲存在訂單資料庫中。

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>新增條件
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>使用 SFTP 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="technical-details"></a>技術詳細資料
以下是有關這個連接支援的觸發程序、動作和回應的詳細資料︰

## <a name="sftp-triggers"></a>SFTP 觸發程序
SFTP 具有下列觸發程序︰  

| 觸發程序 | 說明 |
| --- | --- |
| [當新增或修改檔案時](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |當資料夾中新增或修改檔案時，此作業就會觸發流程。 |

## <a name="sftp-actions"></a>SFTP 動作
SFTP 具有下列動作︰

| 動作 | 說明 |
| --- | --- |
| [取得檔案中繼資料](connectors-create-api-sftp.md#get-file-metadata) |這項作業會使用檔案識別碼取得檔案中繼資料。 |
| [更新檔案](connectors-create-api-sftp.md#update-file) |這項作業會更新檔案內容。 |
| [刪除檔案](connectors-create-api-sftp.md#delete-file) |這項作業會刪除檔案。 |
| [使用路徑來取得檔案中繼資料](connectors-create-api-sftp.md#get-file-metadata-using-path) |這項作業會使用檔案路徑取得檔案中繼資料。 |
| [使用路徑來取得檔案內容](connectors-create-api-sftp.md#get-file-content-using-path) |這項作業會使用檔案路徑取得檔案內容。 |
| [取得檔案內容](connectors-create-api-sftp.md#get-file-content) |這項作業會使用檔案識別碼取得檔案內容。 |
| [建立檔案](connectors-create-api-sftp.md#create-file) |這項作業會將檔案上傳至 SFTP 伺服器。 |
| [複製檔案](connectors-create-api-sftp.md#copy-file) |這項作業會將檔案複製至 SFTP 伺服器。 |
| [列出資料夾中的檔案](connectors-create-api-sftp.md#list-files-in-folder) |這項作業會取得包含在資料夾中的檔案。 |
| [列出根資料夾中的檔案](connectors-create-api-sftp.md#list-files-in-root-folder) |這項作業會取得根資料夾中的檔案。 |
| [解壓縮資料夾](connectors-create-api-sftp.md#extract-folder) |這項作業會將封存檔案解壓縮到資料夾 (範例︰.zip)。 |

### <a name="action-details"></a>動作詳細資料
以下是此連接器動作和觸發程序以及其回應的詳細資料︰

### <a name="get-file-metadata"></a>取得檔案中繼資料
這項作業會使用檔案識別碼取得檔案中繼資料。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |指定檔案 |

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
| 識別碼* |檔案 |指定檔案 |
| body* |檔案內容 |要更新之檔案的內容 |

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
| 識別碼* |檔案 |指定檔案 |

* 表示這是必要屬性

### <a name="get-file-metadata-using-path"></a>使用路徑來取得檔案中繼資料
這項作業會使用檔案路徑取得檔案中繼資料。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| path* |檔案路徑 |檔案的唯一路徑 |

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
| path* |檔案路徑 |檔案的唯一路徑 |

* 表示這是必要屬性

### <a name="get-file-content"></a>取得檔案內容
這項作業會使用檔案識別碼取得檔案內容。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |指定檔案 |

* 表示這是必要屬性

### <a name="create-file"></a>建立檔案
這項作業會將檔案上傳至 SFTP 伺服器。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| folderPath* |資料夾路徑 |資料夾的唯一路徑 |
| name* |檔案名稱 |檔案名稱 |
| body* |檔案內容 |要建立之檔案的內容 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

|  | 屬性名稱 | 資料類型 |
| --- | --- | --- |
| id |字串 | |
| 名稱 |字串 | |
| displayName |字串 | |
| 路徑 |字串 | |
| LastModified |字串 | |
| 大小 |integer | |
| MediaType |字串 | |
| IsFolder |布林值 | |
| ETag |字串 | |
| FileLocator |字串 | |

### <a name="copy-file"></a>複製檔案
這項作業會將檔案複製至 SFTP 伺服器。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| source* |來源檔案路徑 |來源檔案的路徑 |
| destination* |目的地檔案路徑 |目的檔案的路徑，包括目標檔案名稱 |
| overwrite |覆寫？ |如果設定為「True」，則會覆寫目的檔案 |

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

### <a name="when-a-file-is-added-or-modified"></a>當新增或修改檔案時
當資料夾中新增或修改檔案時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| folderId* |資料夾 |指定資料夾 |

* 表示這是必要屬性

### <a name="list-files-in-folder"></a>列出資料夾中的檔案
這項作業會取得包含在資料夾中的檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |資料夾 |指定資料夾 |

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

### <a name="list-files-in-root-folder"></a>列出根資料夾中的檔案
這項作業會取得根資料夾中的檔案。 

這個呼叫沒有參數

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
這項作業會將封存檔案解壓縮到資料夾 (範例︰.zip)。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| source* |來源封存檔案路徑 |封存檔案的路徑 |
| destination* |目的地資料夾路徑 |目的資料夾的路徑 |
| overwrite |覆寫？ |如果設定為「True」，則會覆寫目的檔案 |

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


