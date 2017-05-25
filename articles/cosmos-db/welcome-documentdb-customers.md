---
title: "DocumentDB 客戶，歡迎使用 Azure Cosmos DB | Microsoft Docs"
description: "進一步了解在 //build 2017 宣布的事項：DocumentDB 客戶現在是 Azure Cosmos DB 客戶。"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: n/a
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f60c3502ed51424647088fa8109f13cabc4e73da
ms.contentlocale: zh-tw
ms.lasthandoff: 05/17/2017

---

# <a name="welcome-documentdb-customers-to-azure-cosmos-db"></a>歡迎 DocumentDB 客戶使用 Azure Cosmos DB

DocumentDB 客戶，我們很興奮地讓您知道您現在已成為 Azure Cosmos DB 家族的一員！ 

Azure Cosmos DB ([在 Microsoft Build 2017 大會宣布](https://azure.microsoft.com/blog/azure-cosmos-db-microsofts-globally-distributed-multi-model-database-service/)) 是第一個全球發佈的資料庫服務，可讓您跨越任意多個地理區域彈性地調整輸送量和儲存體，同時保證低延遲、高可用性和一致性 (由業界最完整的 SLA 提供支援)，並支援許多資料模型和 API。 

![Azure Cosmos DB 是 Microsoft 的全球發佈資料庫服務，包含彈性的相應放大、低延遲保證、五個一致性模型，以及完整保證的 SLA](./media/welcome-documentdb-customers/azure-cosmos-db.png)

Azure Cosmos DB 支援的其中一個 API 是 DocumentDB API 和文件資料模型。 您已經熟悉 DocumentDB API。 也就是您用於執行目前 DocumentDB 應用程式的 API。 這些 API「並未」變更 - NuGet 套件、命名空間及所有相依性均維持不變。 **您不需要變更任何項目**，即可繼續執行使用 DocumentDB API 建置的應用程式。 Azure Cosmos DB 只是它們現在所屬的服務名稱。 

讓我們逐步討論您可能遇到的一些問題。 

## <a name="why-move-to-azure-cosmos-db"></a>為何要移至 Azure Cosmos DB？ 

Azure Cosmos DB 是全球大規模發佈之雲端資料庫的下一個大躍進。 身為 DocumentDB 客戶的您現在可以存取具突破性的新系統和 Azure Cosmos DB 所提供的功能。

Azure Cosmos DB 在 2010 年以 “Project Florence” 開始開發，以解決 Microsoft 內部大規模應用程式所面對的開發人員難題。 請注意，建置全球發佈的應用程式不是 Microsoft 獨有的問題，於是我們在 2015 年以 Azure DocumentDB 的形式將第一代的這項技術提供給 Azure 開發人員使用。 

從那時候起，我們便已新增一些功能並引進重要的全新功能。  Azure Cosmos DB 於焉產生。  在這一版的 Azure Cosmos DB 中，DocumentDB 客戶 (與其資料) 會自動無縫轉換為 Azure Cosmos DB 客戶。 此轉換完美無縫，而且您現在可以存取具突破性的新系統和 Azure Cosmos DB 所提供的功能。 這些功能位於核心資料庫引擎，以及全球發佈、彈性延展性和領先業界之完整 SLA 的領域。 具體來說，我們已逐步提升 Azure Cosmos DB 資料庫引擎，使其能夠有效地將所有熱門資料模型、類型系統和 API 對應至 Azure Cosmos DB 的基礎資料模型。 

這項工作的目前開發人員表徵就是新支援 [Gremlin](graph-introduction.md) 和[表格儲存體 API](table-introduction.md)。 這只是開端... 我們將逐漸新增其他熱門 API 和較新的資料模型，更進一步提升全球規模的效能和儲存體。 

請務必指出 DocumentDB 的 [SQL 方言](../documentdb/documentdb-sql-query.md)始終都是基礎 Cosmos DB 能夠支援的許多 API 之一。 身為使用完全受管理服務 (例如 Azure Cosmos DB) 的開發人員，服務唯一的介面就是服務所公開的 API。 總之，身為現有 DocumentDB 客戶的您仍絲毫沒有改變。 Azure Cosmos DB 會提供與 DocumentDB 完全相同的 SQL API。 不過，您現在 (及未來) 可以存取先前無法存取的其他功能。 

我們不斷努力的另一個表徵就是擴充輸送量和儲存體的全球和彈性延展性基礎。 其最初的表徵之一就是 [RU/m](request-units-per-minute.md)，但我們還會在這些領域中宣佈更多功能。 這些新功能可協助客戶降低各種工作負載的成本。 我們對全球發佈子系統提供了幾項基礎增強功能。 這項工作的許多開發人員表徵之一就是一致的前置詞一致性模型 (總計有五個定義完善的一致性模型)。 不過，我們將會在時機成熟時發行許多更有趣的功能。 

## <a name="what-do-i-need-to-do-to-ensure-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>我該怎麼做才能確保 DocumentDB 資源繼續在 Azure Cosmos DB 上執行？

不做任何動作。 您不需要進行任何變更。 DocumentDB 資源現在就是 Azure Cosmos DB 資源，而且在此移轉發生時不會中斷服務。

## <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>我需要對我的應用程式進行哪些變更才能使用 Azure Cosmos DB？

不需要進行任何變更。 類別、命名空間和 NuGet 套件名稱均未變更。 一如往常，我們建議讓您的 SDK 保持最新狀態，以便利用最新的功能和改善功能。 

## <a name="whats-changed-in-the-azure-portal"></a>Azure 入口網站有何變更？

Azure DocumentDB 不再顯示為入口網站中的 Azure 服務。 而 Azure Cosmos DB 會以新的圖示顯示，如下圖所示。 您所有的集合可如以前般使用，而且您仍可調整輸送量、變更一致性及監視 SLA。 已增強 [資料總管 (預覽)] 的功能。 您現在可以檢視和編輯文件、建立和執行查詢，以及經由一個頁面使用預存程序、觸發程序和 UDF ，如下圖所示。 

![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/welcome-documentdb-customers/cosmos-db-data-explorer.png)

## <a name="are-there-changes-to-pricing"></a>價格是否有所變動？

否，在 Azure Cosmos DB 上執行應用程式的成本跟以前一樣。 不過，您可能會受益於我們所宣布的全新**每分鐘要求單位功能**。 如需詳細資訊，請參閱[調整每分鐘的輸送量](request-units-per-minute.md)一文。

## <a name="are-there-changes-to-the-service-level-agreements-slas"></a>服務等級協定 (SLA) 是否有所變動？

否，可用性、一致性、延遲和輸送量的 SLA 均維持不變，仍顯示在入口網站中。 如需 SLA 的詳細資訊，請參閱 [Azure Cosmos DB 的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。
   
![具有範例資料的待辦事項應用程式](./media/welcome-documentdb-customers/azure-cosmosdb-portal-metrics-slas.png)

## <a name="whats-next-with-azure-cosmos-db"></a>Azure Cosmos DB 的下一步是什麼？

Azure Cosmos DB 是持續不斷進步的資料庫服務。 所有新功能都會在 Microsoft 內部的大型應用程式上進行驗證，接著對重要外部客戶公開，最後再全球發行。 

新的 Gremlin 和資料表 API 支援只是 Azure Cosmos DB 即將支援的熱門 API 和資料模型開端，我們很期待與您分享最新的資料模型。 我們也會繼續努力提升全球規模的效能和儲存體。 

一如以往，我們很感謝您成為 Azure DocumentDB 客戶，我們很興奮地與您們 (我們在世界各地的開發人員和客戶) 分享 Azure Cosmos DB。 我們的任務就是提供世界上最受信任的資料庫服務，讓您能夠更輕鬆地建置非常強大、超大規模的應用程式。 請試用 Azure Cosmos DB 中的新功能，讓我們知道您的想法！  我們很興奮地看到您使用 Cosmos DB 建置應用程式。

— 您的 Azure Cosmos DB 好友[@AzureCosmosDB](https://twitter.com/AzureCosmosDB)

