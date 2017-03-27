---
title: "Azure DocumentDB 的資料分割與調整規模 | Microsoft Docs"
description: "了解資料分割在 Azure DocumentDB 中的運作方式、如何設定資料分割和資料分割索引鍵，以及如何為您的應用程式挑選合適的資料分割索引鍵。"
services: documentdb
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 67d817c04672979ec8af8a540c5a63eb4df9bf6a
ms.lasthandoff: 03/15/2017


---
# <a name="partitioning-partition-keys-and-scaling-in-documentdb"></a>Azure DocumentDB 的資料分割、資料分割索引鍵和調整規模

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 的設計可協助您達成快速且可預測的效能，並順暢地隨著應用程式的成長而調整規模。 本文概述 DocumentDB 中資料分割運作方式的概觀，並描述可如何設定 DocumentDB 集合以有效地地調整應用程式規模。

閱讀本文後，您將能夠回答下列問題：   

* Azure DocumentDB 中資料分割的運作方式為何？
* 如何在 DocumentDB 中設定資料分割？
* 什麼是資料分割索引鍵，以及如何為我的應用程式選擇正確的資料分割索引鍵？

若要開始使用程式碼，請從 [DocumentDB 效能測試驅動程式範例](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark)下載專案。 

Scott Hanselman 和 DocumentDB 工程總經理 Shireesh Thota 也會在這段 Azure Friday 影片中說明資料分割和分割索引鍵。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-documentdb"></a>DocumentDB 中的資料分割
您在 DocumentDB 中可儲存及查詢無結構描述的 JSON 文件，以及任何規模的毫秒順序回應時間。 DocumentDB 提供存放資料的容器，稱為「集合」 。 集合是邏輯資源，可以跨一或多個實體資料分割或伺服器分佈。 資料分割數目取決於根據集合儲存體大小與佈建輸送量的 DocumentDB。 DocumentDB 中的每個資料分割，都有固定數量與其相關聯的 SSD 儲存體，會進行複寫以提供高可用性。 分割管理完全是由 Azure DocumentDB 所管理，您不需要撰寫複雜程式碼或管理分割。 從儲存體和輸送量的角度來看，DocumentDB 集合「實際上並無限制」  。 

應用程式完全不會感受到資料分割作業的進行。 DocumentDB 支援快速的讀取與寫入、SQL 與 LINQ 查詢、JavaScript 形式的交易邏輯、一致性層級，以及透過呼叫單一集合資源的 REST API 進行更細微的存取控制。 此服務會處理跨資料分割所分散的資料，以及將查詢要求路由傳送至正確的資料分割。 

運作方式為何？ 當您在 DocumentDB 中建立集合時，您可以指定**資料分割索引鍵屬性**組態值。 此為文件中 DocumentDB 可用來在多部伺服器或多個資料分割之間分散資料的 JSON 屬性 (或路徑)。 DocumentDB 會將資料分割索引鍵值變成雜湊值，並使用雜湊的結果來判斷儲存 JSON 文件所在的資料分割。 具有相同資料分割索引鍵的所有文件，都會儲存在相同的資料分割中。 

例如，請考慮使用將員工和其部門資料儲存在 DocumentDB 中的應用程式。 為了依部門相應放大資料，讓我們選擇 `"department"` 作為資料分割索引鍵屬性。 DocumentDB 中的每個文件都必須包含必要的 `"id"` 屬性，而就每個具有相同資料分割索引鍵值 (例如 `"Marketing`") 的文件而言，這個屬性都不得重複。 儲存在集合中的每個文件都必須具有不重複的資料分割索引鍵與識別碼組合 (例如 `{ "Department": "Marketing", "id": "0001" }`、`{ "Department": "Marketing", "id": "0002" }` 和 `{ "Department": "Sales", "id": "0001" }`)。 換句話說，複合屬性 (資料分割索引鍵, 識別碼) 是集合的主要索引鍵。

DocumentDB 會儲存體大小與佈建的輸送量，在每個集合背後建立少量的實體資料分割。 您定義為資料分割索引鍵的屬性為邏輯資料分割。 多個資料分割索引鍵值通常會共用單一實體資料分割，但單一值永遠不會跨越資料分割。 資料分割索引鍵最好有許多值，因為 DocumentDB 可隨著資料成長或您增加佈建的輸送量，執行更理想的負載平衡。

例如，假設您建立的集合有每秒 25,000 個要求的輸送量，而且 DocumentDB 可支援單一實體資料分割每秒 10,000 個要求。 DocumentDB 會為您的集合建立 3 個實體資料分割 P1、 P2 和 P3。 在插入或讀取文件時，DocumentDB 服務會雜湊對應的 `Department` 值，以將資料對應至三個資料分割 P1、 P2 和 P3。 所以說，如果「行銷」和「銷售」雜湊為 1，則兩者都會儲存於 P1。 而如果 P1 已滿，則 DocumentDB 會將 P1 分割成兩個新的資料分割 P4 和 P5。 然後此服務可能會在分割後將「行銷」移至 P4、將「銷售」移至 P5，而後捨棄 P1。 對您的應用程式而言，資料分割之間的這些資料分割索引鍵移動是透明的，並不會影響到您的集合可用性。

## <a name="sharding-in-api-for-mongodb"></a>API for MongoDB 中的分區化
API for MongoDB 中的分區化集合會使用相同的基礎結構做為 DocumentDB 的資料分割集合。 如同資料分割集合，分區化集合可以有任意數目的分區，而每個分區有固定數量的 SSD 型儲存體數量與其相關聯。 從儲存體和輸送量的角度來看，分區化集合實際上並無限制。 API for MongoDB 的分區化索引鍵相當於 DocumentDB 的資料分割索引鍵，而在決定分區索引鍵時，請務必閱讀[資料分割索引鍵](#partition-keys)和[設計資料分割](#designing-for-partitioning)章節。

<a name="partition-keys"></a>
## <a name="partition-keys"></a>資料分割索引鍵
選擇資料分割索引鍵是在設計階段必須進行的一項重要決策。 您選擇的 JSON 屬性名稱必須具有各種不同的值，而且應該可以平均分散存取模式。 

> [!NOTE]
> 資料分割索引鍵最好有大量相異值 (至少數百至數千個)。 許多客戶會有效利用 DocumentDB 做為索引鍵值存放區，其中唯一的「識別碼」就是資料分割索引鍵，所以會有數百萬至數十億個資料分割索引鍵。
>

下表顯示資料分割索引鍵定義及其對應的 JSON 值的範例。 資料分割索引鍵會以 JSON 路徑的形式指定，例如 `/department` 代表 department 屬性。 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>資料分割索引鍵路徑</strong></p></td>
            <td valign="top"><p><strong>說明</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>對應至 doc.department 的 JSON 值，其中 doc 是指文件。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>對應至 doc.properties.name 的 JSON 值，其中 doc 是指文件 (巢狀屬性)。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>對應至 doc.id 的 JSON 值 (識別碼和資料分割索引鍵是相同屬性)。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"department name"</p></td>
            <td valign="top"><p>對應至 doc["department name"] 的 JSON 值，其中 doc 是指文件。</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> 資料分割索引鍵路徑的語法類似於指定路徑以為原則路徑編製索引，但主要的差別在於路徑會對應至屬性而不是值，也就是結尾沒有萬用字元。 例如，您可以指定 /department/? 以為 department 下的值編製索引，但您會將 /department 指定為資料分割索引鍵定義。 資料分割索引鍵路徑會以隱含方式編製索引，而且無法使用索引原則覆寫來排除編製索引。
> 
> 

讓我們看看資料分割索引鍵選擇對於應用程式效能的影響。

## <a name="partitioning-and-provisioned-throughput"></a>資料分割與佈建的輸送量
DocumentDB 設計用來取得可預測的效能。 當您建立集合時，需以**每秒的[要求單位](documentdb-request-units.md) (RU)**的方式保留輸送量。 每項要求都會指派有與系統資源 (例如作業所使用的 CPU 和 IO) 數量成正比的要求單位費用。 讀取 1 KB 具有工作階段一致性的文件，會使用 1 個要求單位。 不論儲存的項目數或同時執行的並行要求數，讀取一次都是 1 個 RU。 根據大小之不同，較大的文件需要較高的要求單位。 如果您知道實體大小以及支援您應用程式所需的讀取次數，則可以佈建應用程式讀取需求確實需要的輸送量。 

DocumentDB 儲存文件時，會根據資料分割索引鍵值將它們平均分散到不同的資料分割。 輸送量也會平均分散到可用的資料分割，即每個資料分割的輸送量 = (每個集合的總輸送量)/(資料分割數目)。 

> [!NOTE]
> 為達到集合的完整輸送量，您必須選擇資料分割索引鍵，讓您能將要求平均地分散到數個相異的資料分割索引鍵值。
> 
> 

## <a name="single-partition-and-partitioned-collections"></a>單一分割區和已分割集合
DocumentDB 支援建立單一分割和分割的集合。 

* **資料分割的集合**可以跨多個資料分割，並且支援無限制的儲存體與輸送量。 您必須為集合指定資料分割索引鍵。 
* **單一資料分割集合**擁有較低的價格選項，但在最低儲存體和輸送量方面有上限。 您不需要為這些集合指定資料分割索引鍵。 我們建議除了您預計只有少量的資料儲存體和要求的情況外，所有案例皆使用資料分割的集合而非單一資料分割集合。

![DocumentDB 中的資料分割集合][2] 

「資料分割的集合」可支援無限制的儲存體與輸送量。

下表列出使用單一資料分割和資料分割的集合之間的差異︰

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>單一分割區集合</strong></p></td>
            <td valign="top"><p><strong>已分割集合</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>資料分割索引鍵</p></td>
            <td valign="top"><p>None</p></td>
            <td valign="top"><p>必要</p></td>
        </tr>
        <tr>
            <td valign="top"><p>文件的主要索引鍵</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>複合索引鍵 &lt;資料分割索引鍵&gt; 和 "id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>儲存體下限</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>儲存體上限</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>無限</p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量下限</p></td>
            <td valign="top"><p>每秒&400; 個要求單位</p></td>
            <td valign="top"><p>每秒&2;,500 個要求單位</p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量上限</p></td>
            <td valign="top"><p>每秒&10;,000 個要求單位</p></td>
            <td valign="top"><p>無限</p></td>
        </tr>
        <tr>
            <td valign="top"><p>API 版本</p></td>
            <td valign="top"><p>全部</p></td>
            <td valign="top"><p>API 2015-12-16 與更新版本</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-documentdb-sdks"></a>使用 DocumentDB SDK
Azure DocumentDB 已藉由 [REST API 版本 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx)新增對自動資料分割的支援。 若要建立資料分割的集合，必須下載其中一個支援的 SDK 平台 (.NET、Node.js、Java、Python) 中的 SDK 1.6.0 版或更新版本。 

### <a name="creating-partitioned-collections"></a>建立資料分割的集合
下列範例顯示的 .NET 程式碼片段所建立之集合，可儲存輸送量每秒 20,000 個要求單位的裝置遙測資料。 SDK 會設定 OfferThroughput 值 (這會接著設定 REST API 中的 `x-ms-offer-throughput` 要求標頭)。 此處我們將 `/deviceId` 設定為資料分割索引鍵。 選擇的資料分割索引鍵會與其餘的集合中繼資料 (例如名稱與編製索引原則) 一併儲存。

在此範例中，我們挑選了 `deviceId` ，因為我們知道 (a) 由於有大量的裝置，因此可以將寫入平均分散到所有資料分割，讓我們能夠調整資料庫規模以內嵌大量資料，以及 (b) 許多要求 (例如，提取裝置的最新讀取) 會限定在單一 deviceId，而可以從單一資料分割擷取。

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

> [!NOTE]
> 若要使用 SDK 建立已分割集合，您必須指定等於或大於 10,100 RU/秒的輸送量值。 若要為已分割集合設定介於 2,500 與 10,000 之間的輸送量值，您必須暫時使用 Azure 入口網站，因為這些新的較低值尚無法在 SDK 中取得。
> 
> 

此方法會對 DocumentDB 進行 REST API 呼叫，且服務會根據要求的輸送量來佈建許多資料分割。 您可以隨著效能需求的發展變更集合的輸送量。 

### <a name="reading-and-writing-documents"></a>讀取和寫入文件
現在，我們將資料插入 DocumentDB 中。 以下是包含裝置讀取的範例類別，以及呼叫 CreateDocumentAsync 將新的裝置讀取插入集合中。

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

請依資料分割索引鍵和識別碼來讀取文件，並加以更新，然後最後一個步驟是依資料分割索引鍵和識別碼刪除文件。 請注意，讀取包括 PartitionKey 值 (對應至 REST API 中的 `x-ms-documentdb-partitionkey` 要求標頭)。

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-collections"></a>查詢資料分割的集合
當您在資料分割的集合中查詢資料時，DocumentDB 會自動將查詢路由傳送至資料分割 (對應至篩選中所指定的資料分割索引鍵值 (如果有的話))。 例如，此查詢只會路由傳送至包含資料分割索引鍵 "XMS-0001" 的資料分割。

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
下列查詢沒有根據資料分割索引鍵 (DeviceId) 的篩選，且已展開至對資料分割索引執行它的所有資料分割。 請注意，您必須指定 EnableCrossPartitionQuery (REST API 中的`x-ms-documentdb-query-enablecrosspartition` )，才能讓 SDK 跨資料分割執行查詢。

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

DocumentDB 使用 SQL (包含 SDK 1.12.0 和更新版本) 支援已分割集合的[彙總函式](documentdb-sql-query.md#Aggregates) `COUNT`、`MIN`、`MAX`、`SUM` 和 `AVG`。 查詢必須包含單一彙總運算子，而且在投射中必須包含單一值。

### <a name="parallel-query-execution"></a>平行查詢執行
DocumentDB SDK 1.9.0 和更新版本支援平行查詢執行選項，可讓您對分割集合執行低延遲查詢 (即使它們需要涉及大量的資料分割也一樣)。 例如，下列查詢設定為跨資料分割平行執行。

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
若要管理平行執行查詢，您可以調整下列參數︰

* 藉由設定 `MaxDegreeOfParallelism`，您可以控制平行處理原則的程度，亦即與集合的資料分割同時的網路連線數上限。 如果您將此設定為 -1，平行處理原則的程度是由 SDK 管理。 如果 `MaxDegreeOfParallelism` 未指定或設為 0 (這是預設值)，將會有連往集合資料分割的單一網路連線。
* 您可藉由設定 `MaxBufferedItemCount`，來權衡取捨查詢延遲和用戶端記憶體使用量。 如果您省略這個參數或將此設定為 -1，平行查詢執行期間緩衝處理的項目數是由 SDK 管理。

在相同的集合狀態下，平行查詢會以和序列執行相同的順序傳回結果。 執行包含排序 (ORDER BY 和/或 TOP) 的跨資料分割查詢時，DocumentDB SDK 會跨資料分割發出平行查詢，並合併用戶端中已部分排序的結果來產生全域排序的結果。

### <a name="executing-stored-procedures"></a>執行預存程序
您也可以對具有相同裝置識別碼的文件執行不可部分完成交易，例如，如果您正在維護彙總或處於單一文件中裝置的最新狀態。 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
    
在下一節中，我們會探討如何從單一資料分割集合改為資料分割的集合。

## <a name="creating-an-api-for-mongodb-sharded-collection"></a>建立 API for MongoDB 分區化集合
建立 API for MongoDB 分區化集合的最簡單方式是透過您喜愛的工具、驅動程式或 SDK。 在此範例中，我們將使用 Mongo 殼層建立集合。

在 Mongo 殼層中︰

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
結果：

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

<a name="migrating-from-single-partition"></a>

## <a name="migrating-from-single-partition-to-partitioned-collections-in-documentdb"></a>從 DocumentDB 中單一資料分割移轉至資料分割集合

> [!IMPORTANT]
> 如果您要匯入到 API for MongoDB，請遵循這些[指示](documentdb-mongodb-migrate.md)。
> 
> 

當使用單一資料分割集合的應用程式需要較高的輸送量 (>&10;,000 RU/秒) 或較大的資料儲存體 (>&10; GB) 時，您可以使用 [DocumentDB 資料移轉工具](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)將單一資料分割集合中的資料移轉至已分割集合。 

從單一資料分割集合移轉到資料分割的集合

1. 將資料從單一資料分割集合匯出至 JSON。 如需詳細資訊，請參閱 [匯出至 JSON 檔案](documentdb-import-data.md#export-to-json-file) 。
2. 在使用資料分割索引鍵定義所建立、每秒輸送量超過 2,500 個要求單位的資料分割的集合中匯入資料，如下列範例所示。 如需詳細資訊，請參閱 [匯入到 DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) 。

![將資料移轉至 DocumentDB 中的資料分割的集合][3]  

> [!TIP]
> 若要加快匯入速度，請考慮將 [平行要求數目] 增加為 100 以上以利用資料分割的集合所提供的較高輸送量。 
> 
> 

現在，我們已經完成基本概念，接著將查看在 DocumentDB 中使用資料分割索引鍵時的一些重要設計考量。

<a name="designing-for-partitioning"></a>
## <a name="designing-for-partitioning"></a>設計資料分割
選擇資料分割索引鍵是在設計階段必須進行的一項重要決策。 本節描述選取集合資料分割索引鍵時的一些取捨。

### <a name="partition-key-as-the-transaction-boundary"></a>資料分割索引鍵作為交易界限
您選擇的分割區索引鍵應兼顧交易使用和將實體分散到多個分割區索引鍵 (以確保可調整的解決方案) 的需求。 以一個極端的情況來說，您可以針對所有文件設定相同的分割區索引鍵，但如此可能會限制解決方案的延展性。 以另一個極端而言，您可以為每份文件指派唯一的分割區索引鍵以達到高調整性，但如此會透過預存程序和觸發程序，讓您無法使用跨文件交易。 理想的分割區索引鍵可讓您使用有效率的查詢，而且具有足夠的基數，可確保您的解決方案能加以調整。 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>避免儲存體和效能瓶頸
也請務必挑選允許將寫入分散到數個相異值的屬性。 相同資料分割索引鍵的要求，不能超過單一資料分割的輸送量，因此將進行節流。 因此，請務必挑選不會在應用程式內導致 **作用點** 的資料分割索引鍵。 因為單一分割索引鍵的所有資料皆必須儲存在資料分割中，也建議在遇到有大量相同值的資料時，避免分割索引鍵。 

### <a name="examples-of-good-partition-keys"></a>良好資料分割索引鍵的範例
以下是如何挑選應用程式之資料分割索引鍵的一些範例︰

* 如果您實作使用者設定檔後端，則使用者識別碼是不錯的資料分割索引鍵選擇。
* 如果您在儲存 IoT 資料 (例如，裝置狀態)，則裝置識別碼是不錯的資料分割索引鍵選擇。
* 如果您使用 DocumentDB 來記錄時間序列資料，則主機名稱或處理序識別碼很適合做為資料分割索引鍵。
* 如果您有多重租用戶架構，租用戶識別碼是不錯的資料分割索引鍵選擇。

請注意，在一些使用案例 (例如以上所述 IoT 與使用者設定檔) 中，資料分割索引鍵可能與您的識別碼 (文件索引鍵) 相同。 在其他使用案例 (例如時間序列資料) 中，您的資料分割索引鍵可能與識別碼不同。

### <a name="partitioning-and-loggingtime-series-data"></a>資料分割和記錄/時間序列資料
DocumentDB 最常見的其中一個使用案例是用在記錄與遙測。 您可能需要讀取/寫入大量資料，因此請務必挑選適當的資料分割索引鍵。 要做什麼選擇取決於讀取和寫入速率以及您預期要執行的查詢類型。 如何選擇適當資料分割索引鍵的一些秘訣如下。

* 如果您的使用案例牽涉到以較小的速率寫入已累積很長一段時間的資料，而且必須依時間戳記範圍和其他篩選器進行查詢，則使用彙總的時間戳記 (例如日期) 做為資料分割索引鍵是不錯的方法。 這可讓您查詢單一資料分割中某一天的所有資料。 
* 如果您的寫入工作負載繁重 (一般來說這是更常見的情形)，則不應該使用根據時間戳記的資料分割索引鍵，這樣一來，DocumentDB 才可以將寫入工作平均分散到多個資料分割。 在這個案例中，主機名稱、處理序識別碼、活動識別碼或其他具有高基數的屬性是不錯的選擇。 
* 第三種方法是混合式方法，在此方法中您會擁有多個集合，每天/每月各有一個集合，而且資料分割索引鍵是細微的屬性，例如主機名稱。 這樣的好處是，您可以根據時間範圍設定不同的輸送量，例如當月的集合會佈建較高的輸送量，因為它要處理讀取和寫入，而先前月份則佈建較低的輸送量，因為它們只要處理讀取。

### <a name="partitioning-and-multi-tenancy"></a>資料分割與多重租用
如果您實作使用 DocumentDB 的多重租用戶應用程式，則有兩種主要模式可使用 DocumentDB 實作租用：一個租用戶一個資料分割索引鍵，以及一個租用戶一個集合。 以下是每項方式的優缺點︰

* 一個租用戶一個資料分割索引鍵：在此模型中，租用戶共置於單一集合內。 但是，針對單一資料分割，可以執行單一租用戶內文件的查詢與插入。 您也可以跨租用戶內的所有文件實作交易邏輯。 因為多重租用戶共用一個集合，所以您可以節省儲存體和輸送量成本，方法是在單一集合內輪詢租用戶的資源，而不是為每個租用戶佈建額外的空餘空間。 缺點是未隔離每個租用戶的效能。 整個集合的效能/輸送量增加與鎖定特定租用戶的增加。
* 一個租用戶一個集合：每個租用戶都有其專屬集合。 在此模型中，您可以保留每個租用戶的效能。 使用 DocumentDB 的新耗用型計價模式，這種模式對於具有少數租用戶的多重租用戶應用程式最具成本效益。

您也可以使用組合/分層的方式，來共置少數租用戶，並將較大的租用戶移轉到其專屬集合。

## <a name="next-steps"></a>後續步驟
在本文中，我們已描述過在 Azure DocumentDB 中料分割的運作方式、如何建立資料分割的集合，以及如何為您的應用程式挑選適當的資料分割索引鍵。 

* 執行 DocumentDB 的相關規模和效能測試。 如需範例，請參閱 [Azure DocumentDB 的相關效能和規模測試](documentdb-performance-testing.md) 。
* 使用 [SDK](documentdb-sdk-dotnet.md) 或 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 開始撰寫程式碼
* 了解 [DocumentDB 中佈建的輸送量](documentdb-performance-levels.md)

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  



