---
title: Dropbox | Microsoft Docs
description: "使用 Azure App Service 建立邏輯應用程式。 連線到 Dropbox 來管理您的檔案。 您可以執行各種動作，例如上傳、更新、取得及刪除 Dropbox 中的檔案。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: a8ecad3552f35c02c07fe33aa8531fb462b27002


---
# <a name="get-started-with-the-dropbox-connector"></a>開始使用 Dropbox 連接器
連線到 Dropbox 來管理您的檔案。 您可以執行各種動作，例如上傳、更新、取得及刪除 Dropbox 中的檔案。

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)來開始。

## <a name="connect-to-dropbox"></a>連接至 Dropbox。
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 連線可讓邏輯應用程式與另一個服務連線。 例如，若要連線至 Dropbox，您必須先建立 Dropbox *連線*。 若要建立連線，您需要提供平常用來存取所要連線之服務的認證。 因此，在 Dropbox 範例中，您需要 Dropbox 帳戶的認證，才能建立與 Dropbox 的連線。 [深入了解連線]()

### <a name="create-a-connection-to-dropbox"></a>建立 Dropbox 連線
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>使用 Dropbox 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

在此範例中，我們將使用**建立檔案時**觸發程序。 當此觸發程序發生時，我們會呼叫**使用路徑來取得檔案內容** Dropbox 動作。 

1. 在 Logic Apps 設計工具的搜尋方塊中輸入 *dropbox*，然後選取 [Dropbox - 建立檔案時] 觸發程序。      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. 選取您想要追蹤檔案建立所在的資料夾。 選取 [...]\(以紅色方塊識別)，並瀏覽至您想要針對觸發程序的輸入而選取的資料夾。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>使用 Dropbox 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

現在已新增觸發程序，請遵循下列步驟來新增將會取得新檔案內容的動作。

1. 選取 [+ 新的步驟] 來新增您想要在新檔案建立時採取的動作。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. 選取 [新增動作] 。 這會開啟搜尋方塊，您可以在其中搜尋任何想要採取的動作。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. 輸入 *dropbox* 以搜尋與 Dropbox 相關的動作。  
4. 選取 [Dropbox - 使用路徑來取得檔案內容]，做為在選取的 Dropbox 資料夾中建立新檔案時所要採取的動作。 動作控制區塊便會開啟。 如果您未曾授權邏輯應用程式存取您的 Dropbox 帳戶，系統會提示您這麼做。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. 選取 [...]\(位於 [檔案路徑] 控制項的右側)，並瀏覽至您想要使用的檔案路徑。 或者，使用**檔案路徑**權杖來加速建立邏輯應用程式。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. 儲存您的工作並在 Dropbox 中建立新檔案，以啟動您的工作流程。  

## <a name="technical-details"></a>技術詳細資訊
以下是有關這個連接支援的觸發程序、動作和回應的詳細資料︰

## <a name="dropbox-triggers"></a>Dropbox 觸發程序
Dropbox 觸發程序具有下列動作︰  

| 觸發程序 | 說明 |
| --- | --- |
| [當檔案建立時](connectors-create-api-dropbox.md#when-a-file-is-created) |當資料夾中有新檔案建立時，此作業就會觸發流程。 |
| [當檔案遭到修改時](connectors-create-api-dropbox.md#when-a-file-is-modified) |當資料夾中有檔案遭到修改時，此作業就會觸發流程。 |

## <a name="dropbox-actions"></a>Dropbox 動作
Dropbox 連接器具有下列動作︰

| 動作 | 說明 |
| --- | --- |
| [取得檔案中繼資料](connectors-create-api-dropbox.md#get-file-metadata) |這項作業會取得檔案的中繼資料。 |
| [更新檔案](connectors-create-api-dropbox.md#update-file) |這項作業會更新檔案。 |
| [刪除檔案](connectors-create-api-dropbox.md#delete-file) |這項作業會刪除檔案。 |
| [使用路徑來取得檔案中繼資料](connectors-create-api-dropbox.md#get-file-metadata-using-path) |這項作業會使用路徑取得檔案的中繼資料。 |
| [使用路徑來取得檔案內容](connectors-create-api-dropbox.md#get-file-content-using-path) |這項作業會使用路徑取得檔案的內容。 |
| [取得檔案內容](connectors-create-api-dropbox.md#get-file-content) |這項作業會取得檔案的內容。 |
| [建立檔案](connectors-create-api-dropbox.md#create-file) |這項作業會建立檔案。 |
| [複製檔案](connectors-create-api-dropbox.md#copy-file) |這項作業會將檔案複製到 Dropbox。 |
| [列出資料夾中的檔案](connectors-create-api-dropbox.md#list-files-in-folder) |這項作業會取得資料夾中的檔案和子資料夾清單。 |
| [列出根資料夾中的檔案](connectors-create-api-dropbox.md#list-files-in-root-folder) |這項作業會取得根資料夾中的檔案和子資料夾清單。 |
| [將封存檔案解壓縮到資料夾](connectors-create-api-dropbox.md#extract-archive-to-folder) |這項作業會將封存檔案解壓縮到資料夾 (範例︰.zip)。 |

### <a name="action-details"></a>動作詳細資料
以下是此連接器動作和觸發程序以及其回應的詳細資料︰

### <a name="get-file-metadata"></a>取得檔案中繼資料
這項作業會取得檔案的中繼資料。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |選取檔案 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
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

### <a name="update-file"></a>更新檔案
這項作業會更新檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |選取檔案 |
| body* |檔案內容 |檔案的內容 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
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

### <a name="delete-file"></a>刪除檔案
這項作業會刪除檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |選取檔案 |

* 表示這是必要屬性

### <a name="get-file-metadata-using-path"></a>使用路徑來取得檔案中繼資料
這項作業會使用路徑取得檔案的中繼資料。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| path* |檔案路徑 |選取檔案 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
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
| FileLocator |string |

### <a name="get-file-content-using-path"></a>使用路徑來取得檔案內容
這項作業會使用路徑取得檔案的內容。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| path* |檔案路徑 |選取檔案 |

* 表示這是必要屬性

### <a name="get-file-content"></a>取得檔案內容
這項作業會取得檔案的內容。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |選取檔案 |

* 表示這是必要屬性

### <a name="create-file"></a>建立檔案
這項作業會建立檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| folderPath* |資料夾路徑 |選取資料夾 |
| name* |檔案名稱 |檔案名稱 |
| body* |檔案內容 |檔案的內容 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
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

### <a name="copy-file"></a>複製檔案
這項作業會將檔案複製到 Dropbox。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| source* |來源 URL |來源檔案的 URL |
| destination* |目的地檔案路徑 |目的地檔案路徑，包括目標檔案名稱 |
| overwrite |覆寫？ |如果設定為「True」，則會覆寫目的檔案 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
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

### <a name="when-a-file-is-created"></a>當檔案建立時
當資料夾中有新檔案建立時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| folderId* |資料夾 |選取資料夾 |

* 表示這是必要屬性

### <a name="when-a-file-is-modified"></a>當檔案遭到修改時
當資料夾中有檔案遭到修改時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| folderId* |資料夾 |選取資料夾 |

* 表示這是必要屬性

### <a name="list-files-in-folder"></a>列出資料夾中的檔案
這項作業會取得資料夾中的檔案和子資料夾清單。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |資料夾 |選取資料夾 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
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

### <a name="list-files-in-root-folder"></a>列出根資料夾中的檔案
這項作業會取得根資料夾中的檔案和子資料夾清單。 

這個呼叫沒有參數

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
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

### <a name="extract-archive-to-folder"></a>將封存檔案解壓縮到資料夾
這項作業會將封存檔案解壓縮到資料夾 (範例︰.zip)。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| source* |來源封存檔案路徑 |封存檔案的路徑 |
| destination* |目的地資料夾路徑 |用來解壓縮封存檔案內容的路徑 |
| overwrite |覆寫？ |如果設定為「True」，則會覆寫目的檔案 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
BlobMetadata

| 屬性名稱 | 資料類型 |
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
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


