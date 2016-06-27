<properties
   pageTitle="使用 DocumentDB 全球發佈資料 | Microsoft Azure"
   description="了解如何從 Azure DocumentDB (可完全管理的 NoSQL 資料庫服務)，使用全域資料庫進行全球規模的異地複寫、容錯移轉及資料復原。"
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/14/2016"
   ms.author="kipandya"/>
   
   
# 使用 DocumentDB 全球發佈資料

Azure DocumentDB 的設計目的是符合 IoT 應用程式的需求，這類應用程式是由數百萬個全球發佈裝置和網際網路規模的應用程式所組成，其可為世界各地的使用者提供高度回應性體驗。這些資料庫系統所面臨的挑戰是，使用定義完善的資料一致性和可用性保證，達成從多個地理區域低延遲存取應用程式資料的目標。做為全球發佈資料庫系統，DocumentDB 會簡化資料的全球發佈作業，方法是提供可完全管理的多個區域資料庫帳戶，在一致性、可用性和效能之間進行明確取捨，這一切全都倚靠相對應的保證來完成。DocumentDB 資料庫帳戶是透過下列方式來提供：高可用性、單一數字毫秒延遲、多個[定義完善的一致性層級][consistency]、利用多首頁 API 進行透明的區域性容錯移轉，並且能夠彈性調整世界各地的輸送量和儲存體。

  
## 設定多個區域帳戶

透過 Azure 入口網站，將您的 DocumentDB 帳戶範圍設定為涵蓋全球，不到 1 分鐘就能完成。您只需在數個支援且定義完善的一致性層級之間選取正確的一致性層級，並將任意數目的 Azure 區域關聯至您的資料庫帳戶。DocumentDB 一致性層級可讓您在特定的一致性保證與效能之間做出明確的取捨。

![DocumentDB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇][1]

DocumentDB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇。

根據您應用程式所需的資料一致性保證，選取正確的一致性層級。DocumentDB 會自動跨所有特定區域複寫您的資料，並保證您針對資料庫帳戶選取的一致性。


## 使用多個區域容錯移轉 

Azure DocumentDB 能夠以透明方式在多個 Azure 區域之間容錯移轉資料庫帳戶 – 新的[多首頁 API][developingwithmultipleregions] 保證您的應用程式可以繼續使用邏輯端點，並可透過容錯移轉使其不會中斷。容錯移轉是由您所控制，在發生任何範圍的可能失敗狀況時提供彈性來重新隸屬您的資料庫帳戶，包括應用程式、基礎結構、服務或區域性失敗 (實際或模擬)。如果發生 DocumentDB 區域性失敗，服務將會以透明方式容錯移轉您的資料庫帳戶，而您的應用程式可繼續存取資料，而不會遺失可用性。儘管 DocumentDB 提供 [99\.99% 的可用性 SLA][sla]，您還是可以測試應用程式的端對端可用性屬性，方法是[以程式設計方式][arm]以及透過 Azure 入口網站來模擬區域性失敗。


## 設定涵蓋全球的範圍
DocumentDB 可讓您針對每個 DocumentDB 集合單獨佈建任意規模的輸送量和取用儲存體，這會全面橫跨與您資料庫帳戶相關的所有區域。DocumentDB 集合是全球自動發佈，並且在與您資料庫帳戶相關聯的所有區域中進行管理。資料庫帳戶內的集合可以在任何[提供 DocumentDB 服務][serviceregions]的 Azure 區域上進行發佈。

針對每個 DocumentDB 集合購買的輸送量和所取用的儲存體會跨所有區域自動平均佈建。這可讓您的應用程式可在全球各地順暢地調整規模，[只需支付您每小時內所使用的儲存體與輸送量][pricing]。比方說，如果您已針對 DocumentDB 集合佈建 2 百萬個 RU，則每個與您資料庫帳戶相關聯的區域會針對該集合取得 2 百萬個 RU。請見下圖說明。

![針對跨越四個區域的 DocumentDB 集合調整輸送量][2]

DocumentDB 在 P99 上保證 < 10 毫秒的讀取延遲和 < 15 毫秒的寫入延遲。讀取要求絕對不會跨越資料中心的界限，以保證[您所選取的一致性需求][consistency]。寫入一律會在其認可至用戶端之前在本機認可仲裁。每個資料庫帳戶都是利用寫入區域優先順序來設定。已指定高優先順序的區域將用來做為帳戶的目前寫入區域。所有 SDK 都會以透明方式將資料庫帳戶寫入路由傳送到目前的寫入區域。

最後，由於 DocumentDB 完全[無從驗證結構描述][vldb]，因此，您永遠不必擔心要跨越多個資料中心管理/更新結構描述或次要索引。當您的應用程式和資料模型繼續進化時，您的 [SQL 查詢][sqlqueries]會繼續運作。


## 啟用全球發佈 

您可以決定要透過將一或多個 Azure 區域關聯至 DocumentDB 資料庫帳戶，在本機或全球發佈您的資料。您隨時都能藉由將區域新增至您的資料庫帳戶或從中移除，來決定要全球發佈資料，或將它侷限於單一區域。支援多個區域指派的 DocumentDB 資料庫帳戶可以透過 Azure Marketplace，藉由選取 [DocumentDB - 多個區域資料庫帳戶] 來建立。



## 後續步驟

深入了解下列文章中使用 DocumentDB 全球發佈資料的相關資訊：

* [針對集合佈建輸送量和儲存體][throughputandstorage]
* [透過 REST、.NET、Java、Python 及 Node SDK 的多首頁 API][developingwithmultipleregions]
* [DocumentDB 中的一致性層級][consistency]
* [可用性 SLA][sla]
* [管理資料庫帳戶][manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: https://azure.microsoft.com/documentation/articles/documentdb-partition-data/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: https://azure.microsoft.com/documentation/articles/documentdb-developing-with-multiple-regions/
[createaccount]: https://azure.microsoft.com/documentation/articles/documentdb-create-account/
[manageaccount]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/
[manageaccount-consistency]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#consistency
[manageaccount-addregion]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#addregion
[throughputandstorage]: https://azure.microsoft.com/documentation/articles/documentdb-manage/
[arm]: https://azure.microsoft.com/documentation/articles/documentdb-automation-resource-manager-cli/
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/regions/#services
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: https://azure.microsoft.com/documentation/articles/documentdb-sql-query/

<!---HONumber=AcomDC_0615_2016-->