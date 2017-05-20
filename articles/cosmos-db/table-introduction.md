---
title: "Azure Cosmos DB 的資料表 API 簡介 | Microsoft Docs"
description: "了解如何透過受歡迎的 OSS MongoDB API，使用 Azure Cosmos DB 來儲存及查詢大量索引鍵-值資料 (低延遲)。"
services: cosmosdb
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8226ecfb5fa8c70b162ae776679302db6cfdcdbc
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB 簡介：資料表 API

[Azure Cosmos DB](introduction.md) 是 Microsoft 分散全球各地的多模型資料庫服務，適用於任務關鍵性應用程式。 Azure Cosmos DB 提供[一站式全域散發](../documentdb/documentdb-distribute-data-globally.md)、全球[彈性調整的輸送量和儲存體](partition-data.md)、達到第 99 個百分位數的個位數毫秒延遲、[五個定義完善的一致性等級](../documentdb/documentdb-consistency-levels.md)，以及保證的高可用性，全部都由[領先業界的 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) 所支持。 Azure Cosmos DB 會[自動編製資料的索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，您不需要處理結構描述和管理索引。 它是多重模型，支援文件、索引鍵/值、圖形和單欄式資料模型。 

![Azure 表格儲存體 API 和 Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos DB 針對需要彈性結構描述之索引鍵-值存放區的應用程式，提供資料表 API。 [Azure 資料表儲存體](../storage/storage-introduction.md) SDK 和 REST API 可用來與 Azure Cosmos DB 搭配運作。 您可以使用 Azure Cosmos DB 來建立具有高輸送量需求的資料表。 Azure Cosmos DB 支援輸送量最佳化資料表 (非正式的名稱為「進階資料表」)，目前是公開預覽版。 

針對具有高儲存體和較低輸送量需求的資料表，您可以繼續使用 Azure 資料表儲存體。 Azure Cosmos DB 在未來的更新中將導入對儲存體最佳化資料表的支援，而現有和新的 Azure 資料表儲存體帳戶將會升級至 Azure Cosmos DB。

## <a name="premium-and-standard-table-apis"></a>進階和標準資料表 API
如果您目前使用 Azure 表格儲存體，移至 Azure Cosmos DB 的「進階資料表」預覽版即可獲得下列優點：

|  | Azure 表格儲存體 | Azure Cosmos DB：表格儲存體 (預覽) |
| --- | --- | --- |
| 延遲 | 快速，但延遲沒有上限 | 一位數毫秒的讀取和寫入延遲，並在世界各地支援任何規模的 <10 毫秒延遲讀取和 <15 毫秒延遲寫入 (第 99 個百分位數) |
| 輸送量 | 高延展性，但不是專用的輸送量模型。 資料表每秒 20,000 個作業的延展性限制 | 高延展性且[每個資料表都有專用的保留輸送量](../documentdb/documentdb-request-units.md) (由 SLA 支援)。 帳戶沒有輸送量上限，而且支援每個資料表每秒 > 1 千萬個作業 |
| 全域散發 | 具有一個選擇性 HA 可讀取次要讀取區域的單一區域。 您無法起始容錯移轉 | 1 至 30+ 個區域的[一站式全域散發](../documentdb/documentdb-distribute-data-globally.md)，隨時隨地支援[自動和手動容錯移轉](../documentdb/documentdb-regional-failovers.md) |
| 編製索引 | PartitionKey 和 RowKey 只有主要索引。 沒有次要索引 | 對所有屬性自動執行完整的編製索引，但沒有索引管理 |
| 查詢 | 查詢執行作業會使用主索引鍵的索引，要不然會進行掃描。 | 查詢可以利用自動編製屬性的索引，加快查詢速度。 Azure Cosmos DB 的資料庫引擎能夠支援彙總、地理空間以及排序。 |
| 一致性 | 主要區域達到「強一致性」，次要地區達到「最終一致性」 | [五個定義完善的一致性層級](../documentdb/documentdb-consistency-levels.md)，可根據您應用程式的需求，進行可用性、延遲、輸送量及一致性的取捨 |
| 價格 | 儲存體最佳化  | 輸送量最佳化 |
| SLA | 99.9% 的可用性 | 單一區域內可達 99.99% 的可用性，並且能夠新增更多區域來提高可用性。 在一般可用性上達到[業界頂尖的全面性 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) |

## <a name="how-to-get-started"></a>如何開始使用

在 [Azure 入口網站](https://portal.azure.com)中建立 Azure Cosmos DB 帳戶，並開始使用[使用 .NET 的資料表 API 快速入門](create-table-dotnet.md)。 

## <a name="next-steps"></a>後續步驟

以下是可讓您快速入門的一些指標：
* 使用現有的 .NET 資料表 SDK 來開始使用 [Azure Cosmos DB 的資料表 API](create-table-dotnet.md)。
* 了解[透過 Azure Cosmos DB 全域散發](../documentdb/documentdb-distribute-data-globally.md)。
* 了解 [Azure Cosmos DB 中佈建的輸送量](../documentdb/documentdb-request-units.md)。
