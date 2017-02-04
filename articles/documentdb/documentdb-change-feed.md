---
title: "使用 Azure DocumentDB 中的變更摘要支援 | Microsoft Docs"
description: "使用 Azure DocumentDB 的「變更摘要」支援來追蹤 DocumentDB 文件中的變更，並執行以事件為基礎的處理 (例如觸發程序) 以及讓快取和分析系統保持最新狀態。"
keywords: "變更摘要"
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 12/13/2016
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: b22e75264345bc9d155bd1abc1fdb6e978dfad04
ms.openlocfilehash: bafc50750381616ecf30c4e41090f342d82007f9


---
# <a name="working-with-the-change-feed-support-in-azure-documentdb"></a>使用 Azure DocumentDB 中的變更摘要支援
[Azure DocumentDB](documentdb-introduction.md) 是快速且具有彈性的 NoSQL 資料庫服務，可用來儲存大量的交易式和操作資料，且具有可預測的讀取和寫入個位數毫秒延遲。 這使得它適合用於 IoT、遊戲、零售，以及操作記錄應用程式。 這些應用程式中的一個常見設計模式，便是追蹤 DocumentDB 資料的變更，並且根據這些變更來更新具體化檢視、執行即時分析、將資料封存到冷儲存體，以及在特定事件上觸發通知。 DocumentDB 的「變更摘要」支援允許您針對每一個模式建置有效率且可調整的解決方案。

透過「變更摘要」支援，DocumentDB 會提供一份 DocumentDB 集合內的文件排序清單，並以文件的修改順序做出排序。 此摘要可用來接聽集合內的資料修改，並執行下列動作：

* 於插入或修改文件時觸發 API 呼叫
* 執行即時 (串流) 更新處理
* 與快取、搜尋引擎或資料倉儲進行資料同步處理

DocumentDB 中的變更會加以保存，且可以進行非同步處理，並分散到一或多個取用者以進行平行處理。 讓我們看看「變更摘要」的 API，以及您如何使用 API 來建置可調整的即時應用程式。

![使用 DocumentDB「變更摘要」以提供即時分析和事件導向運算案例](./media/documentdb-change-feed/changefeed.png)

## <a name="use-cases-and-scenarios"></a>使用個案和案例
「變更摘要」允許透過大量寫入來有效處理大型資料集，並提供查詢整個資料集以識別變更的項目的替代方式。 例如，您可以有效率地執行下列工作︰

* 透握儲存在 Azure DocumentDB 中的資料更新快取、搜尋索引或資料倉儲。
* 實作應用程式層級資料階層處理和資料保存，也就是在 DocumentDB 中儲存「熱資料」，並將「冷資料」淘汰到 [Azure Blob 儲存體](../storage/storage-introduction.md)或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)。
* 使用 [Apache Hadoop](documentdb-run-hadoop-with-hdinsight.md) 在資料上實作批次分析。
* 透過 DocumentDB [在 Azure 上實作 Lambda 管線 (英文)](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)。 DocumentDB 提供一個可調整的資料庫解決方案，可以處理擷取和查詢，並實作具有低 TCO 的 Lambda 架構。 
* 透過不同的資料分割配置，執行零停機時間移轉至另一個 Azure DocumentDB 帳戶。

**搭配 Azure DocumentDB 使用 Lambda 管線進行擷取和查詢：**

![適用於擷取和查詢的 Azure DocumentDB 型 Lambda 管線](./media/documentdb-change-feed/lambda.png)

您可以使用 DocumentDB 來接收與儲存來自裝置、感應器、基礎結構和應用程式的事件資料，並透過 [Azure 串流分析](documentdb-search-indexer.md)、[Apache Storm](../hdinsight/hdinsight-storm-overview.md) 或 [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md) 即時處理這些事件。 

在 Web 和行動應用程式內，您可以追蹤例如變更客戶設定檔、喜好設定或位置等的事件，以觸發像是使用 [Azure Functions](../azure-functions/functions-bindings-documentdb.md) 或[應用程式服務](https://azure.microsoft.com/services/app-service/)傳送推播通知到客戶裝置的特定動作。 例如，如果您使用 DocumentDB 來建置遊戲，您可以根據完成遊戲的分數，使用「變更摘要」來實作即時排行榜。

## <a name="how-change-feed-works-in-azure-documentdb"></a>變更摘要在 Azure DocumentDB 中的運作方式
DocumentDB 提供以累加方式讀取對 DocumentDB 集合做出之更新的功能。 此變更摘要具有下列屬性：

* 變更會在 DocumentDB 中持續保存，並且可以非同步處理。
* 集合內的文件變更會立即在變更摘要中提供。
* 每個文件變更僅會在變更摘要中出現一次。 變更記錄檔中只會包含指定文件的最新變更。 中繼變更可能無法使用。
* 變更摘要會依照各個資料分割索引鍵值內的修改順序排序。 跨資料分割索引鍵值順序不會是固定的。
* 變更可以從任何時間點同步處理，也就是說，可用變更沒有固定的資料保留期限。
* 變更會以資料分割索引鍵區塊為單位提供。 這項功能可讓大型集合的變更由多個取用者/伺服器平行處理。
* 應用程式可以在相同的集合上同時要求多個「變更摘要」。

根據預設，DocumentDB 的「變更摘要」會針對所有帳戶啟用，且不會對您的帳戶產生任何額外的成本。 和 DocumentDB 的任何其他作業相同，您可以在您寫入區域或任何[讀取區域](documentdb-distribute-data-globally.md)中使用[佈建輸送量](documentdb-request-units.md)以讀取變更摘要。 變更摘要包含對集合內文件進行的插入與更新作業。 您可以擷取刪除項目，方法是在您的文件內設定「虛刪除」旗標來取代刪除動作。 或者，您可以透過 [TTL 功能](documentdb-time-to-live.md)針對您的文件設定有限的逾期期限 (例如 24 小時)，並使用該屬性的值擷取刪除項目。 使用此解決方案，您必須在比 TTL 逾期期限更短的時間間隔內處理變更。 文件集合內每個資料分割索引鍵範圍都可使用變更摘要，因此可以分散到一或多個取用者以進行平行處理。 

![DocumentDB 變更摘要的分散式處理](./media/documentdb-change-feed/changefeedvisual.png)

在下一節中，我們會說明如何使用 DocumentDB REST API 和 SDK 存取摘要變更。

## <a name="working-with-the-rest-api-and-sdk"></a>使用 REST API 和 SDK
DocumentDB 提供彈性的儲存體及輸送量容器，稱為**集合**。 集合內的資料會針對延展性和效能，使用[資料分割索引鍵](documentdb-partition-data.md)以邏輯方式分組。 DocumentDB 提供各種用來存取此資料的 API，包括依識別碼查閱 (Read/Get)、查詢及讀取摘要 (掃描) 進行查詢。 變更摘要可以透過將兩個新的要求標頭填入至 DocumentDB 的 `ReadDocumentFeed` API 來取得，並且可以跨資料分割索引鍵的範圍來平行處理。

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
讓我們快速看一下 ReadDocumentFeed 的運作方式。 DocumentDB 支援透過 `ReadDocumentFeed` API 讀取集合內文件的摘要。 例如，下列要求會傳回 `serverlogs` 集合內文件的頁面。 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/smallcoll HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

可以使用 `x-ms-max-item-count` 標頭限制結果，且可以透過重新提交要求 (包含先前回應中傳回的 `x-ms-continuation` 標頭) 繼續先前的讀取。 當從單一用戶端執行時，`ReadDocumentFeed` 會以序列方式逐一查看資料分割之間的結果。 

**序列讀取文件摘要**

![DocumentDB ReadDocumentFeed 序列執行](./media/documentdb-change-feed/readfeedserial.png)

您也可以使用其中一個支援的 [DocumentDB SDK](documentdb-sdk-dotnet.md) 擷取文件摘要。 例如，下列程式碼片段會示範如何在 .NET 中執行 ReadDocumentFeed。

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

> [!NOTE]
> 「變更摘要」需要 SDK 1.11.0 和更新版本 (目前在私人預覽中提供)

### <a name="distributed-execution-of-readdocumentfeed"></a>ReadDocumentFeed 的分散式執行
針對包含數 TB 以上資料或需內嵌大量更新的集合，序列執行單一用戶端電腦的讀取摘要可能不夠實際。 為了支援這些巨量資料案例，DocumentDB 提供在多個用戶端讀取者/取用者之間明確分散 `ReadDocumentFeed` 呼叫的 API。 

**分散式讀取文件摘要**

![DocumentDB ReadDocumentFeed 分散式執行](./media/documentdb-change-feed/readfeedparallel.png)

若要提供可調整的累加式變更處理，DocumentDB 會根據資料分割索引鍵的範圍，支援變更摘要 API 的向外延展模型。

* 您可以針對執行 `ReadPartitionKeyRanges` 呼叫的集合取得資料分割索引鍵範圍的清單。 
* 針對每個資料分割索引鍵範圍，您可以執行 `ReadDocumentFeed` 來讀取該範圍內資料分割索引鍵的文件。

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>擷取集合的資料分割索引鍵範圍
您可以藉由要求集合內的 `pkranges` 資源來擷取資料分割索引鍵範圍。 例如，下列要求會擷取 `serverlogs` 集合的資料分割索引鍵範圍清單：

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

此要求會傳回下列包含資料分割索引鍵範圍相關中繼資料的回應：

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**資料分割索引鍵範圍屬性**：每個資料分割索引鍵範圍包含下表中的中繼資料屬性︰

<table>
    <tr>
        <th>標頭名稱</th>
        <th>說明</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>資料分割索引鍵範圍的識別碼。 這是每個集合內穩定且唯的一識別碼。</p>
            <p>必須在下列呼叫中使用，以依據資料分割索引鍵範圍讀取變更。</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>資料分割索引鍵範圍的最大資料分割索引鍵雜湊值。 供內部使用。</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>資料分割索引鍵範圍的最小資料分割索引鍵雜湊值。 供內部使用。</td>
    </tr>       
</table>

您可以使用其中一個支援的 [DocumentDB SDK](documentdb-sdk-dotnet.md) 來這麼做。 例如，下列程式碼片段顯示如何在 .NET 中擷取資料分割索引鍵範圍。

    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();
    FeedResponse<PartitionKeyRange> response;

    do
    {
        response = await client.ReadPartitionKeyRangeFeedAsync(collection);
        partitionKeyRanges.AddRange(response);
    }
    while (response.ResponseContinuation != null);

DocumentDB 支援針對每個資料分割索引鍵範圍進行文件擷取，方法是設定選擇性的 `x-ms-documentdb-partitionkeyrangeid` 標頭。 

### <a name="performing-an-incremental-readdocumentfeed"></a>執行累加式 ReadDocumentFeed
ReadDocumentFeed 支援下列在 DocumentDB 集合中進行累加式變更處理的案例/工作：

* 從開始 (也就是集合建立開始) 讀取所有文件變更。
* 從目前時間讀取包含未來更新的所有文件變更。
* 從集合邏輯版本 (ETag) 讀取所有文件變更。 您可以根據從累加式讀取摘要要求傳回的 ETag，將您的取用者設為檢查點。

變更包含對文件進行的插入和更新。 若要擷取刪除項目，您必須在您的文件內使用「虛刪除」屬性，或使用[內建 TTL 屬性](documentdb-time-to-live.md)在變更摘要中指示暫止刪除。

下表列出 ReadDocumentFeed 作業的要求和回應標頭。

**累加式 ReadDocumentFeed 的要求標頭**：

<table>
    <tr>
        <th>標頭名稱</th>
        <th>說明</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>必須設定為「累加式摘要」或省略</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>No header：傳回從一開始 (集合建立) 的所有變更</p>
            <p>"*"：傳回集合內資料的所有新變更</p>
            <p>&lt;etag&gt;：如果設定為集合 ETag，則傳回邏輯時間戳記之後所做的所有變更</p>
        </td>
    </tr>
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>用來讀取資料的資料分割索引鍵範圍識別碼。</td>
    </tr>
</table>

**累加式 ReadDocumentFeed 的回應標頭**：

<table>
    <tr>
        <th>標頭名稱</th>
        <th>說明</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>回應中最後傳回文件的邏輯序號 (LSN)。</p>
            <p>透過在 If-None-Match 中重新提交此值，可以繼續執行累加式 ReadDocumentFeed。</p>
        </td>
    </tr>
</table>

以下是從邏輯版本/ETag `28535`和資料分割索引鍵範圍 = `16` 傳回集合中所有累加變更的範例要求：

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

變更會依照資料分割索引鍵範圍內每個資料分割索引鍵值內的時間加以排序。 跨資料分割索引鍵值順序不會是固定的。 如果結果無法容納於單一頁面，您可以透過重新提交包含 `If-None-Match` 標頭的要求，以及和上一個回應中的 `etag` 相同的值，來讀取下一頁的結果。 如果多個文件已在預存程序或觸發程序內以交易式方式更新，則文件會在相同的回應頁面中傳回。

.NET SDK 提供了 `CreateDocumentChangeFeedQuery` 和 `ChangeFeedOptions` 協助程式類別，以存取對集合所做的變更。 下列程式碼片段示範如何使用來自單一用戶端的 .NET SDK 來擷取從一開始的所有變更。

    private async Task<Dictionary<string, string>> GetChanges(
        DocumentClient client,
        string collection,
        Dictionary<string, string> checkpoints)
    {
        List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();
        FeedResponse<PartitionKeyRange> pkRangesResponse;

        do
        {
            pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(collection);
            partitionKeyRanges.AddRange(pkRangesResponse);
        }
        while (pkRangesResponse.ResponseContinuation != null);

        foreach (PartitionKeyRange pkRange in partitionKeyRanges)
        {
            string continuation = null;
            checkpoints.TryGetValue(pkRange.Id, out continuation);

            IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
                collection,
                new ChangeFeedOptions
                {
                    PartitionKeyRangeId = pkRange.Id,
                    StartFromBeginning = true,
                    RequestContinuation = continuation,
                    MaxItemCount = 1
                });

            while (query.HasMoreResults)
            {
                FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

                foreach (DeviceReading changedDocument in readChangesResponse)
                {
                    Console.WriteLine(changedDocument.Id);
                }

                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
        }

        return checkpoints;
    }

下列程式碼片段顯示如何使用「變更摘要」支援和先前的函式，透過 DocumentDB 即時處理變更。 第一次呼叫會傳回集合中所有的文件，而第二次呼叫只會傳回於最後一個檢查點之後建立的兩個文件。

    // Returns all documents in the collection.
    Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

    // Returns only the two documents created above.
    checkpoints = await GetChanges(client, collection, checkpoints);


您也可以使用用戶端邏輯以選擇性地處理事件，來篩選變更摘要。 例如，以下是使用用戶端 LINQ 處理僅來自裝置感應器之溫度變更事件的程式碼片段。

    FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

    foreach (DeviceReading changedDocument in 
        readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
    {
        // trigger an action, like call an API
    }

在本文中，我們提供 DocumentDB「變更摘要」支援的逐步解說，以及如何使用 DocumentDB REST API 及/或 SDK 追蹤對 DocumentDB 資料所進行的變更。 

## <a name="next-steps"></a>後續步驟
* 請嘗試 [Github 上的 DocumentDB 變更摘要程式碼範例 (英文)](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)
* 深入了解 [DocumentDB 的資源模型和階層](documentdb-resources.md)
* 使用 [DocumentDB SDK](documentdb-sdk-dotnet.md) 或 [REST API (英文)](https://msdn.microsoft.com/library/azure/dn781481.aspx) 開始撰寫程式碼


<!--HONumber=Dec16_HO2-->


