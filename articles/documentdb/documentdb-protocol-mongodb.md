---
title: "Azure Cosmos DB 簡介：適用於 MongoDB 的 API | Microsoft Docs"
description: "了解如何透過常用的 OSS MongoDB API，使用 Azure Cosmos DB 來儲存及查詢大量 JSON 文件 (低延遲)。"
keywords: "MongoDB 是什麼"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7dc474f9f940132dd363740e8288a70ee2f2b970
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB 簡介：適用於 MongoDB 的 API

[Azure Cosmos DB](../cosmos-db/introduction.md) 是 Microsoft 全域散發的多模型資料庫服務，適用於任務關鍵性應用程式。 Azure Cosmos DB 提供[一站式全域散發](../documentdb/documentdb-distribute-data-globally.md)、全球[彈性調整的輸送量和儲存體](../documentdb/documentdb-partition-data.md)、達到第 99 個百分位數的個位數毫秒延遲、[五個定義完善的一致性層級](../documentdb/documentdb-consistency-levels.md)，以及保證的高可用性，全部都由[領先業界的 SLA (英文)](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) 所支援。 Azure Cosmos DB 會[自動編製資料的索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，您不需要處理結構描述和索引管理。 它是多重模型，可支援文件、索引鍵/值、圖表和單欄式資料模型。 

![適用於 MongoDB 的 Azure API](./media/documentdb-protocol-mongodb/cosmosdb-mongodb.png) 

DocumentDB 資料庫可做為針對 [MongoDB (英文)](https://docs.mongodb.com/manual/introduction/) 所撰寫之應用程式的資料存放區。 這表示，使用現有的[驅動程式 (英文)](https://docs.mongodb.org/ecosystem/drivers/)，針對 MongoDB 所撰寫的應用程式現在可與 Cosmos DB 通訊，並使用 DocumentDB 資料庫而非 MongoDB 資料庫。 在許多情況下，您只要變更連接字串，就可以從使用 MongoDB 切換到 DocumentDB。 您可以使用這項功能，輕鬆地在 Azure 雲端建置及執行 MongoDB 資料庫應用程式，利用 Azure Cosmos DB 的全域散發和[領先業界的完整 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)，同時繼續使用 MongoDB 的熟悉技能和工具。


## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>於 MongoDB 應用程式使用 Azure Cosmos DB 有何好處？

* **可彈性調整的輸送量和儲存體：**輕鬆相應增加或相應減少 MongoDB 資料庫，以符合您應用程式的需求。 您的資料會儲存在固態硬碟 (SSD) 中以便獲得可預測的低延遲。 Cosmos DB 支援的 MongoDB 集合可調整為幾乎不受限制的儲存體大小和佈建輸送量。 隨著應用程式的成長，您可以依據可預測的效能彈性且順暢地調整 Cosmos DB。 

* **多重區域複寫**︰Cosmos DB 會自動將您的資料複寫至與您 MongoDB 帳戶相關聯的所有區域，讓您開發需要全域存取資料的應用程式，也能在一致性、可用性與效能之間做出取捨，而且全都有相對應的保證。 Cosmos DB 利用多路連接 API 提供自動的區域性容錯移轉，還能夠彈性調整世界各地的輸送量和儲存體。 請參閱[將資料分散到全球](documentdb-distribute-data-globally.md)以深入了解。

**MongoDB 相容性**︰您可以使用現有的 MongoDB 專業知識、應用程式程式碼和工具。 您可以開發使用 MongoDB 的應用程式，並使用完全受管理的全域分散式 Cosmos DB 服務來部署這些應用程式。

**不需要任何伺服器管理工作**︰您不需要管理和調整 MongoDB 資料庫。 Cosmos DB 是完全受管理的服務，這表示您不需要自己管理任何基礎結構或虛擬機器。 Cosmos DB 可在 30 個以上的 [Azure 區域](https://azure.microsoft.com/regions/services/)中使用。

* **可調整的一致性層級：**提供五個定義完善的一致性層級可選擇，讓您能在一致性與效能之間做出最好的取捨。 針對查詢和讀取作業，Cosmos DB 提供五個不同的一致性層級：強式、限定過期、工作階段、一致的前置和最終。 這些細微且定義完善的一致性層級可讓您在一致性、可用性與延遲三者間做出合理取捨。 深入了解[使用一致性層級將可用性和效能最大化](documentdb-consistency-levels.md)。

* **自動編製索引**：根據預設，Cosmos DB 會自動為 MongoDB 資料庫中文件內的所有屬性編製索引，且不預期或需要任何結構描述或建立次要索引。

**企業級** - Azure Cosmos DB 支援多個本機複本，在面對本機和區域故障時可提供 99.99% 的可用性和資料保護。 Azure Cosmos DB 有企業級的[合規性認證 (英文)](https://www.microsoft.com/trustcenter) 和安全性功能。 

在這段 Azure Friday 影片中，和 Scott Hanselman 與 Azure Cosmos DB 工程總經理 Kirill Gavrylyuk 一起深入了解。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 

## <a name="how-to-get-started"></a>如何開始使用

在 [Azure 入口網站](https://portal.azure.com)中建立 Azure Cosmos DB 帳戶，然後將 MongoDB 連接字串換成您的新帳戶。 

*這樣就大功告成了！*

如需詳細指示，請遵循[建立帳戶](documentdb-create-mongodb-account.md)和[連接至您的帳戶](documentdb-connect-mongodb-account.md)。

## <a name="next-steps"></a>後續步驟

Azure Cosmos DB 的 MongoDB API 相關資訊已整合至整體 Azure Cosmos DB 文件，但以下有幾個線索可讓您開始使用︰

* 依照下列[連線到 MongoDB 帳戶](documentdb-connect-mongodb-account.md)教學課程，了解如何取得您的帳戶連接字串資訊。
* 依照[使用 MongoChef 搭配 Azure Cosmos DB](documentdb-mongodb-mongochef.md) 教學課程來了解如何在 MongoChef 中建立 Azure Cosmos DB 資料庫和 MongoDB 應用程式之間的連線。
* 依照[將資料移轉至具有 MongoDB 通訊協定支援的 Azure Cosmos DB](documentdb-mongodb-migrate.md) 教學課程，將資料匯入適用於 MongoDB 的 API資料庫。
* 使用 [Node.js](documentdb-mongodb-samples.md) 建置第一個 API for MongoDB 應用程式。
* 使用 [NET](documentdb-mongodb-application.md) 建置第一個 API for MongoDB Web 應用程式。
* 使用 [Robomongo](documentdb-mongodb-robomongo.md) 連線至 API for MongoDB 帳戶。
* 使用 [GetLastRequestStatistics 命令和 Azure 入口網站計量](documentdb-request-units.md#GetLastRequestStatistics)，了解您的作業使用多少 RU。
* 了解如何[設定全球分散式應用程式的讀取喜好設定](../cosmos-db/tutorial-global-distribution-mongodb.md)。

