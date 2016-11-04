---
title: DocumentDB Node.js API 和 SDK | Microsoft Docs
description: 了解所有 Node.js API 和 SDK 相關資訊，包括 發行日期、停用日期及 DocumentDB Node.js SDK 每個版本之間的變更。
services: documentdb
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/09/2016
ms.author: rnagpal

---
# DocumentDB API 和 SDK
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## DocumentDB Node.js API 和 SDK
<table>

<tr><td>**下載 SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**API 文件**</td><td>[Node.js API 參考文件](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**SDK 安裝指示**</td><td>[安裝指示](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**提供給 SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**範例**</td><td>[Node.js 程式碼範例](documentdb-nodejs-samples.md)</td></tr>

<tr><td>**開始使用教學課程**</td><td>[開始使用 Node.js SDK](documentdb-nodejs-get-started.md)</td></tr>

<tr><td>**Web 應用程式教學課程**</td><td>[使用 DocumentDB 建置 Node.js Web 應用程式](documentdb-nodejs-application.md)</td></tr>

<tr><td>**目前支援的平台**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

## 版本資訊
### <a name="1.9.0"/>1.9.0</a>
* 新加入已節流處理要求的重試原則支援。(已節流處理的要求會收到要求率太大的例外狀況，即錯誤碼 429。) 根據預設，發生錯誤碼 429 時，DocumentDB 會遵守回應標頭中的 retryAfter 時間，並針對每個要求重試九次。如果您想要忽略伺服器在多次重試之間傳回的 retryAfter 時間，現在可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中設定固定的重試間隔時間。DocumentDB 現在會針對每個節流處理中的要求等候最多 30 秒 (不論重試計數為何)，並傳回包含錯誤碼 429 的回應。您也可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中覆寫該時間。
* DocumentDB 現在會傳回 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 做為每個要求的回應標頭，其代表節流重試計數和要求歷經多次重試的累積時間。
* 新增 RetryOptions 類別，以及公開 ConnectionPolicy 類別上的 RetryOptions 屬性，使其能用來覆寫某些預設的重試選項。

### <a name="1.8.0"/>1.8.0</a>
* 新增對多重區域資料庫帳戶的支援。

### <a name="1.7.0"/>1.7.0</a>
* 新加入文件的存留時間 (TTL) 功能支援。

### <a name="1.6.0"/>1.6.0</a>
* 實作[分割集合](documentdb-partition-data.md)和[使用者定義的效能等級](documentdb-performance-levels.md)。

### <a name="1.5.6"/>1.5.6</a>
* 已修正 RangePartitionResolver.resolveForRead 錯誤，其因錯誤的結果 CONCAT 而無法傳回連結。

### <a name="1.5.5"/>1.5.5</a>
* 修正 hashParitionResolver resolveForRead()：所提供的資料分割索引鍵都未擲回例外狀況時，而不會傳回所有已註冊連結的清單。

### <a name="1.5.4"/>1.5.4</a>
* 修正問題 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - HTTPS 專用代理程式：避免基於 DocumentDB 用途而修改全域代理程式。針對所有 lib 要求使用專用的代理程式。

### <a name="1.5.3"/>1.5.3</a>
* 修正問題 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 正確處理媒體識別碼中的連字號。

### <a name="1.5.2"/>1.5.2</a>
* 修正問題 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 接聽程式洩漏警告。

### <a name="1.5.1"/>1.5.1</a>
* 修正問題 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - 重新命名資料夾雜湊以針對區分大小寫的系統進行雜湊。

### <a name="1.5.0"/>1.5.0</a>
* 藉由新增雜湊和範圍分割解析程式來實作分區化支援。

### <a name="1.4.0"/>1.4.0</a>
* 實作 Upsert。documentClient 上新的 upsertXXX 方法。

### <a name="1.3.0"/>1.3.0</a>
* 已略過以配合其他 SDK 的版本號碼。

### <a name="1.2.2"/>1.2.2</a>
* 將 Q Pomise 包裝函式分割至新的儲存機制。
* 更新至 npm 登錄的封裝檔案。

### <a name="1.2.1"/>1.2.1</a>
* 實作以識別碼為基礎的路由。
* 修正問題 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - 目前屬性與 current() 方法衝突。

### <a name="1.2.0"/>1.2.0</a>
* 新增對地理空間索引的支援。
* 驗證所有資源的識別碼屬性。資源的識別碼不能包含 ?、/、#、&#47;&#47; 等字元，或在結尾處使用空格。
* 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="1.1.0"/>1.1.0</a>
* 實作 V2 索引原則。

### <a name="1.0.3"/>1.0.3</a>
* 問題 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 核心與 Promise SDK 中實作的 eslint 和 grunt 組態。

### <a name="1.0.2"/>1.0.2</a>
* 問題 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Promise 包裝函式不包括標頭並發生錯誤。

### <a name="1.0.1"/>1.0.1</a>
* 已實作透過新增 readConflicts、readConflictAsync 及 queryConflicts 來查詢衝突的功能。
* 已更新 API 文件。
* 問題 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync 錯誤。

### <a name="1.0.0"/>1.0.0</a>
* GA SDK。

## 發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月**之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [!WARNING]
> 適用於 Node.js 之所有 **1.0.0** 之前的 Azure DocumentDB SDK 版本都將於 **2016 2 月 29 日停用**。
> 
> 

<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [1\.9.0](#1.9.0) |2016 年 7 月 7 日 |--- |
| [1\.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1\.7.0](#1.7.0) |2016 年 4 月 26 日 |--- |
| [1\.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1\.5.6](#1.5.6) |2016 年 3 月 8 日 |--- |
| [1\.5.5](#1.5.5) |2016 年 2 月 2 日 |--- |
| [1\.5.4](#1.5.4) |2016 年 2 月 1 日 |--- |
| [1\.5.2](#1.5.2) |2016 年 1 月 26 日 |--- |
| [1\.5.2](#1.5.2) |2016 年 1 月 22 日 |--- |
| [1\.5.1](#1.5.1) |2016 年 1 月 4 日 |--- |
| [1\.5.0](#1.5.0) |2015 年 12 月 31 日 |--- |
| [1\.4.0](#1.4.0) |2015 年 10 月 6 日 |--- |
| [1\.3.0](#1.3.0) |2015 年 10 月 6 日 |--- |
| [1\.2.2](#1.2.2) |2015 年 9 月 10 日 |--- |
| [1\.2.1](#1.2.1) |2015 年 8 月 15 日 |--- |
| [1\.2.0](#1.2.0) |2015 年 8 月 5 日 |--- |
| [1\.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1\.0.3](#1.0.3) |2015 年 6 月 4 日 |--- |
| [1\.0.2](#1.0.2) |2015 年 5 月 23 日 |--- |
| [1\.0.1](#1.0.1) |2015 年 5 月 15 日 |--- |
| [1\.0.0](#1.0.0) |2015 年 4 月 8 日 |--- |
| 0.9.4-發行前版本 |2015 年 4 月 6 日 |2016 年 2 月 29 日 |
| 0.9.3-發行前版本 |2015 年 1 月 14 日 |2016 年 2 月 29 日 |
| 0.9.2-發行前版本 |2014 年 12 月 18 日 |2016 年 2 月 29 日 |
| 0.9.1-發行前版本 |2014 年 8 月 22 日 |2016 年 2 月 29 日 |
| 0.9.0-發行前版本 |2014 年 8 月 21 日 |2016 年 2 月 29 日 |

## 常見問題集
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱
若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。

<!---HONumber=AcomDC_0810_2016------>