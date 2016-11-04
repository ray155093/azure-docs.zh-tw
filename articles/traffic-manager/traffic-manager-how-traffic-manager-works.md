---
title: 流量管理員的運作方式 | Microsoft Docs
description: 本文會協助您了解流量管理員的運作方式
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2016
ms.author: sewhee

---
# 流量管理員的運作方式
Azure 流量管理員可讓您控制流量分散到應用程式端點的方式。端點可以是裝載於 Azure 或 Azure 外部的任何網際網路對向端點。

流量管理員提供兩大優點︰

1. 根據幾個[流量路由方法](traffic-manager-routing-methods.md)的其中之一分散流量
2. [連續監視端點健康狀態](traffic-manager-monitoring.md)以及在端點失敗時自動容錯移轉

當使用者嘗試連接到服務端點時，他們的用戶端 (電腦、電話等等) 必須先將該端點的 DNS 名稱解析為 IP 位址。然後用戶端才能連接到該 IP 位址以存取服務。

**最重要的一點是了解流量管理員是在 DNS 層級上運作。** 流量管理員會根據選擇的流量路由方法和目前的端點健康狀態，使用 DNS 將使用者導向至特定的服務端點。然後用戶端就會**直接**連接到所選端點。流量管理員不是 Proxy，看不到在用戶端與服務間傳遞的流量。

## 流量管理員範例
Contoso Corp 開發出新的合作夥伴入口網站。這個入口網站的 URL 會是 https://partners.contoso.com/login.aspx。應用程式裝載在 Azure 中，為改善可用性和全域效能最大化，他們想要將此應用程式部署到全球 3 個區域，並使用流量管理員將使用者分散到距離最近的可用端點。

若要達成這項組態︰

* 他們要部署 3 個服務執行個體。這些部署的 DNS 名稱為 ‘contoso-us.cloudapp.net’、’contoso-eu.cloudapp.net’ 和 ‘contoso-asia.cloudapp.net’。
* 然後他們要建立名為 'contoso.trafficmanager.net' 的流量管理員設定檔，它已設定為在前述 3 個端點之間使用「效能」流量路由方法。
* 最後，他們會使用 DNS CNAME 記錄，將虛名網域 'partners.contoso.com' 設定成指向 'contoso.trafficmanager.net'。

![流量管理員 DNS 組態][1]

> [!NOTE]
> 虛名網域配合 Azure 流量管理員使用時，您必須使用 CNAME 將虛名網域名稱指向流量管理員網域名稱。
> 
> 由於 DNS 標準的限制，CNAME 無法建立在網域的「頂點」(或根)。因此您無法建立 'contoso.com' 的 CNAME (有時稱為「裸」網域)。您可以只為 'contoso.com' 下的網域建立 CNAME，例如 'www.contoso.com'。
> 
> 因此，您無法直接對裸網域使用流量管理員。若要解決這個問題，我們建議您使用簡單的 HTTP 重新導向至替代名稱的 'contoso.com' 直接要求，例如 'www.contoso.com'。
> 
> 

## 用戶端連接如何使用流量管理員
當使用者要求網頁 https://partners.contoso.com/login.aspx 時 (如上例所述)，其用戶端會執行下列步驟來解析 DNS 名稱並建立連接。

![使用流量管理員建立連接][2]

1. 用戶端 (電腦、電話等等) 會對其設定的遞迴 DNS 服務建立 'partners.contoso.com' 的 DNS 查詢。(遞迴 DNS 服務有時稱為「本機 DNS」服務，不直接裝載 DNS 網域。反而是用戶端用它來卸載網際網路所需之各種授權的 DNS 服務聯繫工作，以解析 DNS 名稱。)
2. 遞迴 DNS 服務現在會解析 'partners.contoso.com' DNS 名稱。遞迴 DNS 服務會先找出 'contoso.com' 網域的名稱伺服器。接著連絡這些名稱伺服器，要求 'partners.contoso.com' DNS 記錄。傳回 contoso.trafficmanager.net 的 CNAME。
3. 遞迴 DNS 服務現在會尋找 Azure 流量管理員服務所提供的 'trafficmanager.net' 網域名稱伺服器。連絡這些名稱伺服器，要求 ‘contoso.trafficmanager.net’ DNS 記錄。
4. 流量管理員名稱伺服器接收要求。它們會選擇應該傳回的端點，其依據為︰a.每個端點的啟用/停用狀態 (不傳回停用的端點) b.每個端點目前的健康狀態，由流量管理員健康狀態檢查所決定。如需詳細資訊，請參閱＜流量管理員端點監視＞。c.所選的流量路由方法。如需詳細資訊，請參閱＜流量管理員流量路由方法＞。
5. 所選端點會傳回為另一個 DNS CNAME 記錄，在本例中，讓我們假設傳回的是 contoso-us.cloudapp.net。
6. 遞迴 DNS 服務現在會找出 ‘cloudapp.net’ 網域的名稱伺服器。連絡這些名稱伺服器，要求 ‘contoso-us.cloudapp.net’ DNS 記錄。即傳回包含美國服務端點 IP 位址的 DNS 'A' 記錄。
7. 遞迴 DNS 服務會將上述名稱解析序列的合併結果傳回用戶端。
8. 用戶端收到來自遞迴 DNS 服務的 DNS 結果並連接至指定的 IP 位址。請注意，它會直接連接至應用程式服務端點，不透過流量管理員。因為它是 HTTPS 端點，所以會執行必要的 SSL/TLS 交握，然後提出 ‘/login.aspx’ 頁面的 HTTP GET 要求。

請注意，遞迴 DNS 服務會快取它收到的 DNS 回應，使用者裝置上的 DNS 用戶端也會這樣做。使用快取的資料而不查詢其他名稱伺服器，可以更快回應後續的 DNS 查詢。快取持續時間取決於每個 DNS 記錄的「存留時間」(TTL) 屬性。較短的值會有更快的快取到期，因此出入流量管理員名稱伺服器的次數會更多；較長的值則表示從失敗的端點導出流量需要更長的時間。流量管理員允許您設定流量管理員 DNS 回應中使用的 TTL，讓您選擇最能平衡應用程式需求的值。

## 常見問題集
### 「流量管理員」使用什麼 IP 位址？
如＜流量管理員的運作方式＞所述，「流量管理員」是在 DNS 層級運作。它會使用 DNS 回應將用戶端導向到適當的服務端點。用戶端會接著直接連線到服務端點，而不會透過「流量管理員」。

因此，「流量管理員」並不提供端點或 IP 位址來供用戶端連線。所以，舉例來說，如果需要靜態 IP 位址，就必須在服務設定該位址，而不在「流量管理員」中設定。

### 「流量管理員」是否支援「黏性」工作階段？
如[上面](#how-clients-connect-using-traffic-manager)所述，「流量管理員」是在 DNS 層級上運作。它會使用 DNS 回應將用戶端導向到適當的服務端點。用戶端會接著直接連線到服務端點，而不會透過「流量管理員」。因此，「流量管理員」並看不見用戶端與伺服器之間的 HTTP 流量，包括 Cookie。

此外，請注意，「流量管理員」所收到之 DNS 查詢的來源 IP 位址是遞迴 DNS 服務的 IP 位址，而不是用戶端的 IP 位址。

因此，「流量管理員」無法識別或追蹤個別的用戶端，也就無法實作「黏性」工作階段。這對所有 DNS 型流量管理系統來說很常見，並不是因使用「流量管理員」而產生的限制。

### 我在使用「流量管理員」時看到 HTTP 錯誤，為什麼？
如[上面](#how-clients-connect-using-traffic-manager)所述，「流量管理員」是在 DNS 層級上運作。它會使用 DNS 回應將用戶端導向到適當的服務端點。用戶端會接著直接連線到服務端點，而不會透過「流量管理員」。

因此，「流量管理員」並看不見用戶端與伺服器之間的 HTTP 流量，也就無法產生 HTTP 層級的錯誤。您看到的任何 HTTP 錯誤必定來自您的應用程式。因為用戶端是連線到應用程式，所以這也意謂著包括「流量管理員」角色在內的 DNS 解析必定已經完成。

因此，進一步的調查應該將焦點放在應用程式上。

一個常見的問題是使用「流量管理員」時，瀏覽器傳遞給應用程式的「主機」HTTP 標頭會顯示瀏覽器中使用的網域名稱。如果您在測試期間使用「流量管理員」網域名稱 (例如 myprofile.trafficmanager.net)，這就可能是該網域名稱，或者也可能是設定為指向「流量管理員」網域名稱的虛名網域 CNAME。不論是上述哪一種情況，都請確認應用程式已設定為接受此主機標頭。

如果您的應用程式是裝載在 Azure App Service 中，請參閱[在使用流量管理員的 Azure App Service 中設定 Web 應用程式的自訂網域名稱](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)。

### 使用「流量管理員」對效能有什麼影響？
如[上面](#how-clients-connect-using-traffic-manager)所述，「流量管理員」是在 DNS 層級上運作。它會使用 DNS 回應將用戶端導向到適當的服務端點。用戶端會接著直接連線到服務端點，而不會透過「流量管理員」。

由於用戶端會直接連線到服務端點，因此在連線建立之後，使用「流量管理員」時並不會造成任何效能影響。

因為「流量管理員」會在 DNS 層級與應用程式整合，所以它確實需要在 DNS 解析鏈結中插入額外的 DNS 查閱 (請參閱 [流量管理員範例](#traffic-manager-example))。「流量管理員」對 DNS 解析時間的影響極小。「流量管理員」會使用名稱伺服器全球網路，以及使用任一傳播網路功能，來確保一律會將 DNS 查詢傳遞至最靠近的可用名稱伺服器。此外，快取 DNS 回應意謂著因使用「流量管理員」而造成的額外 DNS 延遲僅適用於一小部分工作階段。

最後結果就是與將「流量管理員」併入您應用程式中關聯的整體效能影響應該極小。

此外，在使用「流量管理員」的[「效能」流量路由方法](traffic-manager-routing-methods.md#performance-traffic-routing-method)的情況下，所增加的 DNS 延遲應該遠大於透過將使用者傳遞至其最靠近之可用端點來達成的效能改進幅度。

### 我可以搭配「流量管理員」使用哪些應用程式通訊協定？
如[上面](#how-clients-connect-using-traffic-manager)所述，「流量管理員」是在 DNS 層級上運作。完成 DNS 查閱之後，用戶端就會直接連線到應用程式端點，而不會透過「流量管理員」。因此，此連線可以使用任何應用程式通訊協定。

不過，「流量管理員」的端點健全狀況檢查會需要 HTTP 或 HTTPS 端點。這可以與用戶端所連線的應用程式端點分開，方法是在「流量管理員」設定檔健全狀況檢查設定中指定不同的 TCP 連接埠或 URI 路徑。

### 我是否可以使用「流量管理員」搭配「裸」(無 www) 網域名稱？
目前不支援。

DNS CNAME 記錄類型是用來建立從一個 DNS 名稱到另一個名稱的對應。如[流量管理員範例](#traffic-manager-example)所述，「流量管理員」必須要有 DNS CNAME 記錄，才能將虛名 DNS 名稱 (例如 www.contoso.com) 對應到「流量管理員」設定檔 DNS 名稱 (例如 contoso.trafficmanager.net)。此外，「流量管理員」設定檔本身會傳回第二個 DNS CNAME，以指出用戶端應該連線到哪一個端點。

DNS 標準並不允許 CNAME 與其他相同類型的 DNS 記錄並存。由於 DNS 區域的頂點 (或根) 一律會包含兩個預先存在的 DNS 記錄 (SOA 和權威性 NS 記錄)，因此這意謂著無法在不違反 DNS 標準的情況下，在區域頂點建立 CNAME 記錄。

為了解決此問題，我們建議使用裸 (無 www) 網域且想要使用「流量管理員」的服務應該使用 HTTP 重新導向，將流量從裸網域導向到不同的 URL，然後便可藉此使用「流量管理員」。例如，裸網域 ‘contoso.com’ 可以將使用者重新導向到 ‘www.contoso.com’，然後便可藉此使用「流量管理員」。

我們的功能待處理項目中追蹤了對「流量管理員」中裸網域的完整支援。如果您對此功能感興趣，請[在我們的社群意見反應網站投票給它](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)來登錄您的支持。

## 後續步驟
深入了解「流量管理員」的[端點監視和自動容錯移轉](traffic-manager-monitoring.md)。

深入了解「流量管理員」的[流量路由方法](traffic-manager-routing-methods.md)。

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0824_2016-->