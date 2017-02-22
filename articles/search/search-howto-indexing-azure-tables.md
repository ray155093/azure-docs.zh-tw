---
title: "使用 Azure 搜尋服務對 Azure 表格儲存體編制索引"
description: "了解如何使用 Azure 搜尋服務對 Azure 資料表中儲存的資料編制索引"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/18/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 19a652f81beacefd4a51f594f045c1f3f7063b59
ms.openlocfilehash: b7f6c92867e3fabe07312539ec8dfd2d3525f02e

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>使用 Azure 搜尋服務對 Azure 表格儲存體編制索引
本文章說明如何使用 Azure 搜尋服務來對儲存於 Azure 表格儲存體中的資料編制索引。 新的 Azure 搜尋服務資料表索引子可以讓此程序快速且順暢。

## <a name="setting-up-azure-table-indexing"></a>設定 Azure 資料表編制索引
若要設定 Azure 資料表索引子，您可以使用 Azure 搜尋服務 REST API 來建立和管理**索引子**及**資料來源**，如[索引子的作業](https://msdn.microsoft.com/library/azure/dn946891.aspx)中所述。 您也可以使用 .NET SDK 的 [版本 2.0 預覽版](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) 。 在未來，對資料表索引的支援將會新增至 Azure 入口網站。

資料來源能指定哪項資料要編製索引、存取資料需要哪些認證，以及哪些政策能讓 Azure 搜尋服務有效識別資料變更 (新增、修改或刪除的資料列)。

索引子會從資料來源讀取資料，然後將資料載入到目標搜尋索引。

設定資料表編製索引：

1. 建立資料來源
   * 將 `type` 參數設定為 `azuretable`
   * 傳遞您的儲存體帳戶連接字串做為 `credentials.connectionString` 參數。 請參閱下面的[如何指定認證](#Credentials)了解詳細資訊。
   * 使用 `container.name` 參數指定資料表名稱
   * (選擇性) 使用 `container.query` 參數指定查詢。 可能的話，在 PartitionKey 上使用篩選器以獲得最佳效能；任何其他的查詢將會造成執行完整資料表掃描，這可能會產生大型資料表而導致效能不佳。
2. 使用與您想要編製索引的資料表中的資料行對應的結構描述，建立搜尋索引。
3. 將資料來源連接到搜尋索引來建立索引子。

### <a name="create-data-source"></a>建立資料來源
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

如需建立資料來源 API 的詳細資訊，請參閱 [建立資料來源](https://msdn.microsoft.com/library/azure/dn946876.aspx)。

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>如何指定認證 ####

您可以採取下列其中一種方式提供資料表的認證︰ 

- **完整存取儲存體帳戶連接字串**：`DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`。 您可以從 Azure 入口網站取得連接字串︰瀏覽至儲存體帳戶刀鋒視窗 > [設定] > [金鑰] (傳統儲存體帳戶)，或 [設定] > [存取金鑰] (Azure Resource Manager 儲存體帳戶)。
- **儲存體帳戶共用存取簽章** (SAS) 連接字串︰`TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`。 SAS 對於容器 (在此案例中為資料表) 和物件 (資料表資料列) 應該擁有列出和讀取權限。
-  **資料表共用存取簽章**：`ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`。 SAS 對於資料表應該擁有列出和讀取權限。

如需儲存體共用存取簽章的詳細資訊，請參閱[使用共用存取簽章](../storage/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
> 如果您使用 SAS 認證，您必須使用更新的簽章定期更新資料來源認證以防止其到期。 如果 SAS 認證過期，索引子將會失敗並出現類似 `Credentials provided in the connection string are invalid or have expired.` 的錯誤訊息。  

### <a name="create-index"></a>建立索引
    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

如需建立索引 API 的詳細資訊，請參閱 [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>建立索引子
最後，建立參考資料來源和目標索引的索引子。 例如：

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](https://msdn.microsoft.com/library/azure/dn946899.aspx)。

就是這麼簡單 - 快樂地編制索引吧！

## <a name="dealing-with-different-field-names"></a>處理不同的欄位名稱
通常，您現有索引中的欄位名稱會與您資料表中的屬性名稱不同。 您可以使用 **欄位對應** ，將資料表中的屬性名稱對應至您搜尋索引中的欄位名稱。 若要深入了解欄位對應，請參閱 [Azure 搜尋服務索引子欄位對應會橋接資料來源和搜尋索引之間的差異](search-indexer-field-mappings.md)。

## <a name="handling-document-keys"></a>處理文件索引鍵
在 Azure 搜尋服務中，文件索引鍵會唯一識別文件。 每個搜尋索引必須確實具有一個 `Edm.String`類型的索引鍵欄位。 要新增至索引的每個文件都需要有索引鍵欄位 (實際上它是唯一必要的欄位)。

由於資料表資料列有複合索引鍵，Azure 搜尋服務會產生名為 `Key` 的綜合欄位，該欄位為資料分割索引鍵與資料列索引鍵值的串連。 例如，如果資料列的 PartitionKey 為 `PK1` 且 RowKey 是 `RK1`，則 `Key` 欄位的值是 `PK1RK1`。

> [!NOTE]
> `Key` 值可能包含在文件索引鍵中無效的字元，例如連字號。 您可以使用 `base64Encode` [欄位對應函式](search-indexer-field-mappings.md#base64EncodeFunction)來處理無效字元。 如果您這樣做，請記得在 API 呼叫 (例如查閱) 中傳遞文件索引鍵時，也使用 URL 安全 Base64 編碼。
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>增量編製索引和刪除偵測
當您將資料表索引子設為依排程執行時，它會根據資料列的 `Timestamp` 值來決定是否只對新的或已更新過的資料列重新編製索引。 您不需要指定變更偵測原則 – 會自動為您啟用增量編制索引。

若要指示必須從索引中移除特定文件，您可以使用虛刪除策略 – 新增屬性以表示它已遭到刪除，而非刪除資料列，並在資料來源上設定虛刪除偵測原則。 例如，如果資料列有值為 `"true"` 的屬性 `IsDeleted`，則以下顯示的原則會認為資料列已刪除：

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>協助我們改進 Azure 搜尋服務
如果您有功能要求或改進的想法，請在我們的 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)與我們連絡。



<!--HONumber=Jan17_HO3-->


