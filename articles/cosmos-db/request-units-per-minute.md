---
title: "Azure CosmosDB︰每分鐘的要求單位 (RU/m) | Microsoft Docs"
description: "了解如何利用每分鐘的要求單位降低成本。"
services: cosmos-db
documentationcenter: 
author: arnomicrosoft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: acomet
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ea63b988a72801ae4c288048021a915b20a34794
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017


---
# <a name="request-units-per-minute-in-azure-cosmos-db"></a>Azure Cosmos DB 之每分鐘的要求單位

Azure Cosmos DB 的設計可協助您達成快速且可預測的效能，並順暢地隨著應用程式的成長而調整規模。 您可以根據每秒和每分鐘 (RU/m) 的細微度，在 Cosmos DB 容器上佈建輸送量。 根據每分鐘細微度佈建的輸送量，用來管理發生在每秒細微度的非預期工作負載尖峰。 

本文提供每分鐘的要求單位 (RU/m) 佈建如何運作的概觀。 RU/m 佈建的主要目標是針對無法預期的需求和尖峰工作負載，提供可預測的效能 (尤其是如果您需要在作業資料上執行分析時)。 我們希望客戶盡可能取用他們佈建的輸送量，以便安心地快速調整。

閱讀本文後，您將能夠回答下列問題：

* 每分鐘的要求單位如何運作？
* 每分鐘的要求單位和每秒的要求單位有何差異？
* 如何佈建 RU/m？
* 在哪種情況下應該考慮佈建每分鐘的要求單位？
* 如何使用入口網站計量將成本和效能最佳化？
* 定義哪一種要求可能會耗用 RU/m 預算？

## <a name="provisioning-request-units-per-minute-rum"></a>佈建每分鐘的要求單位 (RU/m)

根據每秒細微度 (RU/s) 佈建 Azure Cosmos DB 時，只要輸送量不超過該秒內佈建的容量，就能保證您的要求會在低延遲情況下成功。 使用 RU/m 時，細微度為分鐘，並保證您的要求在該分鐘內會成功。 相較於暴增的系統，我們可確保您會獲得可預測的效能，讓您做好規劃。

每分鐘佈建的運作方式很簡單︰

* 除了 RU/s 之外，RU/m 還會每小時計費。 如需詳細資訊，請瀏覽 Azure Cosmos DB [價格頁面](https://aka.ms/acdbpricing)。
* 您可以在集合等級啟用 RU/m。 這可透過 SDK (Node.js、Java 或 .Net) 或入口網站 (也包括 MongoDB API 工作負載) 完成
* RU/m 啟用時，佈建每 100 RU/s，也會佈建 1,000 RU/m (比率為 10 倍)
* 在某一秒內，只有當您在該秒內已超過您的每秒佈建時，要求單位才會取用您的 RU/m 佈建
* 當 60 秒期間 (UTC) 結束之後，就會再填滿每分鐘佈建
* 只有對每個資料分割最多佈建 5,000 RU/s 的集合，才能啟用 RU/m。 如果您調整輸送量需求，而且每個資料分割達到如此高的佈建等級，您會收到警告訊息

以下是具體的範例，其中客戶可以佈建 10kRU/s 和 100kRU/m，在佈建 10,000 RU/s 和 100,000 RU/m 的集合上，90 秒期間的尖峰佈建 (50kRU/秒) 可節省 73% 的成本：

* 第 1 秒︰RU/m 預算設定在 100,000
* 第 3 秒︰在該秒內，取用的要求單位是 11,010 個 RU，比 RU/s 佈建多出 1,010 個 RU。 因此會從 RU/m 預算中扣除 1,010 個 RU。 在 RU/m 預算中，接下來 57 秒有 98,990 個 RU 可用
* 第 29 秒︰在該秒內，發生極高尖峰 (比每秒佈建高出 4 倍以上)，取用的要求單位是 46,920 個 RU。 RU/m 預算中會扣除 36,920 個 RU，從 92,323 個 RU (第 28 秒) 降到 55,403 個 RU (第 29 秒)
* 第 61 秒︰RU/m 預算重設回 100,000 個 RU。
 
![此圖表顯示 Azure Cosmos DB 的取用和佈建](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute.png)

## <a name="specifying-request-unit-capacity-with-rum"></a>使用 RU/m 指定要求單位容量

建立 Azure Cosmos DB 集合時，您可以指定想要保留給集合的每秒要求單位數 (每秒 RU)。 您也可以決定是否要新增每分鐘的 RU。 這可以透過入口網站或 SDK 來完成。 

### <a name="through-the-portal"></a>透過入口網站

佈建集合時只需要按一下，即可啟用或停用每分鐘的 RU。 

 ![顯示如何在 Azure 入口網站中設定 RU/m 的螢幕擷取畫面](./media/request-units-per-minute/azure-cosmos-db-request-unit-per-minute-portal.png)

### <a name="through-the-sdk"></a>透過 SDK
首先，必須注意 RU/m 僅適用於下列 SDK：

* .Net 1.14.0
* Java 1.11.0
* Node.js 1.12.0
* Python 2.2.0

以下的程式碼片段使用 .NET SDK，建立包含每秒 3,000 個要求單位和每分鐘 30,000 個要求單位的集合：

```csharp
// Create a collection with RU/m enabled
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

// Set the throughput to 3,000 request units per second which will give you 30,000 request units per minute as the RU/m budget
await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000, OfferEnableRUPerMinuteThroughput = true });
```

以下的程式碼片段使用 .NET SDK，將集合的輸送量變更為每秒 5,000 個要求單位，且不佈建每分鐘 RU：

```csharp
// Get the current offer
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to 5000 request units per second without RU/m enabled (the last parameter to OfferV2 constructor below)
OfferV2 offerV2 = new OfferV2(offer, 5000, false);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offerV2);
```

## <a name="good-fit-scenarios"></a>適合案例

在本節中，我們提供一些案例的概觀，這些案例適合啟用每分鐘的要求單位。

**開發/測試環境：**適合。 在開發階段，如果您以不同的工作負載測試您的應用程式，RU/m 在這個階段會很有彈性。 [模擬器](local-emulator.md)是適合用來測試 Azure Cosmos DB 的免費工具。 不過，如果您想要從雲端環境開始，RU/m 可針對您的特殊效能需求而發揮很大的彈性。 您會有更多時間進行開發，不必一開始就擔心效能需求。 我們建議從最小的 RU/s 佈建開始，並啟用 RU/m。

**無法預測、尖峰、每分鐘細微度的需求︰**適合 – 節省︰25-75%。 我們看到 RU/m 提供明顯的改善，且大部分的實際執行案例都屬於這一類。 如果您的 IoT 工作負載在一分鐘內出現幾次尖峰，且當您在系統同時進行大量插入時執行查詢，則需要更多容量來處理尖峰需求。 建議您套用以下的逐步方法，將您的資源需求最佳化。

 ![此圖表顯示 5 分鐘細微度的要求耗用量](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-consumption.png)
 
 *圖 - RU 耗用量基準測試*

**安心使用︰**適合 – 節省︰10-20%。 有時候，您只希望安心使用，而不擔心潛在的尖峰和節流。 此功能非常適合您。 在此情況下，我們建議啟用 RU/m，並稍微降低每秒佈建。 此案例中不同於上述情況，您將不會嘗試積極將佈建最佳化。 這比較傾向於「零節流」的心態。

具有特殊需求的重要作業︰有時候，我們建議只讓重要的作業取用 RU/m 預算，而不要讓特殊或不重要的作業消耗預算。 這可以在下一節輕鬆定義。

## <a name="using-the-portal-metrics-to-optimize-cost-and-performance"></a>使用入口網站計量將成本和效能最佳化

**接下來的幾週，我們將進一步開發內容來監視 RU 分鐘耗用量，以最佳化您的輸送量需求。**

透過入口網站計量，您可以比較取用的一般 RU 秒數和 RU 分鐘數。 監視這些計量應該可以協助您將佈建最佳化。 

我們建議採取逐步方法來決定適合您的 RU/m。 針對每個步驟，您應該可以看出代表工作負載完整週期 (可能是小時、天，甚至是週) 的整體 RU 耗用量，並深入了解您佈建之項目的使用量。

這個方法背後的原則是讓您的輸送量佈建，盡可能接近符合下列效能準則的佈建點。 

![此圖表顯示 5 分鐘細微度的要求耗用量](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-adjust-provisioning.png)
 
若要了解工作負載的最佳佈建點，您需要了解︰

* 取用模式︰沒有、不頻繁或持續性的尖峰？ 小 (平均 2 倍)、中、大 (平均大於 10 倍) 尖峰？
* 節流要求的百分比︰您是否覺得稍微節流也沒有關係？ 如果是的話，多少才適合？ 

只要確定您的目標，就更能夠接近最佳佈建。

為了協助您，我們提供整體指引，讓您根據 RU/m 耗用量將佈建最佳化。 本指引並不適用於所有種類的工作負載，而是以私人預覽知識為主。 我們可能隨著更多經驗而變更這些基準︰

|RU/m % 使用率|RU/m 的使用程度|建議的佈建動作|
|---|---|---|
|0-1%|使用率不足|降低 RU/s 以取用更多 RU/m|
|1-10%|使用情況良好|保持相同的佈建等級|
|10% 以上|過度使用|增加 RU/s 以減少依賴 RU/m|

## <a name="select-which-operations-can-consume-the-rum-budget"></a>選取哪些作業可以取用 RU/m 預算

在要求等級上，您也可以啟用/停用 RU/m 預算來支應要求，而不分作業類型。 如果取用一般佈建的 RU/秒預算，但要求無法取用 RU/m 預算，將會節流處理此要求。 根據預設，如果啟動 RU/m 輸送量預算，則由 RU/m 預算支應任何要求。 

以下的程式碼片段使用 DocumentDB API，停用 CRUD 和查詢作業的 RU/m 預算。

```csharp
// In order to disable any CRUD request for RU/m, set DisableRUPerMinuteUsage to true in RequestOptions
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    new Document { Id = "Cosmos DB" },
    new RequestOptions { DisableRUPerMinuteUsage = true });
// In order to disable any query request for RU/m, set DisableRUPerMinuteOnRequest to true in RequestOptions
FeedOptions feedOptions = new FeedOptions();
feedOptions.DisableRUPerMinuteUsage = true;
var query = client.CreateDocumentQuery<Book>(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    "select * from c",feedOptions).AsDocumentQuery();
```

## <a name="next-steps"></a>後續步驟

在本文中，我們已描述了 Azure Cosmos DB 中的資料分割運作方式、如何建立分割集合，以及如何為您的應用程式挑選適當的資料分割索引鍵。

* 使用 Azure Cosmos DB 執行規模和效能測試。 如需範例，請參閱[使用 Azure Cosmos DB 執行效能和規模測試](performance-testing.md)。
* 使用 [SDK](documentdb-sdk-dotnet.md) 或 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 開始撰寫程式碼。
* 了解 Azure Cosmos DB 中[佈建的輸送量](request-units.md) 


