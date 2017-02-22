---
title: "流量管理員的運作方式 | Microsoft Docs"
description: "這篇文章說明 Azure 流量管理員的運作方式"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: a6c9370d-e60d-440f-aa82-b6d3fa5416b0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 6ba1732840d094497a9e2cb7a7ea7825a7f4bc89

---

# <a name="how-traffic-manager-works"></a>流量管理員的運作方式

Azure 流量管理員可讓您控制流量分散到應用程式端點的方式。 端點是裝載於 Azure 內部或外部的任何網際網路對向服務。

流量管理員提供兩大優點︰

1. 根據幾個 [流量路由方法](traffic-manager-routing-methods.md)
2. [連續監視端點健康狀態](traffic-manager-monitoring.md) 以及在端點失敗時自動容錯移轉

當用戶端嘗試連接至服務時，它必須先將服務的 DNS 名稱解析為 IP 位址。 然後用戶端才能連接到該 IP 位址以存取服務。

**最重要的一點是了解流量管理員是在 DNS 層級上運作。**  流量管理員會使用 DNS，根據流量路由方法的規則，將用戶端導向特定的服務端點。 用戶端會**直接**連接至選取的端點。 流量管理員不是 Proxy 或閘道。 流量管理員看不到在用戶端與服務之間傳遞的流量。

## <a name="traffic-manager-example"></a>流量管理員範例

Contoso Corp 開發出新的合作夥伴入口網站。 此入口網站的 URL 是 https://partners.contoso.com/login.aspx。 應用程式裝載於 Azure 的三個區域。 為了改善可用性和最佳化全域效能，他們使用流量管理員將用戶端流量分配給最靠近的可用端點。

若要達成這項組態︰

* 他們部署三個服務執行個體。 這些部署的 DNS 名稱為 'contoso-us.cloudapp.net'、'contoso-eu.cloudapp.net' 和 'contoso-asia.cloudapp.net'。
* 然後，他們建立名為 'contoso.trafficmanager.net' 的流量管理員設定檔，並設定為在這三個端點之間使用「效能」流量路由方法。
* 最後，他們使用 DNS CNAME 記錄，將虛名網域名稱 'partners.contoso.com' 設定成指向 'contoso.trafficmanager.net'。

![流量管理員 DNS 組態][1]

> [!NOTE]
> 虛名網域配合 Azure 流量管理員使用時，您必須使用 CNAME 將虛名網域名稱指向流量管理員網域名稱。 DNS 標準不允許您在網域的「頂點」(或根) 上建立 CNAME。 因此，您無法建立 'contoso.com' 的 CNAME (有時稱為「裸」網域)。 您只能為 'contoso.com' 下的網域建立 CNAME，例如 'www.contoso.com'。 為了克服這項限制，我們建議使用簡單的 HTTP 重新導向，將 'contoso.com' 的要求導向替代名稱，例如 'www.contoso.com'。

## <a name="how-clients-connect-using-traffic-manager"></a>用戶端連接如何使用流量管理員

接續上述範例，當用戶端要求頁面 https://partners.contoso.com/login.aspx 時，用戶端會執行下列步驟來解析 DNS 名稱，然後建立連接︰

![使用流量管理員建立連接][2]

1. 用戶端會將 DNS 查詢傳送至已設定的遞迴 DNS 服務，以解析名稱 'partners.contoso.com'。 遞迴 DNS 服務 (有時稱為「本機 DNS」服務) 不直接裝載 DNS 網域。 相反地，用戶端不負責連絡網際網路上解析 DNS 名稱所需的各種授權 DNS 服務。
2. 為了解析 DNS 名稱，遞迴 DNS 服務會尋找 'contoso.com' 網域的名稱伺服器。 然後，連絡這些名稱伺服器來要求 'partners.contoso.com' DNS 記錄。 Contoso.com DNS 伺服器會傳回指向 contoso.trafficmanager.net 的 CNAME 記錄。
3. 接下來，遞迴 DNS 服務會尋找 'trafficmanager.net' 網域的名稱伺服器 (由 Azure 流量管理員服務提供)。 然後，將 'contoso.trafficmanager.net' DNS 記錄的要求傳送至這些 DNS 伺服器。
4. 流量管理員名稱伺服器接收要求。 它們會根據下列項目來選擇端點︰

    - 每個端點已設定的狀態 (不會傳回已停用的端點)
    - 每個端點目前的健康狀態，由流量管理員健康狀態檢查所決定。 如需詳細資訊，請參閱 [流量管理員端點監視](traffic-manager-monitoring.md)。
    - 所選的流量路由方法。 如需詳細資訊，請參閱[流量管理員路由方法](traffic-manager-routing-methods.md)。

5. 選擇的端點會傳回成為另一筆 DNS CNAME 記錄。 在此例子中，我們假設傳回 contoso us.cloudapp.net。
6. 接下來，遞迴 DNS 服務會尋找 'cloudapp.net' 網域的名稱伺服器。 它會連絡這些名稱伺服器，以要求 'contoso-us.cloudapp.net' DNS 記錄。 將會傳回一筆 DNS 'A' 記錄，內含美國地區服務端點的 IP 位址。
7. 遞迴 DNS 服務會合併結果，並傳回單一 DNS 回應給用戶端。
8. 用戶端收到 DNS 結果，然後連接至指定的 IP 位址。 用戶端會直接連接至應用程式服務端點，而不透過流量管理員。 因為它是 HTTPS 端點，用戶端會執行必要的 SSL/TLS 交握，然後提出 '/login.aspx' 頁面的 HTTP GET 要求。

遞迴 DNS 服務會快取它收到的 DNS 回應。 用戶端裝置上的 DNS 解析程式也會快取結果。 快取功能會使用快取中的資料，而不查詢其他名稱伺服器，因而可以更快回應後續的 DNS 查詢。 快取持續時間取決於每一筆 DNS 記錄的「存留時間」(TTL) 屬性。 較短的值會導致快取更快到期，因此需要更多次往返於流量管理員的名稱伺服器。 較長的值表示從失敗端點引開流量會花費更長的時間。 流量管理員允許您設定流量管理員 DNS 回應中使用的 TTL，讓您選擇最能平衡應用程式需求的值。

## <a name="faq"></a>常見問題集

### <a name="what-ip-address-does-traffic-manager-use"></a>「流量管理員」使用什麼 IP 位址？

如＜流量管理員的運作方式＞所述，「流量管理員」是在 DNS 層級運作。 它會傳送 DNS 回應，將用戶端導向適當的服務端點。 用戶端會接著直接連線到服務端點，而不會透過「流量管理員」。

因此，「流量管理員」並不提供端點或 IP 位址來供用戶端連線。 因此，如果您的服務需要靜態 IP 位址，則必須在服務上設定，而不是在流量管理員中設定。

### <a name="does-traffic-manager-support-sticky-sessions"></a>流量管理員是否支援「黏性」工作階段？

如[先前](#how-clients-connect-using-traffic-manager)所述，流量管理員是在 DNS 層級上運作。 它會使用 DNS 回應將用戶端導向到適當的服務端點。 用戶端會直接連接至服務端點，而不會透過流量管理員。 因此，流量管理員看不到用戶端與伺服器之間的 HTTP 流量。

此外，流量管理員收到之 DNS 查詢的來源 IP 位址屬於遞迴 DNS 服務，而不是用戶端。 因此，流量管理員無法追蹤個別的用戶端，也就無法實作「黏性」工作階段。 不是只有流量管理員才受此限制，所有 DNS 型流量管理系統都是如此。

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>我在使用流量管理員時為何看到 HTTP 錯誤？

如[先前](#how-clients-connect-using-traffic-manager)所述，流量管理員是在 DNS 層級上運作。 它會使用 DNS 回應將用戶端導向到適當的服務端點。 用戶端會接著直接連線到服務端點，而不會透過「流量管理員」。 流量管理員看不到用戶端與伺服器之間的 HTTP 流量。 因此，您看到的任何 HTTP 錯誤必定來自您的應用程式。 為了讓用戶端連接至應用程式，所有 DNS 解析步驟都已完成。 這包括流量管理員在應用程式流量流程上的任何互動。

因此，進一步的調查應該將焦點放在應用程式上。

從用戶端的瀏覽器傳送的 HTTP 主機標頭是最常見的問題來源。 請確定應用程式已設定為接受您使用之網域名稱的正確主機標頭。 關於使用 Azure App Service 的端點，請參閱[在使用流量管理員的 Azure App Service 中設定 Web 應用程式的自訂網域名稱](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)。

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>使用「流量管理員」對效能有什麼影響？

如[先前](#how-clients-connect-using-traffic-manager)所述，流量管理員是在 DNS 層級上運作。 由於用戶端會直接連線到服務端點，因此在連線建立之後，使用「流量管理員」時並不會造成任何效能影響。

因為「流量管理員」會在 DNS 層級與應用程式整合，所以它確實需要在 DNS 解析鏈結中插入額外的 DNS 查閱 (請參閱 [流量管理員範例](#traffic-manager-example))。 「流量管理員」對 DNS 解析時間的影響極小。 流量管理員使用全球網路上的名稱伺服器，並使用[任一傳播](https://en.wikipedia.org/wiki/Anycast)網路功能，以確保一律將 DNS 查詢路由傳送至最靠近的可用名稱伺服器。 此外，快取 DNS 回應意謂著因使用「流量管理員」而造成的額外 DNS 延遲僅適用於一小部分工作階段。

「效能」方法會將流量路由傳送至最靠近的可用端點。 最後結果就是這個方法引起的整體效能影響會降到最低。 增加的任何 DNS 延遲都被端點更短的網路延遲所抵銷。

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>我可以搭配「流量管理員」使用哪些應用程式通訊協定？

如[先前](#how-clients-connect-using-traffic-manager)所述，流量管理員是在 DNS 層級上運作。 完成 DNS 查閱之後，用戶端就會直接連線到應用程式端點，而不會透過「流量管理員」。 因此，連接可以使用任何應用程式通訊協定。 不過，流量管理員的端點健康狀態檢查需要 HTTP 或 HTTPS 端點。 健康狀態檢查的端點可能不是用戶端連接的應用程式端點。

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>我是否可以在流量管理員中使用「裸」網域名稱？

沒有。 DNS 標準不允許 CNAME 與同名的其他 DNS 記錄並存。 DNS 區域的頂點 (或根) 永遠包含兩筆預先存在的 DNS 記錄：SOA 和授權 NS 記錄。 這表示在區域頂點建立 CNAME 記錄就違反 DNS 標準。

如[流量管理員範例](#traffic-manager-example)中所述，流量管理員需要 DNS CNAME 記錄來對應虛名 DNS 名稱。 例如，您將 www.contoso.com 對應至流量管理員設定檔的 DNS 名稱 contoso.trafficmanager.net。 此外，流量管理員設定檔會傳回第二筆 DNS CNAME，以指出用戶端應該連接至哪一個端點。

為了解決此問題，我們建議使用 HTTP 重新導向，將流量從裸網域名稱導向不同的 URL，這樣就可以使用流量管理員。 例如，裸網域 'contoso.com' 可以將使用者重新導向 CNAME 'www.contoso.com'，後者指向流量管理員 DNS 名稱。

我們的功能待處理項目中追蹤了對「流量管理員」中裸網域的完整支援。 您可以[在我們的社群意見反應站投票](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)，以表達您支持這項功能要求。

## <a name="next-steps"></a>後續步驟

深入了解「流量管理員」的 [端點監視和自動容錯移轉](traffic-manager-monitoring.md)。

深入了解「流量管理員」的 [流量路由方法](traffic-manager-routing-methods.md)。

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png



<!--HONumber=Nov16_HO5-->


