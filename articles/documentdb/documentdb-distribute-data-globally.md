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
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# 使用 DocumentDB 全球發佈資料

> [AZURE.NOTE] 對於任何新建的 DocumentDB 帳戶，通常可以使用 DocumentDB 資料庫的全球發佈，而且會自動啟用。我們正致力於啟用所有現有帳戶的全球發佈，但如果您想在這過渡時期啟用您帳戶的全球發佈，請[連絡客戶支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，我們將立即為您啟用。

Azure DocumentDB 的設計目的是符合 IoT 應用程式的需求，這類應用程式是由數百萬個全球發佈裝置和網際網路規模的應用程式所組成，其可為世界各地的使用者提供高度回應性體驗。如何使用定義完善的資料一致性和可用性保證，來為多個地理區域達成低延遲存取應用程式資料的目標，正是這些資料庫系統所面臨的挑戰。作為全球發佈資料庫系統，DocumentDB 會簡化資料的全球發佈作業，方法是提供多個可完全管理的區域資料庫帳戶，在一致性、可用性和效能之間進行明確取捨，這一切全都倚靠相對應的保證來完成。DocumentDB 資料庫帳戶提供下列優點：高可用性、個位數的毫秒延遲、多個[定義完善的一致性層級][consistency]、利用多首頁 API 透明進行的區域性容錯移轉，以及全球輸送量及儲存體的彈性調整能力。

  
## 設定多個區域帳戶

透過 [Azure 入口網站](documentdb-portal-global-replication.md)，不到 1 分鐘就能將您的 DocumentDB 帳戶範圍設定為涵蓋全球。您只需在數個支援且定義完善的一致性層級之間選取正確的一致性層級，並將任意數目的 Azure 區域關聯至您的資料庫帳戶。DocumentDB 一致性層級可讓您在特定的一致性保證與效能之間做出明確的取捨。

![DocumentDB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇][1]

DocumentDB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇。

根據您應用程式所需的資料一致性保證，選取正確的一致性層級。DocumentDB 會自動跨所有特定區域複寫您的資料，並保證您針對資料庫帳戶選取的一致性。


## 使用多個區域容錯移轉 

Azure DocumentDB 能夠透明地在多個 Azure 區域之間為資料庫帳戶進行容錯移轉 – 新的[多首頁 API][developingwithmultipleregions] 能保證您的應用程式可以繼續使用邏輯端點，並可透過容錯移轉使其不會中斷。容錯移轉是由您所控制，在發生任何範圍的可能失敗狀況時提供彈性來重新隸屬您的資料庫帳戶，包括應用程式、基礎結構、服務或區域性失敗 (實際或模擬)。如果發生 DocumentDB 區域性失敗，服務將會透明地為您的資料庫帳戶進行容錯移轉，而您的應用程式可繼續存取資料，而不會遺失可用性。儘管 DocumentDB 提供 [99\.99% 的可用性 SLA][sla]，您還是可以測試應用程式的端對端可用性屬性，透過[程式設計][arm]或透過 Azure 入口網站兩種方法來模擬區域性失敗。


## 設定涵蓋全球的範圍
DocumentDB 可讓您針對每個 DocumentDB 集合單獨佈建任意規模的輸送量和取用儲存體，這會全面橫跨與您資料庫帳戶相關的所有區域。DocumentDB 集合是全球自動發佈，並且在與您資料庫帳戶相關聯的所有區域中進行管理。資料庫帳戶內的集合可以在任何[提供 DocumentDB 服務][serviceregions]的 Azure 區域上進行發佈。

針對每個 DocumentDB 集合購買的輸送量和所取用的儲存體會跨所有區域自動平均佈建。這可讓您的應用程式可在全球各地順暢地調整規模，[只需支付您每小時內所使用的儲存體與輸送量][pricing]。比方說，如果您已針對 DocumentDB 集合佈建 2 百萬個 RU，則每個與您資料庫帳戶有關聯的區域都會針對該集合取得 2 百萬個 RU。請見下圖說明。

![針對跨越四個區域的 DocumentDB 集合調整輸送量][2]

DocumentDB 在 P99 上保證 < 10 毫秒的讀取延遲和 < 15 毫秒的寫入延遲。讀取要求絕對不會跨越資料中心的界限，以保證[您所選取的一致性需求][consistency]。寫入一律會在其認可至用戶端之前在本機認可仲裁。每個資料庫帳戶都是利用寫入區域優先順序來設定。已指定高優先順序的區域將用來做為帳戶的目前寫入區域。所有 SDK 都會透明地將資料庫帳戶寫入路由傳送到目前的寫入區域。

最後，由於 DocumentDB 完全[無從驗證結構描述][vldb]，因此，您永遠不必擔心要跨越多個資料中心管理/更新結構描述或次要索引。當您的應用程式和資料模型繼續進化時，您的 [SQL 查詢][sqlqueries]會繼續運作。


## 啟用全球發佈 

您可以決定要透過將一或多個 Azure 區域關聯至 DocumentDB 資料庫帳戶，在本機或全球發佈您的資料。您可以隨時新增或移除資料庫帳戶的區域。若要使用入口網站進行全球發佈，請參閱[如何使用 Azure 入口網站執行 DocumentDB 全球資料庫複寫](documentdb-portal-global-replication.md)。若要以程式設計方式執行全球發佈，請參閱[使用多個區域 DocumentDB 帳戶進行開發](documentdb-developing-with-multiple-regions.md)。

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
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/zh-TW/regions/#services
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md

<!---HONumber=AcomDC_0817_2016-->