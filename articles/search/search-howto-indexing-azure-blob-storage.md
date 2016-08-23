<properties
pageTitle="使用 Azure 搜尋服務對 Azure Blob 儲存體編制索引"
description="了解如何使用 Azure 搜尋服務對 Azure Blob 儲存體編製索引，以及從文件擷取文字"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/08/2016"
ms.author="eugenesh" />

# 使用 Azure 搜尋服務在 Azure Blob 儲存體中對文件編制索引

本文說明如何使用 Azure 搜尋服務對儲存在 Azure Blob 儲存體的文件編製索引 (例如 PDF、Microsoft Office 文件和數種其他通用格式)。新的 Azure 搜尋服務 Blob 索引子可以讓此程序快速且順暢。

> [AZURE.IMPORTANT] 這項功能目前為預覽狀態。僅適用於使用 **2015-02-28-Preview** 版本的 REST API。請記住，預覽 API 是針對測試與評估，不應該用於生產環境。

## 支援的文件格式

blob 索引子可以從下列文件格式擷取文字：

- PDF
- Microsoft Office 格式：DOCX/DOC、XLSX/XLS、PPTX/PPT、MSG (Outlook 電子郵件)
- HTML
- XML
- ZIP
- EML
- 純文字檔案
- JSON (如需詳細資訊，請參閱[編製索引 JSON Blob](search-howto-index-json-blobs.md))
- CSV (如需詳細資訊，請參閱[編製索引 CSV Blob](search-howto-index-csv-blobs.md))

## 設定 blob 編製索引

若要設定 Azure Blob 儲存體索引子，您可以使用 Azure 搜尋服務 REST API 以建立和管理**索引子**及**資料來源**，如[本文](https://msdn.microsoft.com/library/azure/dn946891.aspx)中所述。在未來，對 blob 索引的支援將會新增至 Azure 搜尋服務 .NET SDK 和 Azure 入口網站。

若要設定索引子，請執行下列三個步驟︰建立資料來源、建立索引、設定索引子。

### 步驟 1：建立資料來源

資料來源能指定哪項資料要編製索引、存取資料需要哪些認證，以及哪些政策能讓 Azure 搜尋服務有效識別資料變更 (新增、修改或刪除的資料列)。資料來源可供同一個訂用帳戶中的多個索引子使用。

若要為 Blob 編製索引，資料來源必須具有下列必要屬性︰

- **名稱**是搜尋服務中資料來源的唯一名稱。

- **類型**必須是 `azureblob`。

- **認證**可提供儲存體帳戶連接字串來做為 `credentials.connectionString` 參數。您可以從 Azure 入口網站取得連接字串︰瀏覽至所需的儲存體帳戶刀鋒視窗 > [設定] > [索引鍵]，並使用「主要連接字串」或「次要連線字串」值。連接字串已繫結至儲存體帳戶，因此以隱含方式指定連接字串可識別提供資料的儲存體帳戶。

- **容器**會指定儲存體帳戶中的容器。根據預設，容器內的所有 Blob 都可擷取。如果您只想要在特定虛擬目錄為 Blob 編製索引，您可以使用選擇性的**查詢**參數指定該目錄。

下列範例說明資料來源定義︰

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
	}   

如需建立資料來源 API 的詳細資訊，請參閱[建立資料來源](search-api-indexers-2015-02-28-preview.md#create-data-source)。

### 步驟 2：建立索引 

索引會指定文件、屬性和其他建構中可形塑搜尋體驗的欄位。

若要為 Blob 編製索引，請確認您的索引具有可搜尋的 `content` 欄位以儲存 Blob。

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
  		]
	}

如需建立索引 API 的詳細資訊，請參閱[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/dn798941.aspx)。

### 步驟 3：建立索引子 

索引子會連接資料來源與目標搜尋索引，並提供排程資訊以便您可以自動進行資料重新整理。一旦索引和資料來源建立好，要建立參考資料來源和目標索引的索引子就變得相當容易。例如：

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "blob-indexer",
	  "dataSourceName" : "blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

這個索引子每隔兩小時就會執行一次 (已將排程間隔設為 "PT2H")。若每隔 30 分鐘就要執行索引子，可將間隔設為 "PT30M"。支援的最短間隔為 5 分鐘。排程是選擇性 - 如果省略，索引子只會在建立時執行一次。不過，您隨時都可依需求執行索引子。

如需建立索引子 API 的詳細資訊，請參閱[建立索引子](search-api-indexers-2015-02-28-preview.md#create-indexer)。


## 文件擷取程序

Azure 搜尋服務會對每個文件 (blob) 編制索引，如下所示：

- 文件的全部文字內容會擷取至名為 `content` 的字串欄位。請注意，我們目前不支援從單一 blob 擷取多個文件：
	- 例如，CSV 檔案會編制索引為單一文件。如果您需要將 CSV 中的每一行視為個別的文件，請投票使用[此 UserVoice 建議](https://feedback.azure.com/forums/263029-azure-search/suggestions/13865325-please-treat-each-line-in-a-csv-file-as-a-separate)。
	- 複合或內嵌文件 (例如 ZIP 封存或具有內嵌 PDF 附件的 Outlook 電子郵件的 Word 文件) 也會編制索引為單一文件。

- 顯示在 blob 中的使用者指定中繼資料屬性 (如果有的話)，會逐字擷取。中繼資料屬性也可用來控制某些方面的文件擷取程序，如需詳細資訊，請參閱[使用自訂中繼資料以控制文件擷取](#CustomMetadataControl)。

- 標準 blob 中繼資料屬性會擷取到下列欄位：

	- **metadata\_storage\_name** (Edm.String) - blob 的檔案名稱。例如，如果您有 blob /my-container/my-folder/subfolder/resume.pdf，這個欄位的值是 `resume.pdf`。

	- **metadata\_storage\_path** (Edm.String) - blob 的完整 URI，包括儲存體帳戶。例如，`https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

	- **metadata\_storage\_content\_type** (Edm.String) - 內容類型，如同您用來上傳 blob 的程式碼所指定。例如，`application/octet-stream`。

	- **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) - 上次修改 blob 的時間戳記。Azure 搜尋服務會使用此時間戳記來識別已變更的 blob，以避免在初始編製索引之後重新對所有項目編制索引。

	- **metadata\_storage\_size** (Edm.Int64) - blob 大小 (位元組)。

	- **metadata\_storage\_content\_md5** (Edm.String) - blob 內容的 MD5 雜湊，如果有的話。

- 每個文件格式特有的中繼資料屬性會擷取到[這裡](#ContentSpecificMetadata)列出的欄位。

您不需要在您的搜尋索引中針對上述所有屬性定義欄位 - 只擷取您的應用程式所需的屬性。

> [AZURE.NOTE] 通常，您現有的索引中的欄位名稱會與文件擷取期間所產生的欄位名稱不同。您可以使用 [欄位對應] 將 Azure 搜尋服務提供的屬性名稱對應至您的搜尋索引中的欄位名稱。您會在下面看到使用欄位對應的範例。

## 挑選文件索引鍵欄位，然後處理不同的欄位名稱

在 Azure 搜尋服務中，文件索引鍵會唯一識別文件。每個搜尋索引必須確實具有一個 Edm.String 類型的索引鍵欄位。要新增至索引的每個文件需要有索引鍵欄位 (實際上它是唯一必要的欄位)。
   
您應該仔細考慮哪一個擷取的欄位應該對應至您的索引的索引鍵欄位。候選對象是：

- **metadata\_storage\_name** - 這可能是方便的候選對象，但是請注意，1) 名稱可能不是唯一的，因為您在不同的資料夾中可能會有相同名稱的 blob，以及 2) 名稱可能包含在文件所索引鍵中無效的字元，例如連字號。您可以藉由在索引子屬性中啟用 `base64EncodeKeys` 選項，處理無效的字元 - 如果您這麼做，請記得在將它們傳入例如「查閱」的 API 呼叫時，對文件索引鍵進行編碼。(例如，在 .NET 中您可以針對該目的使用 [UrlTokenEncode 方法](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx))。

- **metadata\_storage\_path** - 使用完整路徑以確保唯一性，但是路徑明確包含 `/` 字元，該字元[在文件索引鍵中無效](https://msdn.microsoft.com/library/azure/dn857353.aspx)。如上所述，您可以選擇使用 `base64EncodeKeys` 選項來編碼索引鍵。

- 如果上述任何選項都不適合，您有最終的彈性選項，就是將自訂中繼資料屬性新增至 blob。但是，此選項需要您的 blob 上傳程序，將該中繼資料屬性新增至所有 blob。因為索引鍵是必要屬性，所以沒有該屬性的所有 blob 都無法編製索引。

> [AZURE.IMPORTANT] 如果在索引中沒有索引鍵欄位的明確對應，Azure 搜尋服務會自動使用 `metadata_storage_path` (上述的第二個選項) 做為索引鍵並且啟用 base-64 的索引鍵編碼。

對於此範例，讓我們挑選 `metadata_storage_name` 欄位做為文件索引鍵。同時假設您的索引具有名為 `key` 的索引鍵欄位和欄位 `fileSize`，來儲存文件大小。若要連接所需的項目，在建立或更新您的索引子時，指定下列欄位對應：

	"fieldMappings" : [
	  { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	  { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	]

若要將所有項目整合在一起，以下是您新增欄位對應和針對現有索引子啟用 base-64 索引鍵編碼的方式：

	PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
	  "fieldMappings" : [
	    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	  ],
	  "parameters" : { "base64EncodeKeys": true }
	}

> [AZURE.NOTE] 若要深入了解欄位對應，請參閱[這篇文章](search-indexer-field-mappings.md)。

## 增量編製索引和刪除偵測

當您設定 blob 索引子排程執行時，它只會重新編制索引變更的 blob，由 blob 的 `LastModified` 時間戳記決定。

> [AZURE.NOTE] 您不需要指定變更偵測原則 – 會自動為您啟用增量編制索引。

若要指示必須從索引中移除特定文件，您應該使用虛刪除策略，而不是刪除對應的 blob，新增自訂中繼資料屬性以表示他們正在刪除，而且在資料來源上設定虛刪除偵測原則。

> [AZURE.WARNING] 如果您只刪除 blob，而非使用刪除偵測原則，對應的文件將不會從搜尋索引中移除。

例如，如果 blob 有值為 `true` 的中繼資料屬性 `IsDeleted`，則以下顯示的原則會認為 blob 已刪除：

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
		"container" : { "name" : "my-container", "query" : "my-folder" },
		"dataDeletionDetectionPolicy" : {
			"@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", 	
			"softDeleteColumnName" : "IsDeleted",
			"softDeleteMarkerValue" : "true"
		}
	}   

<a name="ContentSpecificMetadata"></a>
## 內容類型特定的中繼資料屬性

下表摘要說明針對每個文件格式完成的處理，並且說明 Azure 搜尋服務擷取的中繼資料屬性。

文件格式/內容類型 | 內容類型特定的中繼資料屬性 | 處理詳細資料
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | 移除 HTML 標記並且擷取文字
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| 擷取文字，包括內嵌文件 (不含影像)
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | 擷取文字，包括內嵌文件
DOC (application/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | 擷取文字，包括內嵌文件
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | 擷取文字，包括內嵌文件
XLS (application/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | 擷取文字，包括內嵌文件
PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | 擷取文字，包括內嵌文件
PPT (application/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | 擷取文字，包括內嵌文件
MSG (application/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | 擷取文字，包括附件
ZIP (application/zip) | `metadata_content_type` | 從封存中的所有文件擷取文字
XML (application/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 移除 XML 標記並且擷取文字
JSON (application/json) | `metadata_content_type`</br>`metadata_content_encoding` | 擷取文字<br/>注意：如果您需要從 JSON Blob 擷取多個文件欄位，請參閱[編製索引 JSON Blob](search-howto-index-json-blobs.md) 的詳細資訊
EML (message/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | 擷取文字，包括附件
純文字 (text/plain) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## 使用自訂中繼資料以控制文件擷取

您可以將中繼資料屬性新增至 blob 來控制某些層面的 blob 編製索引和文件擷取程序。目前支援下列屬性：

屬性名稱 | 屬性值 | 說明
--------------|----------------|------------
AzureSearch\_Skip | "true" | 指示 blob 索引子完全略過 blob，不會嘗試中繼資料或內容擷取。當您想要略過某些內容類型，或者當特定 blob 一直失敗，並且中斷編製索引程序時，這非常有用。
AzureSearch\_SkipContent | "true" | 指示 blob 索引子僅編制索引中繼資料，並略過 blob 的解壓縮內容。如果 blob 內容不有趣，但是您仍然要對附加至 blob 的中繼資料編制索引，這非常有用。

<a name="IndexerParametersConfigurationControl"></a>
## 使用索引子參數來控制文件擷取

有數個索引子設定參數可用來控制哪些 Blob，以及 Blob 內容和中繼資料的哪些部分會編製成索引。

### 只將具有特定副檔名的 Blob 編製成索引

您可以使用 `indexedFileNameExtensions` 索引子組態參數，只將具有指定副檔名的 Blob 編製成索引。值是包含副檔名 (有前置句點) 逗號分隔清單的字串。例如，若只要將 .PDF 和 .DOCX Blob 編製成索引，請執行這項操作︰

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
	}

### 編製索引時排除具有特定副檔名的 Blob

您可以使用 `excludedFileNameExtensions` 組態參數，在編製索引時排除具有特定副檔名的 Blob。值是包含副檔名 (有前置句點) 逗號分隔清單的字串。例如，若要將除 .PNG 和 .JPEG 副檔名以外的所有 Blob 都編製成索引，請執行下列動作︰

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
	}

如果同時有 `indexedFileNameExtensions` 和 `excludedFileNameExtensions` 參數，Azure 搜尋服務會先查看 `indexedFileNameExtensions`，再查看 `excludedFileNameExtensions`。這表示，如果兩份清單中有相同的副檔名，就會排除在索引編製外。

### 只編製儲存體中繼資料的索引

您可以使用 `indexStorageMetadataOnly` 組態屬性只編製儲存體中繼資料的索引，完全略過文件擷取程序。當您不需要文件內容，也不需要任何特定類型內容的中繼資料屬性時，這非常有用。若要這樣做，將 `indexStorageMetadataOnly` 屬性設為 `true`：

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexStorageMetadataOnly" : true } }
	}

### 編製儲存體和內容類型中繼資料的索引，但略過內容擷取。

如果您需要擷取所有中繼資料但跳過所有 Blob 的內容擷取，可以使用索引子組態來要求這個行為，而不必個別將 `AzureSearch_SkipContent` 中繼資料加入每個 Blob 中。若要這樣做，將 `skipContent` 索引子組態屬性設為 `true`：

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "skipContent" : true } }
	}

## 協助我們改進 Azure 搜尋服務

如果您有功能要求或改進的想法，請在我們的 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)與我們連絡。

<!---HONumber=AcomDC_0810_2016------>