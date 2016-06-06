<properties
   pageTitle="流量管理員的運作方式 | Microsoft Azure"
   description="本文會協助您了解流量管理員的運作方式"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jonatul"/>

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

- 他們要部署 3 個服務執行個體。這些部署的 DNS 名稱為 ‘contoso-us.cloudapp.net’、’contoso-eu.cloudapp.net’ 和 ‘contoso-asia.cloudapp.net’。
- 然後他們要建立名為 'contoso.trafficmanager.net' 的流量管理員設定檔，它已設定為在前述 3 個端點之間使用「效能」流量路由方法。
- 最後，他們會使用 DNS CNAME 記錄，將虛名網域 'partners.contoso.com' 設定成指向 'contoso.trafficmanager.net'。

![流量管理員 DNS 組態][1]

> [AZURE.NOTE] 虛名網域配合 Azure 流量管理員使用時，您必須使用 CNAME 將虛名網域名稱指向流量管理員網域名稱。

> 由於 DNS 標準的限制，CNAME 無法建立在網域的「頂點」(或根)。因此您無法建立 'contoso.com' 的 CNAME (有時稱為「裸」網域)。您可以只為 'contoso.com' 下的網域建立 CNAME，例如 'www.contoso.com'。

> 因此，您無法直接對裸網域使用流量管理員。若要解決這個問題，我們建議您使用簡單的 HTTP 重新導向至替代名稱的 'contoso.com' 直接要求，例如 'www.contoso.com'。

## 用戶端連接如何使用流量管理員

當使用者要求網頁 https://partners.contoso.com/login.aspx 時 (如上例所述)，其用戶端會執行下列步驟來解析 DNS 名稱並建立連接。

![使用流量管理員建立連接][2]

1.	用戶端 (電腦、電話等等) 會對其設定的遞迴 DNS 服務建立 'partners.contoso.com' 的 DNS 查詢。(遞迴 DNS 服務有時稱為「本機 DNS」服務，不直接裝載 DNS 網域。反而是用戶端用它來卸載網際網路所需之各種授權的 DNS 服務聯繫工作，以解析 DNS 名稱。)
2.	遞迴 DNS 服務現在會解析 'partners.contoso.com' DNS 名稱。遞迴 DNS 服務會先找出 'contoso.com' 網域的名稱伺服器。接著連絡這些名稱伺服器，要求 'partners.contoso.com' DNS 記錄。傳回 contoso.trafficmanager.net 的 CNAME。
3.	遞迴 DNS 服務現在會尋找 Azure 流量管理員服務所提供的 'trafficmanager.net' 網域名稱伺服器。連絡這些名稱伺服器，要求 ‘contoso.trafficmanager.net’ DNS 記錄。
4.	流量管理員名稱伺服器接收要求。它們會選擇應該傳回的端點，其依據為︰a.每個端點的啟用/停用狀態 (不傳回停用的端點) b.每個端點目前的健康狀態，由流量管理員健康狀態檢查所決定。如需詳細資訊，請參閱＜流量管理員端點監視＞。c.所選的流量路由方法。如需詳細資訊，請參閱＜流量管理員流量路由方法＞。
5.	所選端點會傳回為另一個 DNS CNAME 記錄，在本例中，讓我們假設傳回的是 contoso-us.cloudapp.net。
6.	遞迴 DNS 服務現在會找出 ‘cloudapp.net’ 網域的名稱伺服器。連絡這些名稱伺服器，要求 ‘contoso-us.cloudapp.net’ DNS 記錄。即傳回包含美國服務端點 IP 位址的 DNS 'A' 記錄。
7.	遞迴 DNS 服務會將上述名稱解析序列的合併結果傳回用戶端。
8.	用戶端收到來自遞迴 DNS 服務的 DNS 結果並連接至指定的 IP 位址。請注意，它會直接連接至應用程式服務端點，不透過流量管理員。因為它是 HTTPS 端點，所以會執行必要的 SSL/TLS 交握，然後提出 ‘/login.aspx’ 頁面的 HTTP GET 要求。

請注意，遞迴 DNS 服務會快取它收到的 DNS 回應，使用者裝置上的 DNS 用戶端也會這樣做。使用快取的資料而不查詢其他名稱伺服器，可以更快回應後續的 DNS 查詢。快取持續時間取決於每個 DNS 記錄的「存留時間」(TTL) 屬性。較短的值會有更快的快取到期，因此出入流量管理員名稱伺服器的次數會更多；較長的值則表示從失敗的端點導出流量需要更長的時間。流量管理員允許您設定流量管理員 DNS 回應中使用的 TTL，讓您選擇最能平衡應用程式需求的值。

## 後續步驟

深入了解流量管理員的[端點監視和自動容錯移轉](traffic-manager-monitoring.md)。

深入了解流量管理員的[流量路由方法](traffic-manager-routing-methods.md)。

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0525_2016-->