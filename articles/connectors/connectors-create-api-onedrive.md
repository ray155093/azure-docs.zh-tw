---
title: "在您的 Logic Apps 中新增 OneDrive 連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 OneDrive 連接器的概觀"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9c6213f0dcb66ae0c53d716abfe84868b87585f1


---
# <a name="get-started-with-the-onedrive-connector"></a>開始使用 OneDrive 連接器
連線到 OneDrive 來管理您的檔案，包括上傳檔案、取得、刪除檔案等等。 

使用 OneDrive，您會︰ 

* 將檔案儲存在 OneDrive 中或更新 OneDrive 中的現有檔案以建置您的工作流程。 
* 使用觸發程序，在 OneDrive 內有檔案建立或更新時，啟動工作流程。
* 使用動作來建立檔案、刪除檔案等等。 例如，當收到有附件的新 Office 365 電子郵件時 (觸發程序)，在 OneDrive 建立新的檔案 (動作)。

本主題說明如何在邏輯應用程式中使用 OneDrive 連接器，並且也列出觸發程序和動作。

> [!NOTE]
> 這個版本的文章適用於 Logic Apps 公開上市版本 (GA)。 
> 
> 

若要深入瞭解 Logic Apps，請參閱[什麼是邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)以及[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="connect-to-onedrive"></a>連線到 OneDrive
您必須先建立與服務的「連線」，才能透過邏輯應用程式存取任何服務。 連線可讓邏輯應用程式與另一個服務連線。 例如，若要連線到 OneDrive，您必須先有 OneDrive「連線」。 若要建立連線，請輸入平常用來存取所要連線之服務的認證。 因此，在 OneDrive 中，請在 OneDrive 帳戶輸入認證以建立連線。

### <a name="create-the-connection"></a>建立連線
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>使用觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 觸發程序會以您想要的間隔和頻率「輪詢」服務。 [深入了解觸發程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 在邏輯應用程式中，輸入「onedrive」以取得觸發程序的清單︰  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 選取 [當檔案遭到修改時]。 如果連線已存在，則選取 [顯示選擇器] 按鈕以選取資料夾。
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    如果系統提示您登入，則輸入登入詳細資料來建立連線。 本主題中的[建立連線](connectors-create-api-onedrive.md#create-the-connection)一節會列出步驟。 
   
   > [!NOTE]
   > 在此範例中，當所選資料夾中的檔案更新時，邏輯應用程式便會執行。 若要查看此觸發程序的結果，請新增另一個動作，以傳送電子郵件給您。 例如，新增 Office 365 Outlook「傳送電子郵件」動作，以在檔案更新時傳送電子郵件給您。 
   > 
   > 
3. 選取 [編輯] 按鈕，然後設定 [頻率] 和 [間隔] 值。 例如，如果您希望觸發程序每隔 15 分鐘輪詢一次，則將 [頻率] 設定為 [分鐘] 並將 [間隔] 設定為 [15]。 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **儲存**您的變更 (工具列的左上角)。 邏輯應用程式將會儲存，而且可能會自動啟用。

## <a name="use-an-action"></a>使用動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 選取加號。 您會看到幾個選擇︰[新增動作]、[新增條件] 或其中一個 [其他] 選項。
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. 選擇 [新增動作] 。
3. 在文字方塊中，輸入「onedrive」以取得所有可用動作的清單。
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. 在我們的範例中，選擇 [OneDrive - 建立檔案]。 如果連線已存在，則選取 [資料夾路徑] 以供放置檔案、輸入 [檔案名稱]，然後選擇想要的 [檔案內容]︰  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    如果系統提示您輸入連線資訊，請輸入詳細資料以建立連線。 本主題的[建立連線](connectors-create-api-onedrive.md#create-the-connection)一節會說明這些屬性。 
   
   > [!NOTE]
   > 在此範例中，我們會在 OneDrive 資料夾建立新檔案。 您可以使用另一個觸發程序的輸出以建立 OneDrive 檔案。 例如，新增 Office 365 Outlook「新的電子郵件送達時」觸發程序。 然後新增 OneDrive「建立檔案」動作，使用 ForEach 內的 [附件] 和 [內容類型] 欄位在 OneDrive 中建立新檔案。 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)
   > 
   > 
5. **儲存**您的變更 (工具列的左上角)。 邏輯應用程式將會儲存，而且可能會自動啟用。

## <a name="technical-details"></a>技術詳細資料
## <a name="triggers"></a>觸發程序
| 觸發程序 | 說明 |
| --- | --- |
| [當檔案建立時](connectors-create-api-onedrive.md#when-a-file-is-created) |當資料夾中有新檔案建立時，此作業就會觸發流程。 |
| [當檔案遭到修改時](connectors-create-api-onedrive.md#when-a-file-is-modified) |當資料夾中有檔案遭到修改時，此作業就會觸發流程。 |

## <a name="actions"></a>動作
| 動作 | 說明 |
| --- | --- |
| [取得檔案中繼資料](connectors-create-api-onedrive.md#get-file-metadata) |這項作業會取得檔案的中繼資料。 |
| [更新檔案](connectors-create-api-onedrive.md#update-file) |這項作業會更新檔案。 |
| [刪除檔案](connectors-create-api-onedrive.md#delete-file) |這項作業會刪除檔案。 |
| [使用路徑來取得檔案中繼資料](connectors-create-api-onedrive.md#get-file-metadata-using-path) |這項作業會使用路徑取得檔案的中繼資料。 |
| [使用路徑來取得檔案內容](connectors-create-api-onedrive.md#get-file-content-using-path) |這項作業會使用路徑取得檔案的內容。 |
| [取得檔案內容](connectors-create-api-onedrive.md#get-file-content) |這項作業會取得檔案的內容。 |
| [建立檔案](connectors-create-api-onedrive.md#create-file) |這項作業會建立檔案。 |
| [複製檔案](connectors-create-api-onedrive.md#copy-file) |這項作業會將檔案複製到 OneDrive。 |
| [列出資料夾中的檔案](connectors-create-api-onedrive.md#list-files-in-folder) |這項作業會取得資料夾中的檔案和子資料夾清單。 |
| [列出根資料夾中的檔案](connectors-create-api-onedrive.md#list-files-in-root-folder) |這項作業會取得根資料夾中的檔案和子資料夾清單。 |
| [將封存檔案解壓縮到資料夾](connectors-create-api-onedrive.md#extract-archive-to-folder) |這項作業會將封存檔案解壓縮到資料夾 (範例︰.zip)。 |

### <a name="action-details"></a>動作詳細資料
在本節中，請查看每個動作的特定詳細資料，包括任何必要或選擇性的輸入屬性，以及任何與連接器相關聯的對應輸出。

#### <a name="get-file-metadata"></a>取得檔案中繼資料
這項作業會取得檔案的中繼資料。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |選取檔案 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
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

#### <a name="update-file"></a>更新檔案
這項作業會更新檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |選取檔案 |
| body* |檔案內容 |檔案的內容 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
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

#### <a name="delete-file"></a>刪除檔案
這項作業會刪除檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |選取檔案 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
無。

#### <a name="get-file-metadata-using-path"></a>使用路徑來取得檔案中繼資料
這項作業會使用路徑取得檔案的中繼資料。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| path* |檔案路徑 |選取檔案 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
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

#### <a name="get-file-content-using-path"></a>使用路徑來取得檔案內容
這項作業會使用路徑取得檔案的內容。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| path* |檔案路徑 |選取檔案 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
無。

#### <a name="get-file-content"></a>取得檔案內容
這項作業會取得檔案的內容。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |檔案 |選取檔案 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
無。

#### <a name="create-file"></a>建立檔案
這項作業會建立檔案。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| folderPath* |資料夾路徑 |選取資料夾 |
| name* |檔案名稱 |檔案名稱 |
| body* |檔案內容 |檔案的內容 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
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

#### <a name="copy-file"></a>複製檔案
這項作業會將檔案複製到 OneDrive。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| source* |來源 URL |來源檔案的 URL |
| destination* |目的地檔案路徑 |目的地檔案路徑，包括目標檔案名稱 |
| overwrite |覆寫？ |如果設定為「True」，則會覆寫目的檔案 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
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

#### <a name="when-a-file-is-created"></a>當檔案建立時
當資料夾中有新檔案建立時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| folderId* |資料夾 |選取資料夾 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
無。

#### <a name="when-a-file-is-modified"></a>當檔案遭到修改時
當資料夾中有檔案遭到修改時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| folderId* |資料夾 |選取資料夾 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
無。

#### <a name="list-files-in-folder"></a>列出資料夾中的檔案
這項作業會取得資料夾中的檔案和子資料夾清單。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 識別碼* |資料夾 |選取資料夾 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
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

#### <a name="list-files-in-root-folder"></a>列出根資料夾中的檔案
這項作業會取得根資料夾中的檔案和子資料夾清單。 

這個呼叫沒有參數。

##### <a name="output-details"></a>輸出詳細資料
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

#### <a name="extract-archive-to-folder"></a>將封存檔案解壓縮到資料夾
這項作業會將封存檔案解壓縮到資料夾 (範例︰.zip)。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| source* |來源封存檔案路徑 |封存檔案的路徑 |
| destination* |目的地資料夾路徑 |用來解壓縮封存檔案內容的路徑 |
| overwrite |覆寫？ |如果設定為「True」，則會覆寫目的檔案 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
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
下表概述動作和觸發程序的回應以及回應說明︰  

| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。 請到我們的 [API 清單](apis-list.md)探索 Logic Apps 中其他可用的連接器。




<!--HONumber=Nov16_HO3-->


