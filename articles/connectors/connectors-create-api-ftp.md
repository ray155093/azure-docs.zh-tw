<properties
pageTitle="了解如何在邏輯應用程式中使用 FTP 連接器 | Microsoft Azure"
description="使用 Azure App Service 建立邏輯應用程式。連線到 FTP 伺服器來管理您的檔案。您可以執行各種動作，例如上傳、更新、取得及刪除 FTP 伺服器中的檔案。"
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# 開始使用 FTP 連接器

使用 FTP 連接器在 FTP 伺服器上監視、管理和建立檔案。

若要使用[任何連接器](./apis-list.md)，您必須先建立邏輯應用程式。您可以從[立即建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)來開始。

## 連接至 FTP

您必須先建立與服務的「連線」，才能透過邏輯應用程式存取任何服務。[連線](./connectors-overview.md)可讓邏輯應用程式與另一個服務連線。

### 建立 FTP 連線

>[AZURE.INCLUDE [建立 FTP 連線的步驟](../../includes/connectors-create-api-ftp.md)]

## 使用 FTP 觸發程序

觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。[深入了解觸發程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

>[AZURE.IMPORTANT]FTP 連接器需要可從網際網路存取且設定為以「被動」模式運作的 FTP 伺服器。此外，FTP 連接器**與隱含 FTPS (FTP over SSL) 不相容**。FTP 連接器只支援明確 FTPS (FTP over SSL)。

在此範例中，我將告訴您如何使用 **FTP - 新增或修改檔案時**觸發程序，在檔案新增或修改於 FTP 伺服器時起始邏輯應用程式工作流程。在企業範例中，您可以使用此觸發程序來監視代表客戶訂單的新檔案 FTP 資料夾。然後，您可以使用 FTP 連接器動作 (例如**取得檔案內容**) 取得訂單的內容以進一步處理並儲存在訂單資料庫中。

1. 在邏輯應用程式設計工具的搜尋方塊中輸入 ftp，然後選取 [FTP - 當新增或修改檔案時] 觸發程序 ![FTP 觸發程序影像 1](./media/connectors-create-api-ftp/ftp-trigger-1.png) [當新增或修改檔案時] 控制項隨即開啟 ![FTP 觸發程序影像 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)
- 選取位於控制項右側的 [...]。這會開啟資料夾選擇器控制項 ![FTP 觸發程序影像 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)
- 選取 **>** (向右箭號)，並瀏覽尋找您要對新的或修改過檔案監視的資料夾。選取資料夾，請注意資料夾現已顯示在 [資料夾] 控制項中。![FTP 觸發程序影像 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)


此時，邏輯應用程式已設有觸發程序，該觸發程序會在檔案於特定 FTP 資料夾中修改或建立時，開始執行工作流程中的其他觸發程序和動作。

>[AZURE.NOTE]為了讓邏輯應用程式能正常運作，它必須包含至少一個觸發程序和一個動作。請依照下一節中的步驟新增動作。



## 使用 FTP 動作

動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

您現已新增觸發程序，請遵循下列步驟來新增動作，該動作將會取得觸發程序所找到之新的或修改過檔案的內容。

1. 選取 [+ 新增步驟] 來新增動作，以取得 FTP 伺服器上檔案的內容
- 選取 [新增動作] 連結。![FTP 動作影像 1](./media/connectors-create-api-ftp/ftp-action-1.png)
- 輸入 FTP 以搜尋與 FTP 相關的所有動作。
- 選取 [FTP-取得檔案內容]，做為在 FTP 資料夾中找到新的或修改過檔案時所要採取的動作。![FTP 動作影像 2](./media/connectors-create-api-ftp/ftp-action-2.png) [取得檔案內容] 控制項隨即開啟。**Note**：如果您未曾授權邏輯應用程式存取您的 FTP 伺服器帳戶，系統會提示您這麼做。![FTP 動作影像 3](./media/connectors-create-api-ftp/ftp-action-3.png)
- 選取 [檔案] 控制項 (位於 [檔案]* 下方的空白處)。在這裡，您可以使用 FTP 伺服器上找到之新的或修改過檔案中的各種屬性。
- 選取 [檔案內容] 選項。![FTP 動作影像 4](./media/connectors-create-api-ftp/ftp-action-4.png)
-  控制項已更新，這表示 [FTP - 取得檔案內容] 動作會取得 FTP 伺服器上新的或修改過檔案的「檔案內容」。![FTP 動作影像 5](./media/connectors-create-api-ftp/ftp-action-5.png)
- 儲存您的工作，然後將檔案加入至 FTP 資料夾，以測試您的工作流程。

此時，邏輯應用程式已設有觸發程序來監視 FTP 伺服器上的資料夾，而當它在 FTP 伺服器上找到新的檔案或修改過的檔案時會起始工作流程。

邏輯應用程式也已設有一個動作，以取得新的或修改過檔案的內容。

您現在可以加入另一個動作，例如 [SQL Server - 插入資料列](./connectors-create-api-sqlazure.md#insert-row)動作，已在 SQL Database 資料表中插入新的或修改過檔案的內容。

## 技術詳細資料

以下是有關這個連接支援的觸發程序、動作和回應的詳細資料︰

## FTP 觸發程序

FTP 具有下列觸發程序︰

|觸發程序 | 說明|
|--- | ---|
|[當新增或修改檔案時](connectors-create-api-ftp.md#when-a-file-is-added-or-modified)|當資料夾中新增或修改檔案時，此作業就會觸發流程。|


## FTP 動作

FTP 具有下列動作︰


|動作|說明|
|--- | ---|
|[取得檔案中繼資料](connectors-create-api-ftp.md#get-file-metadata)|這項作業會取得檔案的中繼資料。|
|[更新檔案](connectors-create-api-ftp.md#update-file)|這項作業會更新檔案。|
|[刪除檔案](connectors-create-api-ftp.md#delete-file)|這項作業會刪除檔案。|
|[使用路徑來取得檔案中繼資料](connectors-create-api-ftp.md#get-file-metadata-using-path)|這項作業會使用路徑取得檔案的中繼資料。|
|[使用路徑來取得檔案內容](connectors-create-api-ftp.md#get-file-content-using-path)|這項作業會使用路徑取得檔案的內容。|
|[取得檔案內容](connectors-create-api-ftp.md#get-file-content)|這項作業會取得檔案的內容。|
|[建立檔案](connectors-create-api-ftp.md#create-file)|這項作業會建立檔案。|
|[複製檔案](connectors-create-api-ftp.md#copy-file)|這項作業會將檔案複製至 FTP 伺服器。|
|[列出資料夾中的檔案](connectors-create-api-ftp.md#list-files-in-folder)|這項作業會取得資料夾中的檔案和子資料夾清單。|
|[列出根資料夾中的檔案](connectors-create-api-ftp.md#list-files-in-root-folder)|這項作業會取得根資料夾中的檔案和子資料夾清單。|
|[解壓縮到資料夾](connectors-create-api-ftp.md#extract-folder)|這項作業會將封存檔案解壓縮到資料夾 (範例︰.zip)。|
### 動作詳細資料

以下是此連接器動作和觸發程序以及其回應的詳細資料︰



### 取得檔案中繼資料
這項作業會取得檔案的中繼資料。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|識別碼*|檔案|選取檔案|

* 表示這是必要屬性

#### 輸出詳細資料

BlobMetadata


| 屬性名稱 | 資料類型 |
|---|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|




### 更新檔案
這項作業會更新檔案。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|識別碼*|檔案|選取檔案|
|body*|檔案內容|檔案的內容|

* 表示這是必要屬性

#### 輸出詳細資料

BlobMetadata


| 屬性名稱 | 資料類型 |
|---|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|




### 刪除檔案
這項作業會刪除檔案。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|識別碼*|檔案|選取檔案|

* 表示這是必要屬性




### 使用路徑來取得檔案中繼資料
這項作業會使用路徑取得檔案的中繼資料。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|path*|檔案路徑|選取檔案|

* 表示這是必要屬性

#### 輸出詳細資料

BlobMetadata


| 屬性名稱 | 資料類型 |
|---|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|




### 使用路徑來取得檔案內容
這項作業會使用路徑取得檔案的內容。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|path*|檔案路徑|選取檔案|

* 表示這是必要屬性




### 取得檔案內容
這項作業會取得檔案的內容。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|識別碼*|檔案|選取檔案|

* 表示這是必要屬性




### 建立檔案
這項作業會建立檔案。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|folderPath*|資料夾路徑|選取資料夾|
|name*|檔案名稱|檔案名稱|
|body*|檔案內容|檔案的內容|

* 表示這是必要屬性

#### 輸出詳細資料

BlobMetadata


| 屬性名稱 | 資料類型 |
|---|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|




### 複製檔案
這項作業會將檔案複製至 FTP 伺服器。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|source*|來源 URL|來源檔案的 URL|
|destination*|目的地檔案路徑|目的地檔案路徑，包括目標檔案名稱|
|overwrite|覆寫？|如果設定為「True」，則會覆寫目的檔案|

* 表示這是必要屬性

#### 輸出詳細資料

BlobMetadata


| 屬性名稱 | 資料類型 |
|---|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|




### 當新增或修改檔案時
當資料夾中新增或修改檔案時，此作業就會觸發流程。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|folderId*|資料夾|選取資料夾|

* 表示這是必要屬性




### 列出資料夾中的檔案
這項作業會取得資料夾中的檔案和子資料夾清單。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|識別碼*|資料夾|選取資料夾|

* 表示這是必要屬性



#### 輸出詳細資料

BlobMetadata


| 屬性名稱 | 資料類型 |
|---|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|




### 列出根資料夾中的檔案
這項作業會取得根資料夾中的檔案和子資料夾清單。


這個呼叫沒有參數

#### 輸出詳細資料

BlobMetadata


| 屬性名稱 | 資料類型 |
|---|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|




### 解壓縮到資料夾
這項作業會將封存檔案解壓縮到資料夾 (範例︰.zip)。


|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|source*|來源封存檔案路徑|封存檔案的路徑|
|destination*|目的地資料夾路徑|目的資料夾的路徑|
|overwrite|覆寫？|如果設定為「True」，則會覆寫目的檔案|

* 表示這是必要屬性



#### 輸出詳細資料

BlobMetadata


| 屬性名稱 | 資料類型 |
|---|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|



## HTTP 回應

上述動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰

|名稱|說明|
|---|---|
|200|OK|
|202|已接受|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤。|
|預設值|作業失敗。|







## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->