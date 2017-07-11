---
title: "Azure Cosmos DB 簡介 | Microsoft Docs"
description: "了解 Azure Cosmos DB。 這個全球散發的多模型資料庫是針對低延遲、彈性的延展性和高可用性所建置。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 49eb2e4f7d57de44a3b7a877dfdd138f4c374436
ms.contentlocale: zh-tw
ms.lasthandoff: 06/29/2017

---

<a id="welcome-to-azure-cosmos-db" class="xliff"></a>

# 歡迎使用 Azure Cosmos DB

Azure Cosmos DB 是 Microsoft 的全球散發多模型資料庫。 透過按鈕點選，Azure Cosmos DB 可讓您有彈性且獨立地跨任意數目的 Azure 地理區域調整輸送量和儲存體。 它利用完整的[服務等級協定](https://aka.ms/acdbsla) (SLA) 提供了輸送量、延遲、可用性和一致性的保證，這是其他資料庫服務無法提供的。

![Azure Cosmos DB 是 Microsoft 的全球散發資料庫服務，包含彈性的相應放大、低延遲保證、五個一致性模型，以及完整保證的 SLA](./media/introduction/azure-cosmos-db.png)

Azure Cosmos DB 包含已最佳化寫入、已管理資源、無結構描述的資料庫引擎，它原生支援多重資料模型：機碼值、文件、圖形和單欄式。 它也支援許多以可延伸的方式用來存取資料的 API，包括 [MongoDB](mongodb-introduction.md)、[DocumentDB SQL](documentdb-introduction.md)、[Gremlin](graph-introduction.md) (預覽版) 以及 [Azure 資料表](table-introduction.md) (預覽版)。 

Azure Cosmos DB 於 2010 年末開始開發，可解決 Microsoft 內部大規模應用程式所面對的開發人員難題。 因為建置全域散發的應用程式不只是 Microsoft 特有的問題，所以我們透過 Azure DocumentDB 的形式將該服務提供給外部所有 Azure 開發人員使用。 Azure Cosmos DB 是 DocumentDB 演進中重要的下一步，而我們現在將它提供給您使用。 在這一版的 Azure Cosmos DB 中，DocumentDB 客戶 (與其資料) 會自動無縫轉換為 Azure Cosmos DB 客戶。 此外，他們現在可以存取由 Azure Cosmos DB 所提供的各種新功能。 

<a id="capability-comparison" class="xliff"></a>

## 功能比較

Azure Cosmos DB 會提供關聯式與非關聯式資料庫的最佳功能。

| 功能 | 關聯式資料庫 | 非關聯式 (NoSQL) 資料庫 |  Azure Cosmos DB |
| --- | --- | --- | --- |
| 全球發佈 | x | x | ✓ 周全且立即可用、30 個以上的區域、多路連接 |
| 水平調整 | x | ✓  | ✓ 獨立調整儲存體和輸送量 | 
| 延遲保證 | x | ✓  | ✓ 讀取 <10 毫秒，寫入 <15 毫秒 (99 個百分位數) | 
| 高可用性 | x | ✓  | ✓ 永遠可用、PACELC 折衷辦法、自動與手動容錯移轉 |
| 資料模型 + API | 關聯式 + SQL | 多模型 + OSS API | 多模型 + SQL + OSS API (更多即將推出) |
| SLA | ✓  | x | ✓ 延遲、輸送量、一致性、可用性的完整 SLA |

<a id="key-capabilities" class="xliff"></a>

## 主要功能
Azure Cosmos DB 是一個全球散發資料庫服務，提供下列功能以協助您建置可調整、全球散發、回應速度快的應用程式：

* [**周全且立即可用的全球散發**](#global-distribution)
    * 不論您的使用者身在何處，您的應用程式都能立即提供給他們使用。 現在，您的資料也能如此。
    * 不需要擔心硬體、新增節點、VM 或核心數。 只要點按一下滑鼠，馬上就能取得您的資料。 

* [**存取和查詢資料的多重資料模型與常用 API**](#data-models)
    * 支援多重資料模型，包括機碼值、文件、圖形和單欄式。
    * 適用於 Node.js、Java、.NET、.NET Core、Python 和 MongoDB 的可延伸 API。
    * 用於查詢的 SQL 和 Gremlin。 

* [**全球性依需求彈性調整輸送量和儲存體**](#horizontal-scale)
    * 輕鬆地以[秒](request-units.md)和[分鐘](https://aka.ms/acdbrupm)的細微度調整輸送量，並隨時依需求變更。 
    * [在背景自動](partition-data.md)調整儲存體，以不限次數涵蓋您的大小需求。

* [**建置回應速度快和關鍵任務應用程式**](#low-latency) 
    * 在全球任何地方存取您的資料，僅個位數毫秒的延遲 (99 個百分位數)。 

* [**確保「永遠可用」可用性**](#high-availability)
    * 單一區域內的 99.99% 可用性。
    * 針對更高的可用性部署至任意數目的 [Azure 區域](https://azure.microsoft.com/regions)。
    * 透過資料零遺失保證來[模擬一或多個區域的錯誤](regional-failover.md)。 

* [**以正確方式撰寫全球散發應用程式**](#consistency)
    * [五個一致性模型](consistency-levels.md)提供與強式 SQL 類似的一致性給類似 NoSQL 的最終一致性，以及其間的所有事。 
  
* [**退款保證**](#sla) 
    * 保證快速取得您的資料，否則退款。 
    * 可用性、延遲、輸送量和一致性的[服務等級協定](https://aka.ms/acdbsla)。 

* [**無資料庫結構描述/索引管理**](#schema-free)
    * 不用再擔心維持您的資料庫結構描述和索引與您的應用程式結構描述之間的同步處理了。 我們完全不需要結構描述。 

* [**低擁有權成本**](#tco)
    * 非管理式解決方案[更符合成本效益](https://aka.ms/documentdb-tco-paper)五到十倍。
    * 比 DynamoDB 便宜三倍。

<a id="global-distribution"></a>

<a id="global-distribution" class="xliff"></a>

## 全球發佈
Azure Cosmos DB 容器沿著兩個維度散發： 

1. 在指定區域中，會使用資源分割區來水平分割所有資源 (區域散發)。 
2. 每個資源分割區也會跨地理區域複寫 (全球散發)。 

![描繪 Azure Cosmos DB 全球散發的圖表](./media/introduction/azure-cosmos-db-global-distribution.png) 

當您的儲存體和輸送量需要調整時，Cosmos DB 會在背景自動跨所有區域執行分割區管理作業。 不論是調整、散發或發生錯誤，Cosmos DB 都會繼續提供全球散發資源的單一系統映像。 

Cosmos DB 中資源的全球散發是[周全且立即可用的](distribute-data-globally.md)。 隨時透過按鈕點選 (或以程式設計方式使用單一 API 呼叫)，您可以將任意數目的地理區域與您的資料庫帳戶相關聯。 

不論資料量或區域數目為何，Cosmos DB 都可以保證每一個新關聯的區域會在一小時內開始處理用戶端要求 (99 個百分位數)。 這會透過平行處理植入和複製所有來源資源分割區的資料至新關聯的區域來完成。 客戶也可以移除現有的區域，或者將先前與其資料庫帳戶相關聯的區域離線。

<a id="data-models"></a>
<a id="multi-model-multi-api-support" class="xliff"></a>

## 支援多模型、多 API
 Azure Cosmos DB 原生支援多重資料模型，包括文件、機碼值、圖形和單欄式。 Cosmos DB 資料庫引擎的核心內容模型是以 Atom-記錄-序列 (ARS) 為基礎。 Atom 是由一小組基元類型 (例如字串、布林值和數字) 所組成。 記錄是由這些類型組成的結構。 序列是由 Atom、記錄或序列所組成的陣列。 
 
 資料庫引擎可以有效地將不同的資料模型轉譯並投影到 ARS 型資料模型上。 Cosmos DB 的核心資料模型可由動態輸入的程式設計語言原生存取，並能以原樣公開為 JSON。 
 
 服務也支援用於資料存取及查詢的熱門資料庫 API。 Cosmos DB 的資料庫引擎目前支援 [DocumentDB SQL](documentdb-introduction.md)、[MongoDB](mongodb-introduction.md)、[Azure 資料表](table-introduction.md) (預覽版) 和 [Gremlin](graph-introduction.md) (預覽版)。 您可以繼續使用熱門 OSS API 來建置應用程式，並獲得經過實際測試、完全受管理且全球散發之資料庫服務的所有好處。 

<a id="horizontal-scale"></a>
<a id="horizontal-scaling-of-storage-and-throughput" class="xliff"></a>

## 儲存體和輸送量的水平調整
Cosmos DB 容器中的所有資料 (例如，文件集合、資料表或圖形) 會由資源分割區以水平方式分割並在背景自動管理。 資源分割區是由[客戶指定的分割區索引鍵](partition-data.md)所分割之資料的一致且高度可用的容器。 它針對它所管理的一組資源提供單一系統映像，且為擴充性和散發的基本單位。 Cosmos DB 的設計可讓您根據跨不同地理區域的應用程式流量模式，有彈性地調整輸送量，以支援依位置和時間波動的工作負載。 服務會在背景自動管理分割區，而不會破壞 Cosmos DB 容器的可用性、一致性、延遲或輸送量。  
 
![Azure Cosmos DB 可水平調整](./media/introduction/azure-cosmos-db-partitioning.png) 

您可以有彈性地調整 Azure Cosmos DB 容器的輸送量，方法是使用[每秒要求單位 (RU/秒)](request-units.md) 透過程式設計方式佈建輸送量。 就內部而言，服務會在背景自動管理資源分割區，以提供指定容器上的輸送量。 Cosmos DB 確保輸送量可供與容器相關聯的所有區域使用。 新的輸送量會在設定輸送量值變更的五秒內生效。 

您可以根據每秒和[每分鐘 (RU/m)](request-units-per-minute.md) 的細微度，在 Cosmos DB 容器上佈建輸送量。 根據每分鐘細微度佈建的輸送量，用來管理發生在每秒細微度的非預期工作負載尖峰。 

<a id="low-latency"></a>
<a id="low-latency-guarantees-at-the-99th-percentile" class="xliff"></a>

## 低延遲保證 (99 個百分位數)
Cosmos DB 的 SLA 保證其客戶的端對端低延遲 (99 個百分位數)。 針對典型的 1 KB 項目，Cosmos DB 保證在相同的 Azure 區域內讀取低於 10 毫秒以及索引化寫入低於 15 毫秒的端對端延遲 (99 個百分位數)。 中間的延遲性就可大幅降低 (低於 5 毫秒)。  透過每個資料庫交易的要求處理上限，Cosmos DB 讓用戶端能清楚地分辨高延遲交易與資料庫無法使用的情況。

<a id="high-availability"></a>
<a id="transparent-multi-homing-and-9999-high-availability" class="xliff"></a>

## 透明多路連接和 99.99% 高可用性
您可以動態將「優先順序」指派給與您的 Azure Cosmos DB 資料庫帳戶相關聯的區域。 優先順序是用來在不幸發生區域性錯誤時，將要求導向至特定區域。 在不太可能發生的區域性災害中，Cosmos DB 會自動根據優先順序來進行容錯移轉。

若要測試應用程式的端對端可用性，您可以[手動觸發容錯移轉](regional-failover.md) (速率限制是一小時內兩個作業)。 Cosmos DB 保證手動區域性容錯移轉期間資料零遺失。 萬一發生區域性災害，Cosmos DB 保證系統起始自動容錯移轉期間的資料遺失上限。 在區域性容錯移轉之後，您不必重新部署您的應用程式，而且 Azure Cosmos DB 會維持保證的可用性 SLA。 

針對這種情況，Cosmos DB 可讓您使用邏輯 (與區域無關) 或實體 (區域特定) 端點來和資源互動。 萬一進行容錯移轉時，前者可確保能在背景自動多路連接應用程式。 後者可微調控制應用程式，將讀取和寫入重新導向到特定區域。 Cosmos DB 保證每個資料庫帳戶 99.99% 可用性 SLA。 可用性保證與下列項目無關：規模調整 (佈建的輸送量和儲存體)、區域數目、與指定資料庫相關聯之區域之間的地理距離。 

<a id="consistency"></a>
<a id="multiple-well-defined-consistency-models" class="xliff"></a>

## 多個定義完善的一致性模型
商業散發資料庫分為兩類：完全不會提供完善且可證明之一致性選擇的資料庫，以及提供兩個極端的可程式性選擇 (強式與最終一致性) 的資料庫。 前者增加了應用程式開發人員其複寫通訊協定細節的負擔，並期望他們在一致性、可用性、延遲和輸送量之間做出困難的取捨。 後者則是將壓力放在選擇兩個極端其中之一。 儘管有超過 50 個一致性模型的研究和建議，散發資料庫社群仍無法商業化超越強式與最終一致性的一致性層級。 

Cosmos DB 讓您在[五個完善定義的一致性模型](consistency-levels.md)，以及一致性頻譜之間選擇 – 強式、限定過期、[工作階段](http://dl.acm.org/citation.cfm?id=383631)、一致前置詞和最終。 

![Azure Cosmos DB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇](media/introduction/azure-cosmos-db-consistency-levels.png)

下表說明每個一致性層級提供的特定保證。
 
**一致性層級和保證**

| 一致性層級 | 保證 |
| --- | --- |
| 強式 | 線性化能力 |
| 限定過期 | 一致前置詞。 讀取落後寫入 (k 前置詞或 t 間隔) |
| 工作階段   | 一致前置詞。 單純讀取、單純寫入、讀取您的寫入、讀取後接寫入 |
| 一致前置詞 | 傳回的更新是所有更新的部分前置詞 (沒有間隔) |
| 最終  | 次序錯誤讀取 |

您可以在 Cosmos DB 帳戶上設定預設一致性層級 (並在稍後覆寫特定讀取要求的一致性)。 就內部而言，預設的一致性層級適用於跨區域之分割區集合內的資料。 


<a id="sla"></a>
<a id="guaranteed-service-level-agreements" class="xliff"></a>

## 保證的服務等級協定

Cosmos DB 是針對可用性、輸送量、低延遲與一致性提供 99.99% [SLA 保證](https://aka.ms/acdbsla)的第一個受管理資料庫服務。
* 可用性：針對每個資料面和控制面作業提供 99.99% 的執行時間可用性 SLA。
* 輸送量︰已順利完成 99.99% 的要求 
* 延遲︰99.99% 的 <10 毫秒延遲 (99 個百分位數)
* 一致性︰100% 的讀取要求將符合您所要求之一致性層級的一致性保證。


<a id="schema-free"></a>
<a id="schema-free" class="xliff"></a>

## 不需要結構描述

關聯式和 NoSQL 資料庫兩者都會強制您處理結構描述與索引管理、版本控制和移轉 – 這一切在全球散發安裝中都極具挑戰性。 但別擔心 -- Cosmos DB 會處理這個問題！ 透過 Cosmos DB，您不必管理結構描述和索引、處理結構描述版本控制或擔心在移轉結構描述時應用程式停機。 Cosmos DB 的資料庫引擎完全不需要結構描述 – 它會自動建立它所內嵌之資料的索引，而不需要任何結構描述或索引並提供超級快速的查詢。 

<a id="tco"></a>
<a id="low-cost-of-ownership" class="xliff"></a>

## 降低擁有權成本

 考量所有的擁有權總成本 (TCO) 時，Azure Cosmos DB 這樣的受管理雲端服務，成本效益可以是其內部部署或虛擬機器上執行之 OSS 對應項目的五到十倍。 而針對大量工作負載， Azure Cosmos DB 更是比 DynamoDB 便宜兩到三倍。 在 [TCO 白皮書 (英文)](https://aka.ms/documentdb-tco-paper) 中深入了解。 

<a id="next-steps" class="xliff"></a>

## 後續步驟
透過下列其中一個快速入門開始使用 Azure Cosmos DB：

* [開始使用 Azure Cosmos DB 的 DocumentDB API](create-documentdb-dotnet.md)
* [開始使用 Azure Cosmos DB 的 MongoDB API](create-mongodb-nodejs.md)
* [開始使用 Azure Cosmos DB 的圖形 API](create-graph-dotnet.md)
* [開始使用 Azure Cosmos DB 的資料表 API](create-table-dotnet.md)

