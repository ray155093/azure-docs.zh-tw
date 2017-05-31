---
title: "資料庫待用加密 - Azure Cosmos DB | Microsoft Docs"
description: "了解 Azure Cosmos DB 如何提供所有資料的預設加密。"
services: cosmosdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8248b429cf9b7fdfc709a68c4d1e88b7b660020b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Azure Cosmos DB 資料庫待用加密

「待用加密」是一個用來描述加密靜態儲存裝置 (例如固態硬碟 (SSD) 和硬碟 (HDD)) 上資料的常見用語。  Azure Cosmos DB 會將其主要資料庫儲存在 SSD 上，並將其媒體附件和備份儲存在 Azure Blob (通常以 HDD 進行備份) 之中。  在針對 Azure Cosmos DB 推出待用加密之後，您所有的資料庫、媒體附件及備份都會進行加密。  這代表您的資料現在會在傳輸過程 (通過網路) 和待用 (位於靜態儲存裝置上) 期間進行加密，提供您端對端的加密。

我們很努力地讓做為 PaaS 服務的 Azure Cosmos DB 非常容易使用。  因此，所有儲存在 Azure Cosmos DB 中的使用者資料都會在待用和傳輸時進行加密，使用者並不需要執行任何動作。  換句話說，待用加密預設便會「開啟」。  我們並沒有提供將此功能開啟或關閉的控制項，並是在持續符合我們的[可用性和效能 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) 的前提下提供此功能。

## <a name="how-does-encryption-at-rest-work"></a>待用加密如何運作？

待用加密是使用數種安全性技術來實作的，這些技術包括安全金鑰儲存體系統、加密的網路，以及密碼編譯 API。  下圖顯示加密資料儲存體和金鑰管理的分隔方式。  負責解密及處理資料的系統，必須和負責管理金鑰的系統進行通訊。

![設計圖表](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

下列為使用者要求的基本流程：
- 準備好使用者資料庫帳戶，並透過向管理服務資源提供者 (RP) 提出要求來擷取儲存體金鑰。
- 使用者透過 HTTPS/安全傳輸 (SDK 會對詳細資料進行抽象處理) 建立與 Azure Cosmos DB 的連線。
- 使用者透過先前所建立的安全連線，傳送要儲存的 JSON 文件。
- 為 JSON 文件編製索引 (除非使用者已關閉編製索引)。
- JSON 文件和索引資料皆寫入安全儲存體。
- 系統會定期自安全儲存體讀取資料，並備份到 Azure 加密 Blob 存放區。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>問︰如果已啟用 SSE，Azure 儲存體的成本會多出多少？
答：沒有任何額外成本。

### <a name="q-who-manages-the-encryption-keys"></a>問︰誰負責管理加密金鑰？
答︰金鑰是由 Microsoft 管理。

### <a name="q-how-often-are-encryption-keys-rotated"></a>問︰加密金鑰多久輪替一次？
答︰Microsoft 有 DocumentDB 遵循的一組內部指導方針。  Microsoft 雖然不會發行特定的指導方針，但是會發佈 (也稱為 SDL 的安全性開發生命週期) [https://www.microsoft.com/sdl/default.aspx]，這被視為是一組內部的指導方針，而且有適用於開發人員的最佳實用作法。

### <a name="q-can-i-use-my-own-encryption-keys"></a>問︰我是否可以使用自己的加密金鑰？
答：Azure Cosmos DB 是一種 PaaS 服務，我們也很努力地讓這個服務容易使用。  我們注意到會詢問此問題的使用者，通常是想詢問有關符合如 PCI-DSS 等合規性的 Proxy 問題。  建置這項功能時，我們已和合規性稽核人員合作，以確保使用 Azure Cosmos DB 的客戶能夠在不需要自行管理金鑰的情況下滿足他們的需求。
這就是為何我們目前有提供讓使用者自行管理金鑰的選項。


### <a name="q-what-regions-have-encryption-turned-on"></a>問：有哪些區域已開啟加密？
答：所有的 Azure Cosmos DB 區域皆已針對所有使用者資料開啟加密。

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>問︰加密會影響效能延遲和輸送量 SLA 嗎？
答︰現在所有現有和新的帳戶都啟用待用加密，但是效能 SLA 沒有任何影響或變化。  您可以在[效能 SLA 頁面](https://azure.microsoft.com/support/legal/sla/documentdb)閱讀更多資訊以查看最新的保證。

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>問︰本機模擬器支援待用加密嗎？
答︰模擬器是獨立的開發/測試工具，不使用受管理 DocumentDB 服務所使用的金鑰管理服務。 我們建議您在儲存機密模擬器測試資料的磁碟機上啟用 BitLocker。 (模擬器支援變更預設資料目錄) [https://docs.microsoft.com/azure/documentdb/documentdb-nosql-local-emulator]，以及使用已知位置。

## <a name="next-steps"></a>後續步驟

如需 Azure Cosmos DB 安全性及最新改進的概觀，請參閱 [Azure Cosmos DB 資料庫安全性](documentdb-nosql-database-security.md)。

如需 Microsoft 認證的詳細資訊，請參閱 [Azure 信任中心](https://azure.microsoft.com/en-us/support/trust-center/)。

