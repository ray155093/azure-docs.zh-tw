---
title: "Azure 流量管理員 - 常見問題集 | Microsoft Docs"
description: "本文提供 Azure 流量管理員常見問題集的解答。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8c4c8db3cf57537dd77d33b3ded2dc24167f511f
ms.lasthandoff: 03/25/2017

---

# <a name="traffic-manager-frequently-asked-questions-faq"></a>流量管理員常見問題集 (FAQ)

## <a name="traffic-manager-basics"></a>流量管理員基本概念

### <a name="what-ip-address-does-traffic-manager-use"></a>「流量管理員」使用什麼 IP 位址？

如[流量管理員的運作方式](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)所述，流量管理員是在 DNS 層級運作。 它會傳送 DNS 回應，將用戶端導向適當的服務端點。 用戶端會接著直接連線到服務端點，而不會透過「流量管理員」。

因此，「流量管理員」並不提供端點或 IP 位址來供用戶端連線。 因此，如果您的服務需要靜態 IP 位址，則必須在服務上設定，而不是在流量管理員中設定。

### <a name="does-traffic-manager-support-sticky-sessions"></a>流量管理員是否支援「黏性」工作階段？

如[流量管理員的運作方式](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)所述，流量管理員是在 DNS 層級運作。 它會使用 DNS 回應將用戶端導向到適當的服務端點。 用戶端會直接連接至服務端點，而不會透過流量管理員。 因此，流量管理員看不到用戶端與伺服器之間的 HTTP 流量。

此外，流量管理員收到之 DNS 查詢的來源 IP 位址屬於遞迴 DNS 服務，而不是用戶端。 因此，流量管理員無法追蹤個別的用戶端，也就無法實作「黏性」工作階段。 不是只有流量管理員才受此限制，所有 DNS 型流量管理系統都是如此。

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>我在使用流量管理員時為何看到 HTTP 錯誤？

如[流量管理員的運作方式](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)所述，流量管理員是在 DNS 層級運作。 它會使用 DNS 回應將用戶端導向到適當的服務端點。 用戶端會接著直接連線到服務端點，而不會透過「流量管理員」。 流量管理員看不到用戶端與伺服器之間的 HTTP 流量。 因此，您看到的任何 HTTP 錯誤必定來自您的應用程式。 為了讓用戶端連接至應用程式，所有 DNS 解析步驟都已完成。 這包括流量管理員在應用程式流量流程上的任何互動。

因此，進一步的調查應該將焦點放在應用程式上。

從用戶端的瀏覽器傳送的 HTTP 主機標頭是最常見的問題來源。 請確定應用程式已設定為接受您使用之網域名稱的正確主機標頭。 關於使用 Azure App Service 的端點，請參閱[在使用流量管理員的 Azure App Service 中設定 Web 應用程式的自訂網域名稱](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)。

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>使用「流量管理員」對效能有什麼影響？

如[流量管理員的運作方式](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)所述，流量管理員是在 DNS 層級運作。 由於用戶端會直接連線到服務端點，因此在連線建立之後，使用「流量管理員」時並不會造成任何效能影響。

因為流量管理員會在 DNS 層級與應用程式整合，所以它確實需要在 DNS 解析鏈結中插入額外的 DNS 查閱。 「流量管理員」對 DNS 解析時間的影響極小。 流量管理員使用全球網路上的名稱伺服器，並使用[任一傳播](https://en.wikipedia.org/wiki/Anycast)網路功能，以確保一律將 DNS 查詢路由傳送至最靠近的可用名稱伺服器。 此外，快取 DNS 回應意謂著因使用「流量管理員」而造成的額外 DNS 延遲僅適用於一小部分工作階段。

「效能」方法會將流量路由傳送至最靠近的可用端點。 最後結果就是這個方法引起的整體效能影響會降到最低。 增加的任何 DNS 延遲都被端點更短的網路延遲所抵銷。

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>我可以搭配「流量管理員」使用哪些應用程式通訊協定？

如[流量管理員的運作方式](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)所述，流量管理員是在 DNS 層級運作。 完成 DNS 查閱之後，用戶端就會直接連線到應用程式端點，而不會透過「流量管理員」。 因此，連接可以使用任何應用程式通訊協定。 不過，流量管理員的端點健康狀態檢查需要 HTTP 或 HTTPS 端點。 健康狀態檢查的端點可能不是用戶端連接的應用程式端點。

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>我是否可以在流量管理員中使用「裸」網域名稱？

沒有。 DNS 標準不允許 CNAME 與同名的其他 DNS 記錄並存。 DNS 區域的頂點 (或根) 永遠包含兩筆預先存在的 DNS 記錄：SOA 和授權 NS 記錄。 這表示在區域頂點建立 CNAME 記錄就違反 DNS 標準。

流量管理員需要 DNS CNAME 記錄來對應虛名 DNS 名稱。 例如，您將 www.contoso.com 對應至流量管理員設定檔的 DNS 名稱 contoso.trafficmanager.net。 此外，流量管理員設定檔會傳回第二筆 DNS CNAME，以指出用戶端應該連接至哪一個端點。

為了解決此問題，我們建議使用 HTTP 重新導向，將流量從裸網域名稱導向不同的 URL，這樣就可以使用流量管理員。 例如，裸網域 'contoso.com' 可以將使用者重新導向 CNAME 'www.contoso.com'，後者指向流量管理員 DNS 名稱。

我們的功能待處理項目中追蹤了對「流量管理員」中裸網域的完整支援。 您可以[在我們的社群意見反應站投票](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)，以表達您支持這項功能要求。

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>處理 DNS 查詢時，流量管理員會考量用戶端子網路位址嗎？ 
否，目前流量管理員在執行地理和效能路由方法的查閱時，只會考量它所接收到的 DNS 查詢的來源 IP 位址，在大多數案例中，為 DNS 解析程式的 IP 位址。  
具體來說，流量管理員中目前不支援 [RFC 7871 – DNS 查詢中的用戶端子網路 (英文)](https://tools.ietf.org/html/rfc7871)，這提供 [DNS (EDNS0) 的延伸模組機制 (英文)](https://tools.ietf.org/html/rfc2671)，其可將用戶端子網路位址從支援它的解析程式傳遞到 DNS 伺服器。 您可以透過我們的[社群意見反應站 (英文)](https://feedback.azure.com/forums/217313-networking) 來表達您支持這項功能要求。


## <a name="traffic-manager-geographic-traffic-routing-method"></a>流量管理員地理流量路由方法

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>地理路由派上用場的使用案例有哪些？ 
每當 Azure 客戶需要依據地理區域來區別他們的使用者位置時，就可以使用地理路由類型。 例如，使用地理流量路由方法，您可以為特定區域的使用者提供不同於其他區域的使用者體驗。 另一個例子是符合本機資料主權規定，要求特定區域的使用者只能由該區域中的端點提供服務。

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>流量管理員的地理路由支援哪些區域？ 
[這裡](traffic-manager-geographic-regions.md)可以找到流量管理員所使用的國家/區域階層。 雖然此頁面一有變動就會隨時更新，但您也可以透過程式設計方式，利用 [Azure 流量管理員 REST API](https://docs.microsoft.com/rest/api/trafficmanager/) 擷取相同的資訊。 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>流量管理員如何判斷使用者從何處執行查詢？ 
流量管理員會查看查詢的來源 IP (這很可能是代表使用者執行查詢的本機 DNS 解析程式)，並使用內部 IP 至區域的對應來判斷位置。 此對應會融合網際網路上的變動而不斷更新。 

### <a name="is-it-guaranteed-that-traffic-manager-will-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>它保證流量管理員將在每個案例中正確判斷使用者確切的地理位置嗎？
否，基於下列因素，流量管理員不能保證我們從 DNS 查詢的來源 IP 位址推斷的地理區域將會一律對應到使用者的位置： 

- 首先，如先前的常見問題集所述，我們看到的來源 IP 位址是代表使用者執行查閱的 DNS 解析程式的 IP 位址。 儘管 DNS 解析程式的地理位置是使用者地理位置的良好 Proxy，但是，根據 DNS 解析程式服務的使用量和客戶選擇使用的特定 DNS 解析程式服務而定，它也可以不一樣。 例如，位於馬來西亞的客戶可以在他們裝置的設定中指定使用 DNS 解析程式服務，此服務可能會挑選其位於新加坡的 DNS 伺服器來處理該使用者/裝置的查詢解析。 在此情況下，流量管理員只會看到解析程式對應到新加坡位置的 IP 位址。 此外，請參閱此頁面上先前有關用戶端子網路位址支援的常見問題集。

- 其次，流量管理員會使用內部對應來進行 IP 位址到地理區域的轉譯。 雖然此對應已通過驗證並持續更新以提供其精確度並負責網際網路的進化本質，但仍會產生我們的資料無法確切代表所有 IP 位址之地理位址的可能性。


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>端點必須實際位於地理路由設定的相同區域嗎？ 
否，端點的位置不限制哪些區域可以對應它。 例如，美國中部 Azure 區域的端點可以接納所有來自印度的使用者。

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>您可以在未設定為執行地理路由的設定檔中指派地理區域給端點嗎？ 
是，如果設定檔的路由方法不是地理，您可以使用 [Azure 流量管理員 REST API](https://docs.microsoft.com/rest/api/trafficmanager/)，將地理區域指派給該設定檔中的端點。 在非地理路由類型的設定檔中，將忽略此設定。 如果您稍後將此設定檔變更為地理路由類型，流量管理員會使用這些對應。


### <a name="when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic-i-am-getting-an-error"></a>當我嘗試將現有設定檔的路由方法變更為地理時，發生錯誤。
設為地理路由的設定檔下的所有端點，必須至少有一個區域對應到它。 若要將現有的設定檔轉換成地理路由類型，您需要先使用 [Azure 流量管理員 REST API](https://docs.microsoft.com/rest/api/trafficmanager/)，將地理區域和其所有端點相關聯，才能將路由類型變更為地理。 如果使用入口網站，您必須先刪除端點，將設定檔的路由方法變更為地理，然後新增端點及其地理區域對應。 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>為什麼強烈建議客戶建立巢狀設定檔，而不是在啟用地理路由的設定檔下新增端點？ 
在使用地理路由類型的設定檔內，一個區域只能指派給一個端點。 如果該端點不是已連結子設定檔的巢狀類型，萬一該端點變成狀況不良，流量管理員會繼續將流量傳送給它，因為選擇不傳送任何流量也沒有比較好。 即使指派的區域位於指派給狀況不良端點的區域「上層」，流量管理員也不會容錯移轉至另一個端點 (例如，如果端點的西班牙區域狀況不良，我們不會容錯移轉至另一個已被指派歐洲地區的端點)。 這是為了確保流量管理員會顧及客戶在其設定檔中已設定的地理界限。 若要在端點變成狀況不良時能夠容錯移轉至另一個端點，建議將地理區域指派給內有多個端點的巢狀設定檔，而不是指派給個別的端點。 如此一來，如果巢狀子設定檔中的端點失敗，流量就可以容錯移轉至相同巢狀子設定檔內的另一個端點。

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>支援此路由類型的 API 版本有任何限制嗎？

是，只有 2017 年 3 月 1 日的 API 版本和更新版本支援地理路由類型。 任何舊版 API 都無法用來建立地理路由類型的設定檔，或將地理區域指派給端點。 如果使用舊版 API 從 Azure 訂用帳戶擷取設定檔，將不會傳回地理路由類型的任何設定檔。 此外，使用舊版 API 時，傳回的任何設定檔如果有已指派地理區域的端點，則不會顯示其地理區域指派。



## <a name="traffic-manager-endpoints"></a>流量管理員端點

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>可以將流量管理員用於來自多個訂用帳戶的端點嗎？

來自多個訂用帳戶的端點不能與 Azure Web 應用程式搭配使用。 Azure Web Apps 規定 Web Apps 使用的任何自訂網域名稱只能在單一訂用帳戶內使用。 無法使用具有相同網域名稱的多個訂用帳戶中的 Web Apps。

針對其他端點類型，則可以將「流量管理員」與來自多個訂用帳戶的端點搭配使用。 設定流量管理員的方式取決於您是使用傳統部署模型，還是 Resource Manager 經驗。

* 在 Resource Manager 中，來自任何訂用帳戶的端點都可以新增至流量管理員，只要設定流量管理員設定檔的人員具有端點的讀取權限即可。 使用 [Azure Resource Manager 角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md)可以授與這些權限。
* 在傳統部署模型介面中，流量管理員要求任何設定為 Azure 端點的雲端服務或 Web 應用程式，都必須位於與流量管理員設定檔相同的訂用帳戶中。 其他訂用帳戶中的雲端服務端點可以當做「外部」端點新增至流量管理員。 這些外部端點會當成 Azure 端點計費，而不是採用外部費率。

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>我可以使用流量管理員來設定雲端服務「預備」位置嗎？

是。 雲端服務「預備」位置可在流量管理員中設定為外部端點。 健康情況檢查仍然以 Azure 端點費率計費。 因為外部端點類型正在使用中，所以不會自動挑選基礎服務的變更。 使用外部端點時，流量管理員無法偵測雲端服務何時停止或刪除。 因此，流量管理員會持續計費健康情況檢查，直到停用或刪除端點為止。

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>流量管理員是否支援 IPv6 端點？

流量管理員目前不提供 IPv6 定址的名稱伺服器。 不過，連接到 IPv6 端點的 IPv6 用戶端仍然可以使用流量管理員。 用戶端不會直接向流量管理員提出 DNS 要求。 相反地，用戶端會使用遞迴 DNS 服務。 僅支援 IPv6 的用戶端會透過 IPv6 將要求傳送至遞迴 DNS 服務。 然後，遞迴服務應該就能夠使用 IPv4 來連絡流量管理員名稱伺服器。

流量管理員會以端點的 DNS 名稱來回應。 為了支援 IPv6 端點，必須有一筆 DNS AAAA 記錄將端點 DNS 名稱指向 IPv6 位址。 流量管理員健康情況檢查僅支援 IPv4 位址。 此服務必須在相同 DNS 名稱上公開 IPv4 端點。

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>可以使用流量管理員搭配相同區域中的多個 Web 應用程式嗎？

通常，流量管理員用於將流量導向不同區域中部署的應用程式。 不過，也可用於應用程式在相同區域中有多個部署時。 流量管理員 Azure 端點不允許將相同 Azure 區域中的多個 Web 應用程式端點新增至相同的流量管理員設定檔。

下列步驟提供此條件約束的因應措施：

1. 請檢查您的端點位於不同的 Web 應用程式「縮放單位」中。 網域名稱必須對應到指定縮放單位中的單一網站。 因此，相同縮放單位中的兩個 Web Apps 不能共用流量管理員設定檔。
2. 將虛名網域名稱當做自訂主機名稱，新增至每個 Web 應用程式。 每個 Web 應用程式必須在不同的縮放單位中。 所有 Web Apps 都必須屬於相同的訂用帳戶。
3. 將一個 (且只有一個) Web 應用程式端點當做 Azure 端點，新增至流量管理員設定檔。
4. 將每個額外的 Web 應用程式端點新增至流量管理員設定檔，成為外部端點。 只能使用 Resource Manager 部署模型來新增外部端點。
5. 在虛名網域中建立 DNS CNAME 記錄，以指向流量管理員設定檔 DNS 名稱 (<…>.trafficmanager.net)。
6. 透過虛名網域名稱 (而不是流量管理員設定檔 DNS 名稱) 存取您的網站。

##  <a name="traffic-manager-endpoint-monitoring"></a>流量管理員端點監視

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>流量管理員有能力從 Azure 區域失敗中恢復嗎？

流量管理員在 Azure 中是提供高可用性應用程式的一個重要元件。
若要提供高可用性，流量管理員必須擁有極高的可用性，並且可從區域失敗中恢復。

根據設計，流量管理員元件可從任何 Azure 區域的全面失敗中恢復。 此恢復功能適用於所有流量管理員元件︰DNS 名稱伺服器、API、儲存層及端點監視服務。

萬一整個 Azure 區域停機，也能預期流量管理員會持續正常運作。 在多個 Azure 區域中部署的應用程式可依賴流量管理員，將流量導向其應用程式的可用執行個體。

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>資源群組位置的選擇如何影響流量管理員？

流量管理員是單一全域服務。 不是區域性。 如何選擇資源群組位置，對於部署在該資源群組中的流量管理員設定檔而言，並沒有差別。

Azure Resource Manager 需要所有資源群組指定位置，這會決定部署在該資源群組中資源的預設位置。 當您建立流量管理員設定檔時，它會建立在資源群組中。 所有流量管理員設定檔都使用**全域**當作位置，覆寫資源群組預設值。

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>如何判斷每個端點目前的健全狀況？

每個端點的目前監視狀態以及整體設定檔都會顯示於 Azure 入口網站中。 您也可以透過流量監視 [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx)、[PowerShell cmdlets](https://msdn.microsoft.com/library/mt125941.aspx) 和[跨平台 Azure CLI](../cli-install-nodejs.md) 取得此資訊。

Azure 未提供關於過去端點健全狀況的歷程記錄資訊，或針對端點健全狀況的變更來提出警示的能力。

### <a name="can-i-monitor-https-endpoints"></a>我可以監視 HTTPS 端點嗎？

是。 流量管理員支援透過 HTTPS 探查。 在監視組態中將 **HTTPS** 設定為通訊協定。

流量管理員無法提供任何憑證驗證，包括：

* 不會驗證伺服器端憑證
* 不支援 SNI 伺服器端憑證
* 不支援用戶端憑證

### <a name="what-host-header-do-endpoint-health-checks-use"></a>端點健全狀況檢查使用哪一個主機標頭？

流量管理員在 HTTP 和 HTTPS 的健康狀態檢查中使用主機標頭。 流量管理員所使用的主機標頭是設定檔中設定的端點目標名稱。 主機標頭中使用的值不能與目標屬性分開指定。

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>健康情況檢查是從哪些 IP 位址產生？

下列清單包含從流量管理員健康狀態檢查可產生的 IP 位址。 您可以使用此清單來確保端點會允許來自這些 IP 位址的連入連線，以檢查其健康情況狀態。

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

## <a name="traffic-manager-nested-profiles"></a>流量管理員巢狀設定檔

### <a name="how-do-i-configure-nested-profiles"></a>如何設定巢狀設定檔？

您可以使用 Azure Resource Manager 和傳統 Azure REST API、Azure PowerShell Cmdlet 及跨平台 Azure CLI 命令來設定巢狀流量管理員設定檔。 透過新的 Azure 入口網站也支援它們。 傳統入口網站不支援它們。

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>流量管理員支援幾層巢狀結構？

設定檔的巢狀結構深度最多可達 10 層。 不允許使用「迴圈」。

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>在同一個「流量管理員」設定檔中，是否可以將其他端點類型與巢狀子設定檔混合使用？

是。 對於在設定檔內如何結合不同類型的端點，並沒有任何限制。

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>巢狀設定檔如何套用計費模型？

使用巢狀設定檔並沒有計價上的負面影響。

流量管理員計費有兩個要素︰端點健康狀態檢查和數百萬個 DNS 查詢

* 端點健康情況檢查︰當子設定檔被設定為父設定檔中的端點時，並不會針對該子設定檔收費。 監視子設定檔中的端點是以一般方式計費。
* DNS 查詢：每個查詢只計算一次。 查詢父設定檔而從子設定檔傳回端點時，只計入父設定檔內。

如需完整的詳細資料，請參閱[流量管理員定價頁面](https://azure.microsoft.com/pricing/details/traffic-manager/)。

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>巢狀設定檔是否會對效能造成影響？

沒有。 使用巢狀設定檔不會影響效能。

在處理每個 DNS 查詢時，流量管理員名稱伺服器會周遊設定檔階層內部。 父設定檔的 DNS 查詢可能會收到從子設定檔傳回端點的 DNS 回應。 不論您使用單一設定檔或巢狀設定檔，都只會使用單一 CNAME 記錄。 不需要為階層中的每個設定檔建立 CNAME 記錄。

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>流量管理員如何計算父設定檔中的巢狀端點健康狀態？

父設定檔不會直接對子系執行健康狀態檢查， 子設定檔端點的健康狀態會用來計算子設定檔的整體健康狀態。 這項資訊會在巢狀設定檔階層中往上傳播，以判斷巢狀端點的健康狀態。 父設定檔會使用此彙總健康狀態，以決定是否可以將流量導向子設定檔。

下表描述流量管理員檢查巢狀端點健康狀態時的行為。

| 子設定檔監視狀態 | 父端點監視狀態 | 注意事項 |
| --- | --- | --- |
| 已停用。 已停用子設定檔。 |已停止 |父端點狀態為「已停止」，不是「已停用」。 「已停用」狀態會保留，表示您已停用父設定檔中的端點。 |
| 已降級。 至少有一個子設定檔端點的狀態為「已降級」。 |線上︰子設定檔中「線上」端點的數目至少等於 MinChildEndpoints 的值。<BR>CheckingEndpoint︰子設定檔中「線上」加 CheckingEndpoint 端點的數目至少等於 MinChildEndpoints 的值。<BR>已降級︰其他情況。 |系統會將流量傳遞給狀態為 CheckingEndpoint 的端點。 如果 MinChildEndpoints 設定太高，則端點一律會降級。 |
| 線上。 至少有一個子設定檔端點處於「線上」狀態。 沒有端點處於「已降級」狀態。 |請參閱上方。 | |
| CheckingEndpoints。 至少有一個子設定檔端點是 'CheckingEndpoint'。 沒有端點是「線上」或「已降級」。 |同上。 | |
| 非使用中。 所有子設定檔端點不是「已停用」就是「已停止」，或者此設定檔沒有任何端點 |已停止 | |

## <a name="next-steps"></a>後續步驟：
- 深入了解「流量管理員」的 [端點監視和自動容錯移轉](../traffic-manager/traffic-manager-monitoring.md)。
- 深入了解「流量管理員」的 [流量路由方法](../traffic-manager/traffic-manager-routing-methods.md)。
