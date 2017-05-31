---
title: "Azure Cosmos DB 資料庫問題 - 常見問題集 | Microsoft Docs"
description: "取得關於 Azure Cosmos DB (一種全域散發的多模型資料庫服務) 之常見問題集的解答。 解答產能、效能層級和調整的相關資料庫問題。"
keywords: "資料庫問題, 常見問題集, Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 687690fc3b99d52d7c822b7b7ff71c282541be6d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="frequently-asked-questions-about-azure-cosmos-db"></a>關於 Azure Cosmos DB 的常見問題集
## <a name="database-questions-about-microsoft-azure-cosmos-db-fundamentals"></a>關於 Microsoft Azure Cosmos DB 基本概念的資料庫問題
### <a name="what-is-microsoft-azure-cosmos-db"></a>何謂 Microsoft Azure Cosmos DB？
Microsoft Azure Cosmos DB 是一種可進行全域複寫的多模型資料庫服務，可在無結構描述的資料上進行豐富的查詢、協助提供可設定且可靠的效能，並支援快速開發，這些完全都是透過受管理的平台，背後有 Microsoft Azure 強大的功能與先進的技術做後盾。 如果關鍵需求在於可預測的輸送量、高可用性、低延遲性和無結構描述的資料模型，則 Azure Cosmos DB 就是最適合 Web、行動裝置、遊戲和 IoT 應用程式的解決方案。 Azure Cosmos DB 提供結構描述的彈性和豐富的編製索引能力，並利用整合式 JavaScript 來包含多文件交易式支援。  

Azure Cosmos DB 於 2010 年末開始開發，可解決 Microsoft 內部大規模應用程式所面對的開發人員難題。 因為建置全域散發的應用程式不只是 Microsoft 特有的問題，所以我們透過 Azure DocumentDB 的形式將該服務提供給外部所有 Azure 開發人員使用。 Azure Cosmos DB 是 DocumentDB 演進中重要的下一步，而我們現在將它提供給您使用。 在這一版的 Azure Cosmos DB 中，DocumentDB 客戶 (與其資料) 會自動無縫轉換為 Azure Cosmos DB 客戶。 此外，他們現在可以存取由 Azure Cosmos DB 所提供的各種新功能。 

如需更多資料庫問題、解答，以及部署和使用此服務的指示，請參閱 [Azure Cosmos DB 文件頁面 (英文)](https://azure.microsoft.com/documentation/services/documentdb/)。

### <a name="what-happened-to-documentdb"></a>DocumentDB 有何改變？
DocumentDB API 是適用於 Azure Cosmos DB 的其中一個支援 API 和資料模型。 此外，Azure Cosmos DB 支援使用圖形 API (預覽)、資料表 API (預覽) 和 MongoDB API。 

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>如何在 Azure 入口網站中取得我的 DocumentDB 帳戶？
只需按一下 Azure 入口網站左側功能表中的 [Azure Cosmos DB] 圖示。 如果您之前已有 DocumentDB 帳戶，現在就能擁有 Azure Cosmos DB 帳戶，且不會向您收取任何費用。

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB 有哪些一般使用案例？
對於新的 Web、行動裝置、遊戲和 IoT 應用程式，若其中自動調整規模、可預測的效能、毫秒回應時間的快速排序，以及查詢無結構描述之資料的能力都很重要，則 Azure Cosmos DB 是個不錯的選擇。 Azure Cosmos DB 適合用來進行快速開發，且支援應用程式資料模型的持續反覆運算。  管理使用者產生之內容和資料的應用程式，就是 [Azure Cosmos DB 的常見使用案例](documentdb-use-cases.md)。  

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB 如何提供可預測的效能？
[要求單位](documentdb-request-units.md)是 Azure Cosmos DB 的輸送量計算單位。 1 RU 會對應至 1KB 文件的 GET 的輸送量。 根據完成作業所需的輸送量，Azure Cosmos DB 中的每個作業 (包括讀取、寫入、SQL 查詢和預存程序執行) 具有決定性的 RU 值。 您可以就單一 RU 計量來思考，而不是思考 CPU、IO 和記憶體以及它們分別如何影響您的應用程式輸送量。

每個 Azure Cosmos DB 容器可以按照每秒輸送量 RU 的佈建輸送量加以保留。 對於任何規模的應用程式，您可以將個別要求設為基準以測量其 RU 值，以及佈建容器來處理所有要求的要求單位總和。 您也可以隨著應用程式發展需求，相應增加或減少容器的輸送量。 如需有關要求單位的詳細資訊，以及幫助您判斷容器需求的協助，請參閱[估計輸送量需求](documentdb-request-units.md#estimating-throughput-needs)並嘗試使用[輸送量計算機 (英文)](https://www.documentdb.com/capacityplanner)。 此處的容器會參考 DocumentDB API 的集合、圖形 API 的圖形、MongoDB API 集合及資料表 API 的資料表。  


### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB 符合 HIPAA 規範嗎？
是，Azure Cosmos DB 符合 HIPAA 規範。 HIPAA 建立了使用、洩漏與保護個別辨識健全狀況資訊的需求。 如需詳細資訊，請參閱 [Microsoft 信任中心](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的儲存體限制有哪些？
對於容器可以儲存在 Azure Cosmos DB 中的資料總量，沒有任何限制。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的輸送量限制有哪些？
在 Azure Cosmos DB 中容器可支援的輸送量總量沒有限制，但此處的關鍵在於您的工作負載需要大致平均分配給夠大的分割區索引鍵數目。

### <a name="how-much-does-microsoft-azure-cosmos-db-cost"></a>Microsoft Azure Cosmos DB 的費用是多少？
請參閱 [Azure Cosmos DB 定價詳細資料](https://azure.microsoft.com/pricing/details/documentdb/)頁面，以取得詳細資料。 Azure Cosmos DB 使用量費用取決於已佈建的容器數目、容器在線上的時數，以及每個容器的佈建輸送量。 此處的容器會參考 DocumentDB API 的集合、圖形 API 的圖形、MongoDB API 集合及資料表 API 的資料表。 

### <a name="is-there-a-free-account-available"></a>有免費的帳戶嗎？
如果您不熟悉 Azure，可以註冊 [Azure 免費帳戶](https://azure.microsoft.com/free/)，就能得到 30 天免費試用以及美金 200 元，讓您試用所有 Azure 服務。 或者，如果您有 Visual Studio 訂用帳戶，就可以享有[免費的 Azure 信用額度每月美金 150 元](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用於所有 Azure 服務。  

您也可以免費使用 [Azure Cosmos DB 模擬器](documentdb-nosql-local-emulator.md)在本機開發及測試應用程式，而不需建立 Azure 訂用帳戶。 如果您滿意應用程式在 Azure Cosmos DB 模擬器中的運作方式，就可以切換成使用雲端的 Azure Cosmos DB 帳戶。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>如何取得 Azure Cosmos DB 的其他協助？
如果您需要任何協助，請透過 [Stack Overflow (英文)](http://stackoverflow.com/questions/tagged/azure-documentdb) 或 [MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)聯繫我們，或是傳送郵件給 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) 來安排與 Azure Cosmos DB 工程小組的一對一談話。 

## <a name="set-up-microsoft-azure-cosmos-db"></a>設定 Microsoft Azure Cosmos DB
### <a name="how-do-i-sign-up-for-microsoft-azure-cosmos-db"></a>如何註冊 Microsoft Azure Cosmos DB？
Azure 入口網站中已提供 Microsoft Azure Cosmos DB。 首先，您必須註冊 Microsoft Azure 訂用帳戶。 當您註冊 Microsoft Azure 訂用帳戶之後，就可以將 DocumentDB API、圖形 API (預覽)、資料表 API (預覽) 或 MongoDB API 帳戶加入您的 Azure 訂用帳戶。

### <a name="what-is-a-master-key"></a>什麼是主要金鑰？
主要金鑰是可存取帳戶的所有資源的安全性權杖。 擁有此金鑰的人，對資料庫帳戶中的所有資源具有讀取和寫入存取權。 分配主要金鑰時，務必謹慎。 [Azure 入口網站][azure-portal]的 [金鑰]**** 刀鋒視窗提供主要金鑰和次要金鑰。 如需金鑰的詳細資訊，請參閱 [檢視、複製和重新產生存取金鑰](documentdb-manage-account.md#keys)。

### <a name="what-are-the-regions-to-which-preferredlocations-be-set-to"></a>設定 PreferredLocations 的目標區域有哪些？ 
目前適用於 PrefferredLocations 的有效區域如下：美國西部、美國中西部、美國西部 2、美國東部、美國東部 2、美國中部、美國中南部、美國中北部、西歐、北歐、東亞、東南亞、日本西部、日本東部、澳大利亞東南部、澳大利亞東部、印度中部、印度南部、印度西部、加拿大東部、加拿大中部、德國中部、德國東北部、中國北部、中國東部、韓國北部、韓國中部、英國西部、英國南部、巴西北部、USGov 亞利桑那、USGov 德州。

### <a name="is-there-something-i-should-be-aware-of-when-distributing-data-across-the-world-via-azures-data-centers"></a>當我透過 Azure 的資料中心在世界各地散發資料時應注意哪些事項？ 
Cosmos DB 可存在於所有區域中。 由於它是核心服務，因此，每個新的資料中心都將存有一個 Cosmos DB。 這份清單是截至目前為止的記錄。 設定這些項目時，您必須記住 Cosmos DB 會涉及主權和政府雲端。 這意味著，如果您在該處建立帳戶且想要複寫出去，是不被允許的，而您同樣無法連接到那些位置，以從外部帳戶啟用該處的複寫。 



### <a name="do-you-plan-to-provide-more-price-options-in-the-future"></a>您是否計劃在未來提供更多的價格選項？
是，Cosmos DB 目前提供輸送量最佳化的模型。 我們計劃在不久的將來，提供更好的定價模型。 

 
# <a name="documentdb-api"></a>DocumentDB API 
## <a name="database-questions-about-developing-against-documentdb-api"></a>關於針對 DocumentDB API 進行開發的資料庫問題

### <a name="how-to-do-i-start-developing-against-documentdb-api"></a>如何開始針對 DocumentDB API 進行開發？
[Azure 入口網站][azure-portal]中已提供 Microsoft DocumentDB API。  首先，您必須註冊 Microsoft Azure 訂用帳戶。  註冊 Microsoft Azure 訂用帳戶之後，您可以將 DocumentDB API 容器加入您的 Azure 訂用帳戶。 如需加入 Azure Cosmos DB 帳戶的相關指示，請參閱[建立 Azure Cosmos DB 資料庫帳戶](documentdb-create-account.md)。 如果您過去已有 DocumentDB 帳戶，現在就會擁有 Azure Cosmos DB 帳戶。  

[SDK](documentdb-sdk-dotnet.md) 適用於 .NET、Python、Node.js、JavaScript 和 Java。  開發人員也可以使用 [RESTful HTTP API](https://msdn.microsoft.com/library/azure/dn781481.aspx)，經由各種平台和語言來與 Azure Cosmos DB 資源互動。

### <a name="can-i-access-some-ready-made-samples-for-getting-headstart"></a>我可以存取一些現有的範例作為有利的開端嗎？
GitHub 上提供 DocumentDB API [.NET](documentdb-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](documentdb-nodejs-samples.md) 和 [Python](documentdb-python-samples.md) SDK 的範例。


### <a name="does-documentdb-api--database-support-schema-free-data"></a>DocumentDB API 資料庫支援無結構描述的資料嗎？
是，DocumentDB API 可讓應用程式儲存任意的 JSON 文件，而不需要結構描述定義或提示。 透過 Azure Cosmos DB SQL 查詢介面，就可立即查詢資料。   

### <a name="does-documentdb-api-support-acid-transactions"></a>DocumentDB API 支援 ACID 交易嗎？
是，DocumentDB API 支援以 JavaScript 預存程序和觸發程序表達的跨文件交易。 交易以每個集合的單一分割為範圍，且以 ACID 語意執行，也就是全有或全無，與其他並行執行的程式碼和使用者要求隔離。  如果伺服器端執行 JavaScript 應用程式碼期間擲回例外狀況，則會回復整個交易。 如需交易的詳細資訊，請參閱 [資料庫程式交易](documentdb-programming.md#database-program-transactions)。

### <a name="what-is-a-collection"></a>什麼是集合？
集合是一組文件及其相關聯的 JavaScript 應用程式邏輯。 集合是計費實體，其[成本](documentdb-performance-levels.md)是由使用的輸送量和儲存體所決定。 集合可以跨越一或多個資料分割/伺服器，也可以進行調整以處理幾乎無限量的儲存體或輸送量。

集合也是 Azure Cosmos DB 的帳務實體。 每個集合根據佈建的輸送量和使用的儲存體空間，以小時為單位計費。 如需詳細資訊，請參閱 [DocumentDB API 定價](https://azure.microsoft.com/pricing/details/documentdb/)。  

### <a name="how-do-i-create-a-database"></a>我如何建立資料庫？
您可以使用 [Azure 入口網站]() (如[建立 Azure Cosmos DB 集合和資料庫](documentdb-create-collection.md)所述)、其中一個 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 或透過 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 來建立資料庫。  

### <a name="how-do-i-set-up-users-and-permissions"></a>我如何設定使用者和權限？
您可以使用其中一個 [DocumentDB API SDK](documentdb-sdk-dotnet.md) 或透過 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 來建立使用者和權限。   

### <a name="does-documentdb-api-support-sql"></a>DocumentDB API 支援 SQL 嗎？
SQL 查詢語言是 SQL 支援的查詢功能增強子集。 Azure Cosmos DB 的 SQL 查詢語言提供豐富的階層式和關聯式運算子，且透過 JavaScript 型使用者定義函式 (UDF) 支援擴充性。 JSON 文法允許將 JSON 文件模型化為樹狀結構，並以標籤當作樹狀結構節點，這是由 Azure Cosmos DB 的自動編製索引技術及 Azure Cosmos DB 的 SQL 查詢方言所使用。  如需如何使用 SQL 文法的詳細資訊，請參閱 [QueryDocumentDB][query] 一文。

### <a name="does-documentdb-api--support-sql-aggregation-functions"></a>DocumentDB API 支援 SQL 彙總函式嗎？
DocumentDB API 透過 SQL 文法，藉由彙總函式 `COUNT`、`MIN`、`MAX`、`AVG` 和 `SUM` 來支援任何規模的低延遲彙總。 如需詳細資訊，請參閱[彙總函式](documentdb-sql-query.md#Aggregates)。

### <a name="how-does-documentdb-api--provide-concurrency"></a>DocumentDB API 如何提供並行存取？
DocumentDB API 透過 HTTP 實體標記或 ETag，支援開放式並行存取控制 (OCC)。 每個 DocumentDB API 資源都有一個 ETag，並且會在每次更新文件時，於伺服器上設定此 ETag。 ETag 標頭和目前的值會包含於所有回應訊息中。 Etag 可與 If-Match 標頭搭配使用，讓伺服器能夠決定是否應該更新資源。 If-Match 值是要經過檢查的 ETag 值。 如果 ETag 值符合伺服器的 ETag 值，就會更新資源。 如果 ETag 不再是最新狀態，則伺服器會拒絕該作業，並提供「HTTP 412 前置條件失敗」回應碼。 用戶端接著必須重新擷取資源，以取得該資源目前的 ETag 值。 此外，ETag 可以與 If-None-Match 標頭搭配使用，來判斷是否需要重新擷取資源。

若要在 .NET 中使用開放式並行存取，可使用 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 類別。 如需 .NET 範例，請參閱 github 上 DocumentManagement 範例中的 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) 。

### <a name="how-do-i-perform-transactions-in-documentdb-api"></a>如何在 DocumentDB API 中執行交易？
DocumentDB API 透過 JavaScript 預存程序和觸發程序，支援語言整合式交易。 如果是單一分割集合，則指令碼內的所有資料庫作業會在集合範圍內的快照隔離底下執行，如果集合已分割，則在集合中具有相同分割索引鍵值的文件底下執行。 文件版本 (ETag) 的快照是在交易開始時取得，且只有當指令碼成功執行時才會認可。 如果 JavaScript 擲回錯誤，則會回復交易。 如需詳細資訊，請參閱 [DocumentDB API 伺服器端程式設計](documentdb-programming.md)。

### <a name="how-can-i-bulk-insert-documents-into-documentdb-api"></a>如何將文件大量插入 DocumentDB API？
有三種方式可將文件大量插入 Azure Cosmos DB：

* 使用資料移轉工具，如[將資料匯入 DocumentDB API](documentdb-import-data.md) 所述。
* 使用 Azure 入口網站的文件總管，如[使用文件總管大量新增文件所述](documentdb-view-json-document-explorer.md#bulk-add-documents)。
* 使用預存程序，如 [DocumentDB API 伺服器端程式設計](documentdb-programming.md)所述。

### <a name="does-documentdb-api-support-resource-link-caching"></a>DocumentDB API 支援資源連結快取嗎？
是，因為 Cosmos DB 是一項 RESTful 服務，資源連結是固定不變且可快取的。 DocumentDB API 用戶端可以指定 "If-None-Match" 標頭來讀取任何資源，例如文件或集合，而且只有當伺服器版本變更時才會更新其本機複本。

### <a name="is-a-local-instance-of-documentdb-api-available"></a>DocumentDB API 的本機執行個體可供使用嗎？
是。 [Azure Cosmos DB 模擬器](documentdb-nosql-local-emulator.md)提供 DocumentDB API 服務的高逼真度模擬。 它支援與 Azure Cosmos DB 完全相同的功能，包括支援建立和查詢 JSON 文件、佈建和擴充集合，以及執行預存程序和觸發程序。 您可以使用 Azure Cosmos DB 模擬器來開發及測試應用程式，並且只需對 Azure Cosmos DB 的連接端點進行單一組態變更，就能將它們部署至全球規模的 Azure。

## <a name="database-questions-about-developing-against-api-for-mongodb"></a>針對 API for MongoDB 進行開發的相關資料庫問題
### <a name="what-is-azure-cosmos-dbs-api-for-mongodb"></a>什麼是 Azure Cosmos DB 之適用於 MongoDB 的 API？
Microsoft Azure Cosmos DB 之適用於 MongoDB 的 API 是一個相容性層級，可讓應用程式使用社群支援的現有 Apache MongoDB API 和驅動程式，輕鬆且透明地與原生 Azure Cosmos DB 資料庫引擎通訊。 開發人員現在可以使用現有的 MongoDB 工具鏈結和技巧來建置可利用 Azure Cosmos DB 的應用程式，進而受益於 Azure Cosmos DB 的獨特功能，其中包括自動編製索引、備份維護、有財務支援的服務等級協定 (SLA) 等。

### <a name="how-to-do-i-connect-to-my-api-for-mongodb-database"></a>如何連線到我的 API for MongoDB 資料庫？
直接前往 [Azure 入口網站](https://portal.azure.com)是連接到 Azure Cosmos DB 之適用於 MongoDB 的 API 的最快方式。 瀏覽至您的帳戶。 在帳戶的「左導覽列」中，按一下 [快速啟動]。 [快速啟動] 是取得程式碼片段以連接到您資料庫的最佳方式。 

Azure Cosmos DB 會強制執行嚴格的安全性需求和標準。 Azure Cosmos DB 帳戶需要驗證，並透過 *SSL* 提供安全通訊，以確保使用 TLSv1.2。

如需詳細資訊，請參閱[連線到 API for MongoDB 資料庫](documentdb-connect-mongodb-account.md)。

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>API for MongoDB 資料庫是否有其他錯誤碼？
除了常見的 MongoDB 錯誤碼，API for MongoDB 本身有特定的錯誤碼。


| 錯誤               | 代碼  | 說明  | 方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 取用的要求單位總數已超過針對集合佈建的要求單位率並已進行節流。 | 請考慮從 Azure 入口網站調整集合的輸送量或重試一次。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 <br><br>*例如：&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="database-questions-about-developing-with-azure-cosmos-db-table-api-preview"></a>關於使用 Azure Cosmos DB：資料表 API (預覽) 進行開發的資料庫問題

### <a name="terms"></a>詞彙 
Azure Cosmos DB：資料表 API (預覽) 是指 Azure Cosmos DB 針對在組建 2017 中宣告之資料表支援所提供的進階供應項目。 

標準資料表 SDK 是預先存在的 Azure 儲存體資料表 SDK。 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>如何使用新的資料表 API (預覽) 供應項目？ 
[Azure 入口網站][azure-portal]中已提供 Microsoft 資料表 API。  首先，您必須註冊 Microsoft Azure 訂用帳戶。  註冊 Microsoft Azure 訂用帳戶之後，您可以將 資料表 API 容器加入您的 Azure 訂用帳戶。  
在預覽期間，[SDK](../cosmos-db/table-sdk-dotnet.md) 適用於 .NET，您可以從完成[資料表 API](../cosmos-db/create-table-dotnet.md) 快速入門來開始。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>我需要新的 SDK，才能使用資料表 API (預覽) 嗎？ 
是，以下提供 Nuget 套件形式的新 SDK：[資料表 API](../cosmos-db/table-sdk-dotnet.md)。 它稱為適用於 Windows Azure 儲存體 8.1.2 且含進階資料表 API (預覽) 的 SDK。  

### <a name="how-do-i-provide-the-feedback-about-the-sdk-bugs"></a>如何提供關於 SDK 和 Bug 的意見反應？
請使用下列其中一種方法來分享您的意見反應：

* [在 Uservoice 上 (英文)](https://feedback.azure.com/forums/263030-documentdb)
* [MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>我需要哪一個連接字串，才能連接到資料表 API (預覽)？
連接字串為 `DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com`。 您可以從 Azure 入口網站的 [金鑰] 頁面取得連接字串。 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-apipreview"></a>如何在新的資料表 API (預覽) 中覆寫要求選項的組態設定？
這些設定是已記載的 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。 您可以在用戶端應用程式內將設定加入至 app.config 中的 appsettings 區段，藉以變更它們。
<appSettings>
    <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
    <add key="TableThroughput" value="<PositiveIntegerValue"/>
    <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
    <add key="TableUseGatewayMode" value="True|False"/>
    <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
</appSettings>


### <a name="is-there-any-change-to-existing-customers-that-use-the-existing-standard-table-sdk"></a>是否已對目前使用現有標準資料表 SDK 的客戶進行任何變更？
無。 並未對使用現有標準資料表 SDK 的現有或新客戶進行任何變更。 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>如何檢視儲存在 Azure Cosmos DB 中的資料表 資料，以便與資料表 API (預覽) 搭配使用？ 
您可以使用 Azure 入口網站來瀏覽資料。 您也可以使用資料表 API (預覽) 程式碼或以下所述的工具。 

### <a name="which-tools-will-work-with-table-api-preview"></a>哪些工具將可與資料表 API (預覽) 搭配使用？ 
舊版的 Azure 檔案總管 (0.8.9)。

能夠彈性地以稍早指定之格式採用連接字串的工具，可支援新的資料表 API (預覽)。 [Azure 儲存體用戶端工具](../storage/storage-explorers.md)頁面上提供一份資料表工具清單。 

### <a name="does-powershellcli-work-with-the-new-table-api-preview-"></a>PowerShell/CLI 可以與新的資料表 API (預覽) 搭配運作嗎？
我們計劃針對資料表 API (預覽) 加入對 PowerShell/CLI 的支援。 

### <a name="is-the-concurrency-on-operations-controlled"></a>可以在作業中控制並行存取嗎？
是，開放式並行存取會透過使用 Etag 機制 (如標準資料表 API 中所預期) 來提供。 

### <a name="is-odata-query-model-supported-for-entities"></a>是否針對實體支援 OData 查詢模型？ 
是，資料表 API (預覽) 支援 OData 查詢和 Linq 查詢。 

### <a name="can-i-connect-to-standard-azure-table-and-the-new-premium-table-api-preview-at-side-by-side-in-same-application-"></a>我可以在同一個應用程式中並行連接至標準 Azure 資料表和新的進階資料表 API (預覽) 嗎？ 
是，達成此動作的方法是，透過連接字串來建立兩個不同的 CloudTableClient 執行個體，並指向它們自己的 URI。

### <a name="how-do-i-migrate-existing-table-storage-application-to-this-new-offering"></a>如何將現有的資料表儲存體應用程式移轉到這個新的供應項目？
如果您想要在現有的資料表儲存體資料上利用新的資料表 API 供應項目，請連絡 askdocdb@microsoft.com。 

### <a name="what-is-the-roadmap-for-this-service-when-will-other-functionality-of-standard-table-api-be-offered"></a>這項服務的藍圖是什麼，何時將提供標準資料表 API 的其他功能？
當我們繼續朝向公開上市邁進時，計劃要加入 SAS 權杖、ServiceContext、統計資料、加密、分析和其他功能的支援。  請在 [Uservoice (英文)](https://feedback.azure.com/forums/263030-documentdb) 上提供意見反應給我們。 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-say-i-start-with-n-gbs-of-data-and-my-data-will-grow-to-1-tb-overtime"></a>假設我一開始就有 N GB 的資料，而我的資料將隨著時間過去成長為 1 TB，則我該如何針對此服務完成儲存體大小的擴充？  
Cosmos DB 是設計來藉由使用水平調整提供無限制的儲存體。 我們的服務將會監視並有效地增加您的儲存體。 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>如何監視資料表 API (預覽) 供應項目？
您可以使用資料表 API (預覽) 的 [計量] 窗格，來監視要求和儲存體使用量。 

### <a name="how-do-i-calculate-throughput-i-require"></a>如何計算我需要的輸送量？
您可以使用 [容量] 估算程式來計算作業所需的 TableThroughput，如此處的[估算要求單位和資料儲存體 (英文)](https://www.documentdb.com/capacityplanner) 所述。 通常，您可以將實體表示為 json，並為您的作業提供編號。 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>我可以在本機搭配使用新的資料表 API (預覽) SDK 與模擬器嗎？
是，當您使用新的 SDK 時，可以在本機模擬器上使用資料表 API (預覽)。 請從[這裡](documentdb-nosql-local-emulator.md)下載新的模擬器。 app.config 中的 StorageConnectionString 值必須是 "DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081"。 

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>我現有的應用程式可以與資料表 API (預覽) 搭配運作嗎？ 
新資料表 API (預覽) 的介面區可以在使用 .NET API 進行的建立、刪除、更新、查詢作業中，與現有的 Azure 標準資料表 SDK 相容。 您需要確定您具有資料列索引鍵，因為資料表 API (預覽) 需要分割區索引鍵和資料列索引鍵。當我們繼續朝向將此服務供應項目公開上市邁進時，也計劃要加入其他 SDK 支援。

### <a name="do-i-need-to-migrate-existing-azure-table-based-application-to-new-sdk-if-i-do-not-want-to-use-table-api-preview-features"></a>如果我不想使用資料表 API (預覽) 功能，還需要將現有的 Azure 資料表式應用程式移轉到新的 SDK 嗎？
否，現有的客戶可以建立和使用現存的標準資料表資產，而不會產生任何形式的中斷。 不過，如果您不使用新的資料表 API (預覽)，就無法從自動索引、額外的一致性選項或全域散發中獲益。 

### <a name="how-do-i-add-replication-for-the-data-in-premium-table-api-preview-across-multiple-regions-of-azure"></a>如何跨 Azure 的多個區域，在進階資料表 API (預覽) 中加入對資料的複寫？
您可以使用 Cosmos DB 入口網站的[全域複寫設定](documentdb-portal-global-replication.md)，以加入適合您應用程式的區域。 若要開發全域散發的應用程式，您還應該使用設為本地區域的 PreferredLocation 資訊來加入您的應用程式，以提供低讀取延遲。 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in--premium-table-apipreview"></a>如何在進階資料表 API (預覽) 中變更帳戶的主要寫入區域？
您可以使用 Cosmos DB 的全球複寫入口網站窗格來加入一個區域，然後容錯移轉至所需的區域。 如需指示，請參閱[使用多區域 Azure Cosmos DB 帳戶進行開發](documentdb-regional-failovers.md)。 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>當我散發資料時，如何設定慣用的讀取區域以取得低延遲？ 
您需要利用 app.config 中的 PreferredLocation 索引鍵，以協助您從本地位置進行讀取。 針對現有的應用程式，如果設定了 LocationMode，資料表 API (預覽) 將會擲回錯誤，請移除該程式碼，因為進階資料表 API (預覽) 將會從 app.config 檔案中挑選此資訊。  這些設定是已記載的 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。


### <a name="how-do-i-think-of-consistency-in-table-api-preview"></a>如何看待資料表 API (預覽) 中的一致性？ 
Cosmos DB 可在一致性、高可用性及延遲之間提供完全合乎邏輯的取捨。 從這個版本開始，它會提供 5 個一致性層級，讓您可從中選擇。 這些一致性層級可供資料表 API (預覽) 開發人員使用。 這可讓開發人員在查詢資料時，於資料表層級和個別要求上選擇正確的一致性模型。  當用戶端連接時，它可以指定一致性層級，這可透過 app.config 設定中 TableConsistencyLevel 索引鍵的值來變更。 資料表 API (預覽) 會利用您自己預設使用工作階段一致性寫入的讀取，來提供低延遲的讀取。 如需詳細資訊，請參閱[一致性層級](documentdb-consistency-levels.md)。 根據預設，標準資料表目前提供區域內的強式一致性，以及次要位置中最終的一致性。  

### <a name="does-this-mean-compared-to-eventual--strong-consistency-that-is-possible-with-standard-table---we-now-have-more-choices"></a>這表示相較於標準資料表能夠提供的最終和強式一致性，我們現在有更多選項嗎？
是，這些選項都記載於[一致性層級](documentdb-consistency-levels.md)一文中，可協助應用程式開發人員運用 Cosmos DB 的分散式特性。 由於也會針對一致性提供保證，因此您目前可安心運用這些選項。 這些設定是已記載的 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。

### <a name="when-global-distribution-is-enabled--how-long-does-it-take-to-replicate-the-data"></a>啟用全域散發時，複寫資料需要多久的時間？
我們會在本地區域中永久認可資料，並在數毫秒內立即將資料推送至其他區域，而此複寫只取決於資料中心的 RTT。 請參閱 [Azure Cosmos DB︰Azure 上分散在世界各地的資料庫服務](documentdb-distribute-data-globally.md)中關於 Cosmos DB 的全域散發能力。

### <a name="can-the-read-request-consistency-be-changed"></a>可以變更讀取要求一致性嗎？
Cosmos DB 允許在設為資料表的容器層級上設定一致性。 SDK 也允許在 app.config 檔案中提供 TableConsistencyLevel 索引鍵的值，藉以變更層級。 這些是可能的值：Strong|Bounded Staleness|Session|ConsistentPrefix|Eventual。 這記載於[一致性層級](documentdb-consistency-levels.md)一文中。 這裡的重點是請記住，要求一致性不能超過針對資料表所設定的一致性。 例如，如果在強式要求的最終和設定一致性層級上擁有資料表的設定一致性，則該一致性將不會運作。 

### <a name="how-does-premium-table-api-preview-account-take-care-of-failover-in-case-a-region-goes-down"></a>進階資料表 API (預覽) 帳戶如何處理容錯移轉以防區域中斷？ 
進階資料表 API (預覽) 會利用 Cosmos DB 的全域散發平台。 若要確保您的應用程式可以容許資料中心停機，您必須在 Cosmos DB 入口網站中針對該帳戶至少啟用一個以上的區域，[使用多區域 Azure Cosmos DB 帳戶進行開發](documentdb-regional-failovers.md)。 您可以使用入口網站來設定區域的優先順序，[使用多區域 Azure Cosmos DB 帳戶進行開發](documentdb-regional-failovers.md)。 您可以針對帳戶加入任意數目的區域，並藉由提供優先順序來控制可將它容錯移轉至其中的位置。 當然，您原本也需要在該處提供應用程式以運用資料庫。 如此一來，您的客戶將不會看到停機時間。 用戶端 SDK 將會自動連接，它可以偵測到區域已關閉，並自動容錯移轉至新的區域。

### <a name="is-premium-table-api-preview-enabled-for-backups"></a>是否已啟用進階資料表 API (預覽) 進行備份？
是，進階資料表 API (預覽) 會利用 Cosmos DB 平台進行備份。 備份會自動執行。 Cosmos DB 備份的文件於此：[使用 Azure Cosmos DB 進行線上備份與還原](C:\Users\govindk\azure-docs-pr\articles\documentdb\documentdb-online-backup-and-restore.md)

 
### <a name="does-the-table-api-preview-index-all-attributes-of-entities-by-default"></a>資料表 API (預覽) 預設會為實體的所有屬性編製索引嗎？
是，根據預設，會為實體的所有屬性編製索引。 編製索引的詳細資料記載於 [Azure Cosmos DB︰編製索引原則](documentdb-indexing-policies.md)一文中。 

### <a name="does-this-mean-i-do-not-have-to-create-different-indexes-to-satisfy-the-queries"></a>這表示我不需要建立不同的索引來滿足查詢嗎？ 
是，Cosmos DB 會為所有屬性提供自動編製索引，而不需任何結構描述定義。 這樣讓開發人員能夠專注於應用程式，而不需擔心索引的建立和管理。 編製索引的詳細資料記載於 [Azure Cosmos DB︰編製索引原則](documentdb-indexing-policies.md)一文中。

### <a name="can-the-indexing-policy-be-changed"></a>可以變更編製索引原則嗎？
是，您可以提供索引定義來變更索引。 這些設定的意義記載於 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)一文中。 您需要適當地進行編碼和逸出這些設定。  

利用 app.config 檔案內的字串 json 格式。
{ "indexingMode": "consistent", "automatic": true, "includedPaths": [ { "path": "/somepath", "indexes": [ { "kind": "Range", "dataType": "Number", "precision": -1 }, { "kind": "Range", "dataType": "String", "precision": -1 } ] } ], "excludedPaths": [ { "path": "/anotherpath" } ] }

### <a name="cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-like-sorting-aggregates-hierarchy-and-other-functionality---is-this-planned-to-be-added-to-the-table-api-"></a>Cosmos DB 作為平台似乎還有很多功能，例如排序、彙總、階層及其他功能，是否計劃要將此功能加入至資料表 API ？ 
在預覽中，Azure Cosmos DB 支援的資料表 API 查詢功能與 Azure 資料表儲存體相同。 Azure Cosmos DB 也支援排序、彙總、地理空間查詢、階層及各種不同的內建函式。 在未來的服務更新中，將會在資料表 API 中提供額外的功能。 如需這些功能的概觀，請參閱 [Azure Cosmos DB 查詢](../documentdb/documentdb-sql-query.md)。
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>我何時應該變更資料表 API (預覽) 的 TableThroughput？
當您執行資料的 ETL 或想要在短時間內上傳大量資料時，就應該變更 TableThroughput。 

或

您需要更多來自後端容器的輸送量，因為您在計量上看見使用的輸送量比佈建輸送量還多，而您正要開始進行節流。 這記載於[設定輸送量](documentdb-set-throughput.md)一文中。

### <a name="can-i-scale-up-or-down-the-throughput-of-my-table-api-preview-table"></a>我可以相應增加或減少資料表 API (預覽) 資料表的輸送量嗎？ 
是，您可以使用 Cosmos DB 入口網站的 [調整] 窗格來執行相同的動作。 這記載於[設定輸送量](documentdb-set-throughput.md)主題中。

### <a name="can-premium-table-api-preview-leverage-ru-per-minute-offering"></a>進階資料表 API (預覽) 可以運用每分鐘供應項目的 RU 嗎？ 
是，進階資料表 (預覽) 會運用 Cosmos DB 的功能，針對效能、延遲、可用性和一致性提供 SLA。 這確保它可運用每分鐘供應項目的 RU，如[要求單位](documentdb-request-units.md)所述。 此功能可讓客戶無法在尖峰時間進行佈建，並緩和突然增加的工作負載。

### <a name="is-there-a-default-tablethroughput-which-is-set-for-newly-provisioned-tables"></a>是否有預設的 TableThroughput，可設定來供新的佈建資料表使用？
是，如果您不會透過 app.config 覆寫 TableThroughput，且不會在 Cosmos DB 中使用預先建立的容器，服務會建立輸送量為 400 的資料表。
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-standard-table-api"></a>對於標準資料表 API 的現有客戶，是否有任何定價變更？
無。 對於現有的標準資料表 API 客戶，沒有任何價格上的變更。 

### <a name="how-is-the-price-calculated-for-the-table-apipreview"></a>如何針對資料表 API (預覽) 計算價格？ 
這取決於配置的 TableThroughput。 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>如何在資料表 API (預覽) 供應項目中處理資料表上的任何節流？ 
如果要求速率超過基礎容器之佈建輸送量的容量，您將會收到錯誤，而 SDK 將使用重試原則來重試呼叫。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-leverage-premium-table-preview-offering-of-cosmos-db"></a>為何我需要選擇 PartitionKey 和 RowKey 以外的輸送量，以運用 Cosmos DB 的進階資料表 (預覽) 供應項目？
如果您未在 app.config 中提供輸送量，Cosmos DB 將為您的容器設定預設輸送量。 

Cosmos DB 會針對效能以及作業中的延遲上限提供保證。 當引擎可在租用戶的作業強制執行控管時，這是可行的。 設定 TableThroughput 基本上可確保您會取得保證的輸送量、延遲，因為平台現在將保留這個容量，並保證作業成功。  
輸送量的規格也可讓您彈性地變更它來運用您應用程式的季節性，並符合輸送量需求和節省成本。

### <a name="azure-storage-sdk-has-been-very-cheap-for-me-as-i-only-pay-to-store-the-data-and-i-rarely-query-cosmos-dbs-new-offering-seems-to-be-charging-me-even-though-i-have-not-performed-single-transaction-or-stored-anything-whats-going-on-here"></a>Azure 儲存體 SDK 對我而言非常便宜，因為我只需要支付資料的儲存，而且我很少查詢。 即使我尚未執行單一交易或儲存任何項目，Cosmos DB 的新供應項目似乎還是會向我收取費用。 這其中發生了什麼狀況？

Cosmos DB 已設計為全域散發的 SLA 型系統，並提供可用性、延遲和輸送量的保證。 當您在 Cosmos DB 中保留輸送量時，與其他系統不同的是，它會提供保證。 Cosmos DB 也會提供客戶長久以來所要求的額外功能，例如，次要索引、全域散發等。在預覽期間，我們提供輸送量最佳化的模型，從長遠來看，我們計劃提供儲存體最佳化的模型，以符合客戶的需求。  

### <a name="i-never-get-quota-full-indicating-a-partition-is-full-when-i-keep-ingesting-data-into-azure-table-storage-with-the-table-apipreview--i-can-get-this-error-is-this-offering-limiting-me-and-forcing-me-to-change-my-present-application"></a>當我將資料擷取到 Azure 資料表儲存體時，永遠無法取得完整的「配額」(表示分割區已滿)。 當我使用資料表 API (預覽) 時，會遇到此錯誤。 這個供應項目會限制我，並強迫我變更我目前的應用程式嗎？

Cosmos DB 是 SLA 型系統，可提供無限制的規模並為延遲、輸送量、可用性、一致性提供保證。 若要確保您會取得保證的進階效能，您必須確定該資料大小，索引是可管理且可擴充的。 每個分割區索引鍵的實體/項目數目限制為 10 GB，這是為了確保我們能夠提供絕佳的查閱、查詢效能。 為了確保您的應用程式能夠針對 Azure 儲存體進行更佳的調整，我們要求您不要藉由儲存某一個分割區的所有資訊，然後進行查詢，來建立熱分割區。  

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>因此，新的資料表 API (預覽) 仍然需要 PartitionKey 和 RowKey 嗎？ 
是。 因為資料表 API (預覽) 的介面區與 Azure 資料表儲存體 SDK 類似，所以，分割區索引鍵會提供一種絕佳方式來散發資料。 資料列索引鍵在該分割區內是唯一的。 是，資料列索引鍵必須存在且不能為 Null，如同在標準 SDK 中。 截至組建預覽為止，RowKey 的長度是 255 個位元組，而 PartitionKey 為 100 個位元組 (將儘速提高至 1 KB)。 

### <a name="what-will-be-the-error-messages-of-table-api-preview-"></a>資料表 API (預覽) 的錯誤訊息將有哪些？
由於此預覽可與標準資料表相容，因此，大多數的錯誤將對應至標準資料表的錯誤。 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another--in-tables-api-preview"></a>當我嘗試在資料表 API (預覽) 中逐一建立許多資料表時，為什麼要進行節流？
Cosmos DB 是 SLA 型系統，可提供提供延遲、輸送量、可用性及一致性的保證。 由於它是佈建的系統，因此會保留資源以保證這些需求。 系統會偵測到以快速方式建立資料表的速率，並加以節流。 我們會建議您查看建立資料表的速率，並和緩地將其向下調整為每分鐘 5 以下。 請記住，資料表 API (預覽) 是一個佈建的系統，因此當您佈建它時，就必須針對它付費。  

# <a name="graph-apipreview"></a>圖形 API (預覽)
## <a name="database-questions-about-developing-against-graph-apipreview"></a>關於針對圖形 API (預覽) 進行開發的資料庫問題
### <a name="how-can-i-leverage-graph-api-preview-with-cosmos-db"></a>如何能夠搭配 Cosmos DB 來運用圖形 API (預覽)？
您可以從擴充程式庫運用圖形 API (預覽) 的功能。 它稱為 Microsoft Azure 圖形，而且可在 NuGet 中取得。 

### <a name="it-looks-like-you-support-gremlin-as-traversal-language-do-you-plan-to-add-some-more-forms-of-query-"></a>您看起來像是支援 gremlin 作為周遊語言，您打算加入更多形式的查詢嗎？
是，我們計劃在未來加入其他查詢機制。 

### <a name="how-can-i-use-the-new-graph-apipreview-offering"></a>如何使用新的圖形 API (預覽) 供應項目？ 
請從完成[圖形 API](../cosmos-db/create-graph-dotnet.md) 快速入門來開始。




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

