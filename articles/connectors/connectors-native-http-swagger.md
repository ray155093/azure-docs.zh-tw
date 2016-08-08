<properties
	pageTitle="在邏輯應用程式中新增 HTTP + Swagger 動作 | Microsoft Azure"
	description="HTTP + Swagger 動作與作業的概觀"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# 開始使用 HTTP + Swagger 動作

您可以使用 HTTP + Swagger 動作，透過 [Swagger 文件](https://swagger.io)在任一 REST 端點建立一流的連接器。

- 以一流的設計工具經驗擴充邏輯應用程式以呼叫任一 REST 端點

若要開始在邏輯應用程式中使用 HTTP + Swagger 動作，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

---

## 使用 HTTP + Swagger 做為觸發程序或動作

HTTP + Swagger 觸發程序和動作函數的作用和 [HTTP 動作](connectors-native-http.md)相同，但能從 [swagger 中繼資料](https://swagger.io)將 API 的形式和輸出顯示在設計工具，因而可提供較佳的設計經驗。此外，您可以使用 HTTP + Swagger 做為觸發程序，而且如果您想實作輪詢觸發程序，則應遵循[這份文件所述](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers)的輪詢模式。
	
[進一步了解邏輯應用程式觸發程序和動作。](connectors-overview.md)

以下是使用 HTTP + Swagger 作業做為工作流程動作的範例。

1. 選取 [新增步驟] 按鈕
1. 選擇 [新增動作]
1. 在動作搜尋方塊中，輸入 "swagger" 以列出 HTTP + Swagger 動作

	![選取 HTTP + Swagger 動作](./media/connectors-native-http-swagger/using-action-1.png)

1. 輸入 swagger 文件的 URL
	- 此 URL 必須是 HTTPS 端點並已啟用 CORS，才能從設計工具使用。如果 swagger 文件不符合此條件，您可以使用 [已啟用 CORS 的 Azure 儲存體](#hosting-swagger-from-storage)來儲存文件。
1. 按 [下一步] 以從 swagger 文件讀取和轉譯
1. 新增 HTTP 呼叫所需的任何參數

	![完成 HTTP 動作](./media/connectors-native-http-swagger/using-action-2.png)

1. 按一下工具列左上角的 [儲存]，邏輯應用程式便會儲存並發佈 (啟動)

### 從儲存體裝載 Swagger

您可能會想參考未裝載的 swagger 文件，或是可能不符合在設計工具中使用的安全性或跨原始來源需求的 swagger 文件。為解決這個問題，您可以將 swagger 文件儲存在 Azure 儲存體並啟用 CORS，這樣就能參考該文件。在 Azure 儲存體中建立、設定和儲存 swagger 的步驟如下︰

1. [在 Blob 儲存體建立 Azure 儲存體帳戶](../storage/storage-create-storage-account.md) - 將權限設為「公用存取」
1. 在 Blob 啟用 CORS
	- 您可以使用[這個 PowerShell 指令碼](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)自動設定該設定。
1. 將 swagger 檔案上傳至 Azure Blob
	- 您可以從 [Azure 入口網站](https://portal.azure.com)或 [Azure 儲存體總管](http://storageexplorer.com/)之類的工具執行這項動作
1. 參考 Azure Blob 中文件的 HTTPS 連結 (遵循 `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*` 格式)

---

## 技術詳細資訊

以下是此連接器支援的觸發程序和動作的詳細資料。

## HTTP + Swagger 觸發程序

觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。[深入了解觸發程序。](connectors-overview.md) HTTP + Swagger 連接器有 1 個觸發程序。

|觸發程序|說明|
|---|---|
|HTTP + Swagger|進行 HTTP 呼叫並傳回回應內容|

## HTTP + Swagger 動作

動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作。](connectors-overview.md) HTTP + Swagger 連接器有 1 個可能的動作。

|動作|說明|
|---|---|
|HTTP + Swagger|進行 HTTP 呼叫並傳回回應內容|

### 動作詳細資料

HTTP + Swagger 連接器隨附 1 個可能的動作。下面有關於每個動作、其必要和選擇性輸入欄位，以及與其使用方式相關聯的對應輸出詳細資料的資訊。

#### HTTP + Swagger

在有 swagger 中繼資料的協助下提出 HTTP 輸出要求。* 代表必要欄位。

|顯示名稱|屬性名稱|說明|
|---|---|---|
|方法 *|方法|要使用的 HTTP 指令動詞|
|URI*|uri|HTTP 要求的 URI|
|標頭|headers|要包含的 HTTP 標頭的 JSON 物件|
|內文|body|HTTP 要求本文|
|驗證|驗證|要求要使用的驗證 - [如需詳細資訊，請參閱 HTTP](./connectors-native-http.md#authentication)|
<br>

**輸出詳細資料**

HTTP 回應

|屬性名稱|資料類型|說明|
|---|---|---|
|標頭|物件|回應標頭|
|內文|物件|回應物件|
|狀態碼|int|HTTP 狀態碼|

### HTTP 回應

呼叫不同動作時，您可能會收到特定回應。以下資料表概述對應的回應及說明。

|名稱|說明|
|---|---|
|200|OK|
|202|已接受|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|

---

## 後續步驟

以下是有關如何推動邏輯應用程式的詳細資料和我們的社群。

## 建立邏輯應用程式

立即試用平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。您可以查看我們的 [API 清單](apis-list.md)，以探索邏輯應用程式中其他可用的連接器。

<!---HONumber=AcomDC_0727_2016-->