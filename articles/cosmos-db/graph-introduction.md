---
title: "Azure Cosmos DB 圖形 API 簡介 | Microsoft Docs"
description: "了解如何使用 Azure Cosmos DB 並透過 Gremlin 查詢語言儲存、查詢和周遊巨大圖形 (低延遲)。"
services: cosmosdb
author: arramac
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 963822f0927afea90c8a03b60b7ee93fe36b4070
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Azure Cosmos DB：圖形 API 簡介

[Azure Cosmos DB](introduction.md) 是 Microsoft 全域散發的多模型資料庫服務，適用於任務關鍵性應用程式。 Azure Cosmos DB 提供[現成的全域散發](../documentdb/documentdb-distribute-data-globally.md)、全球[彈性調整的輸送量和儲存體](partition-data.md)、達到第 99 個百分位數的個位數毫秒延遲、[五個定義完善的一致性等級](../documentdb/documentdb-consistency-levels.md)，以及保證的高可用性，全部都由[領先業界的 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) 所支持。 Azure Cosmos DB 會[自動編製資料的索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，您不需要處理結構描述和管理索引。 它是多重模型，支援文件、索引鍵/值、圖表和單欄式資料模型。 

![Gremlin、圖表和 Azure Cosmos DB](./media/graph-introduction/graph-gremlin.png) 

Azure Cosmos DB 提供圖形模型化和周遊 API，還有現成的全域散發、彈性調整的儲存體和輸送量、小於 10 毫秒的讀取延遲和達到 p99 的 <15 毫秒、自動編製索引和查詢、可調整的一致性等級，以及涵括 99.99% 可用性的完整 SLA。 您可以使用 [Gremlin 標準](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)查詢 Azure Cosmos DB，它與其他 Gremlin 相容圖形系統整合。

在本文中，我們概略說明 Azure Cosmos DB 圖形 API，以及如何使用它來儲存有數十億個頂點和邊緣的巨大圖形、在毫秒延遲等級內查詢它們，以及輕鬆發展圖形結構和結構描述。 

## <a name="graph-databases"></a>圖表資料庫
實務上的資料會自然連線。 傳統的資料模型化著重於實體。 但對於許多應用程式，還需要為實體之間的豐富關係性建立模型。 圖表可讓您自然地為實體和關聯性建立模型。 

[圖表](http://mathworld.wolfram.com/Graph.html)是由[頂點](http://mathworld.wolfram.com/GraphVertex.html)和[邊緣](http://mathworld.wolfram.com/GraphEdge.html)組成的結構。 頂點和邊緣的屬性數量不限。 頂點代表特定的物件，例如人員、地點或事件。 邊緣代表頂點之間的關聯性。 比方說，一個人可能認識其他人、已涉入某個事件，及/或最近到過特定地點。 屬性表達頂點和邊緣的相關資訊。 屬性的例子包括具有名稱、年齡的頂端，以及具有時間戳記和/或加權的邊緣。 更正式的說，這種模型稱為[屬性圖表](https://github.com/tinkerpop/blueprints/wiki/Property-Graph-Model)。 Azure Cosmos DB 支援屬性圖表模型。 

例如，下圖中的範例圖表顯示人員、行動裝置、興趣和作業系統之間的關聯性。 

![顯示人員、裝置和興趣的範例資料庫](./media/graph-introduction/sample-graph.png) 

圖表適合用來了解科學、技術和商務領域中的各種資料集。 圖表資料庫可讓您自然又有效率地模型化和儲存圖表，很適合在許多案例中使用。 圖表資料庫通常是 NoSQL 資料庫，因為這些使用案例通常也需要結構描述彈性及快速的反覆運算。 

圖表提供一種新穎又強大的資料模型化技術。 但這一點本身尚不足以構成使用圖表資料庫的理由。 在許多涉及圖表周遊的使用案例和模式中，圖表遠遠勝過傳統的 SQL 和 NoSQL 資料庫。 在周遊多個關聯性時，例如好友的好友，此效能差異會更明顯。 

您可以結合圖表資料庫所提供的快速周遊和圖表演算法，例如深度優先搜尋、廣度優先搜尋、Dijkstra 的演算法等等，解決各種領域的問題，例如社交網路、地理空間、內容管理、地理空間和推薦。 

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>行星級圖表與 Azure Cosmos DB
Azure Cosmos DB 是一種完全受管理的圖表資料庫，提供全域散發、彈性調整的儲存體和輸送量、自動編製索引和查詢、可調整的一致性等級，而且支援 TinkerPop 標準。  

![Azure Cosmos DB 圖表架構](./media/graph-introduction/cosmosdb-graph-architecture.png) 

Azure Cosmos DB 提供以下有別於市場上其他圖表資料庫的功能： 

* **可彈性調整的輸送量和儲存體**︰實務上的圖表需要調整超過單一伺服器的產能。 Azure Cosmos DB 可讓您順暢地在多部伺服器之間調整圖表。 您也可以單獨根據存取模式來自行調整圖表的輸送量。 Azure Cosmos DB 支援的圖表資料庫可調整為幾乎不受限制的儲存體大小和佈建輸送量。 

* **多重區域複寫**︰Azure Cosmos DB 會自動將您的圖表資料複寫至您的帳戶相關聯的所有區域，讓您開發需要全域存取資料的應用程式，也能在一致性、可用性與效能之間做出取捨，而且全都有相對應的保證。 Azure Cosmos DB 利用多重定址 API 提供自動的區域性容錯移轉，還能夠彈性調整世界各地的輸送量和儲存體。

* **使用熟悉的 Gremlin 語法快速查詢和周遊**︰儲存異質性的頂點和邊緣，並透過熟悉的 Gremlin 語法查詢這些文件 (也即將提供增強式 SQL)。 Azure Cosmos DB 採用高度並行、無鎖定、記錄結構化索引技術，自動編製所有內容的索引。 這樣一來，不需要指定結構描述提示、次要索引或檢視，就能進行大量且即時的查詢和周遊。 深入了解[使用 Gremlin 查詢圖形](gremlin-support.md)。

* **受到完整管理**：不需要管理資料庫和電腦資源。 因為是受到完整管理的 Microsoft Azure 服務，您不需要管理虛擬機器、部署和設定軟體、管理調整事宜，或處理複雜的資料層升級。 每個圖表都會自動備份，以防區域性失敗。 您可以輕鬆地新增 Azure Cosmos DB 帳戶，並在需要時佈建容量，將精力投注在應用程式，不用浪費時間來操作和管理資料庫。

* **自動編製索引**：根據預設，Azure Cosmos DB 會自動為圖表中節點和邊緣內的屬性編制索引，而不期待或需要任何結構描述或建立次要索引。 

* **Gremlin 相容性**：Azure Cosmos DB 天生就支援開放原始碼 Gremlin 標準，可以與其他支援 Gremlin 的圖表系統整合。 因此，您可以輕鬆地從另一個不同的圖表資料庫移轉，例如 Titan 或 Neo4j，或搭配圖表分析架構一起使用 Azure Cosmos DB，例如 Apache Spark GraphX。

* **可調整的一致性等級：**有五個定義完善的一致性等級可選擇，讓您在一致性與效能之間做出最好的取捨。 針對查詢和讀取作業，Azure Cosmos DB 提供五個不同的一致性等級：強式、限定過期、工作階段、一致的前置和最終。 這些細微且定義完善的一致性等級，可讓您在一致性、可用性與延遲三者間做出合理取捨。 深入了解 [使用一致性層級以最大化 DocumentDB 中的可用性和效能](../documentdb/documentdb-consistency-levels.md)。

Azure Cosmos DB 也可讓您在相同的容器/資料庫內使用多個模型，例如文件和圖表。 您可以使用文件集合來同時儲存圖表資料與文件，並使用透過 JSON 的 SQL 查詢和 Gremlin 查詢，查詢與圖表相同的資料。 

## <a name="getting-started"></a>開始使用
您可以透過 Azure CLI、Azure Powershell 或 Azure 入口網站，搭配圖形 API 的支援，建立 Azure Cosmos DB 帳戶。 建立之後，Azure 入口網站會提供像 `https://<youraccount>.graphs.azure.com` 一樣的服務端點，此端點提供 Gremlin 的 WebSocket 前端。 您可以設定 Gremlin 相容性工具 (例如 [Gremin 主控台](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console)) 連線至此端點，然後在 Java、Node.js 或任何 Gremlin 用戶端驅動程式中建置應用程式。

下表顯示您可以開始運用在 Azure Cosmos DB 上的常用 Gremlin 驅動程式︰

| 下載 | 文件 |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Github 上的 Gremlin-JavaScript](https://github.com/jbmusso/gremlin-javascript) |
| [Gremlin 主控台](https://tinkerpop.apache.org/downloads.html) |[TinkerPop 文件](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB 也透過 NuGet 提供第一方 .NET 程式庫，其中包含以 [Azure Cosmos DB SDK](../documentdb/documentdb-sdk-dotnet.md) 為基礎的 Gremlin 擴充方法。 此程式庫提供「內含式」Gremlin 伺服器，可用來直接連線至 DocumenDB 資料分割。 

| 下載 | 文件 |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

[Azure Cosmos DB 模擬器](../documentdb/documentdb-nosql-local-emulator.md)可讓您使用圖形 API 在本機開發和測試，不需建立 Azure 訂用帳戶，也不會產生任何費用。 如果您滿意應用程式在模擬器中的運作方式，就可以切換成使用雲端的 Azure Cosmos DB 帳戶。

## <a name="scenarios-for-azure-cosmos-dbs-graph-support"></a>Azure Cosmos DB 的 Graph 支援案例
以下是某些可以使用 Azure Cosmos DB 圖表支援的案例︰ 

* **社交網路**︰藉由結合客戶相關資料和他們與其他人的互動，您可以開發個人化體驗、預測客戶行為，或將興趣雷同的人們聯繫在一起。 Azure Cosmos DB 可用來管理社交網路並追蹤客戶的喜好設定和資料。 

* **推薦引擎**︰零售業中常用。 藉由結合產品、使用者和使用者互動的相關資訊，例如購物、瀏覽或商品評價，您可以建立自訂的推薦。 Azure Cosmos DB 憑藉其低延遲、彈性調整及原生的圖表支援，很適合將這些互動建立模型。

* **地理空間**︰電信、物流和旅遊規劃方面的許多應用程式需要在特定區域內尋找有興趣的地點，或在兩個地點之間找出最短/最佳路線。 Azure Cosmos DB 很自然地可以解決這些問題。 

* **物聯網**︰以圖形模擬 IoT 裝置之間的網路和連線，可讓您更了解裝置和資產的狀態，以及網路的某個部分變動時可能對其他部分造成什麼影響。 

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure Cosmos DB 中的圖表支援，請參閱︰

* 開始使用 [Azure Cosmos DB 圖表教學課程](create-graph-dotnet.md)
* 了解如何[使用 Gremlin 在 Azure Cosmos DB 中查詢圖表](gremlin-support.md)

