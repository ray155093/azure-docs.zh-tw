---
title: "巢狀流量管理員設定檔 | Microsoft Docs"
description: "這篇文章說明「Azure 流量管理員」的「巢狀設定檔」功能"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 3e48a28aa1ecda6792e79646a33875c8f01a878f
ms.openlocfilehash: fdf22a3f8d0ba6f1838af4f5e6924c8c0a18ef64

---

# <a name="nested-traffic-manager-profiles"></a>巢狀流量管理員設定檔

流量管理員包括一系列流量路由方法，可讓您控制流量管理員如何選擇哪一個端點應接收來自每一位使用者的流量。 如需詳細資訊，請參閱 [流量管理員流量路由方法](traffic-manager-routing-methods.md)。

每個「流量管理員」設定檔皆指定一個流量路由方法。 不過，有些情況需要的流量路由，比單一流量管理員設定檔所提供的路由更複雜。 您可以將流量管理員設定檔巢狀化，以結合多個流量路由方法的優點。 巢狀設定檔可讓您覆寫預設流量管理員行為，以支援更大和更複雜的應用程式部署。

下列範例說明如何在各種情況中使用巢狀流量管理員設定檔。

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>範例 1︰結合「效能」和「加權」流量路由

假設您將應用程式部署在下列 Azure 區域︰美國西部、西歐及東亞。 您使用流量管理員的「效能」流量路由方法，將流量分配給最靠近使用者的區域。

![單一流量管理員設定檔][1]

現在，假設您希望先測試服務的更新，再更廣泛地推出。 您想要使用「加權」流量路由方法，將一小部分的流量導向您的測試部署。 除了西歐現有的生產環境部署，您還設定測試部署。

您無法在單一設定檔中結合「加權」和「效能」流量路由。 為了支援這種情況，您使用兩個西歐端點和「加權」流量路由方法，建立流量管理員設定檔。 接下來，您將這個「子」設定檔新增為「父」設定檔的端點。 父設定檔仍使用「效能」流量路由方法，而且包含其他全域部署當做端點。

下圖說明此範例：

![巢狀流量管理員設定檔][2]

在此設定中，透過父設定檔導向的流量正常地將流量分散至各區域。 在西歐內，巢狀設定檔會根據指派的權數，將流量分散至生產端點和測試端點。

當父設定檔使用「效能」流量路由方法時，必須指派位置給每個端點。 您需要在設定端點時指派位置。 選擇最接近部署的 Azure 區域。 Azure 區域是「網際網路延遲資料表」所支援的位置值。 如需詳細資訊，請參閱[流量管理員「效能」流量路由方法](traffic-manager-routing-methods.md#performance-traffic-routing-method)。

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>範例 2︰巢狀設定檔中的端點監視

「流量管理員」會主動監控每個服務端點的健康情況。 如果端點的狀況不良，流量管理員會將使用者導向替代端點，以維持服務的可用性。 這項端點監視及容錯移轉行為適用於所有流量路由方法。 如需詳細資訊，請參閱 [流量管理員端點監視](traffic-manager-monitoring.md)。 巢狀設定檔的端點監視有不同的運作方式。 使用巢狀設定檔時，父設定檔不會直接對子系執行健康狀態檢查， 子設定檔端點的健康狀態會用來計算子設定檔的整體健康狀態。 此健康狀態資訊會在巢狀設定檔階層中往上傳播。 父設定檔會使用這個彙總的健康狀態，來決定是否要將流量導向子設定檔。 如需巢狀設定檔健康狀態監視的完整詳細資料，請參閱本文的[常見問題集](#faq)一節。

回到前一個範例，假設西歐的生產環境部署失敗。 根據預設，「子」設定檔會將所有流量都導向測試部署。 如果測試部署也失敗，父設定檔會決定子設定檔不應該接收流量，因為所有子端點都狀況不良。 接著，父設定檔會將流量分散至其他區域。

![巢狀設定檔容錯移轉 (預設行為)][3]

您可能喜歡這種做法。 您也可能擔心現在西歐的所有流量都流向測試部署，而不是一小部分的流量。 不論測試部署的健康狀態如何，當西歐的生產環境部署失敗時，您想要容錯移轉至其他區域。 若要啟用此容錯移轉，在將子設定檔設定為父設定檔中的端點時，您可以指定 'MinChildEndpoints' 參數。 此參數決定子設定檔中可用的最少端點。 預設值為 '1'。 在此案例中，您將 MinChildEndpoints 值設為 2。 低於此閾值時，父設定檔會將整個子設定檔視為無法使用，並將流量導向其他端點。

下圖說明此設定：

![以 'MinChildEndpoints' = 2 進行容錯移轉的巢狀設定檔][4]

> [!NOTE]
> 「優先順序」流量路由方法會將所有流量分配給單一端點。 因此，如果子設定檔的 MinChildEndpoints 不是設為 '1'，則作用不大。

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>範例 3︰設定「效能」流量路由中容錯移轉區域的優先順序

「效能」流量路由方法的預設行為可避免下一個最靠近的端點超載而引發一連串失敗。 端點失敗時，原本要導向該端點的所有流量，將會平均分散至所有區域的其他端點。

![搭配預設容錯移轉的「效能」流量路由][5]

不過，假設您偏好將西歐流量容錯移轉至美國西部，而只有在這兩個端點都無法使用時，才將流量導向其他區域。 您可以使用子設定檔並搭配「優先順序」流量路由方法來建立這個解決方案。

![搭配優先性容錯移轉的「效能」流量路由][6]

由於西歐端點的優先順序高於美國西部端點，因此當兩個端點都在線上時，所有流量會傳送至西歐端點。 如果西歐發生失敗，其流量就會導向美國西部。 如果使用巢狀設定檔，則只有在西歐和美國西部都失敗時，流量才會導向東亞。

您可以對所有區域重複此模式。 將父設定檔中的三個端點全部取代為三個子設定檔，每個都提供容錯移轉優先順序。

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>範例 4︰控制相同區域中多個端點之間的「效能」流量路由

假設在設定檔中使用「效能」流量路由方法，而此設定檔在特定區域有多個端點。 根據預設，導向該區域的流量會平均分散至該區域中所有可用的端點。

![「效能」流量路由區域內流量分配 (預設行為)][7]

我們不是在西歐新增多個端點，而是將這些端點封入個別的子設定檔中。 子設定檔新增至西歐唯一端點的父設定檔。 子設定檔上的設定在西歐內啟用優先順序式或加權式的流量路由，可控制西歐的流量分配。

![搭配自訂區域內流量分配的「效能」流量路由][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>範例 5︰每個端點的監視設定

假設您使用流量管理員，順暢地將流量從傳統內部部署網站移轉至裝載於 Azure 中的新雲端網站。 對於舊版的網站，您想要使用首頁 URI 來監視網站健康狀態。 但對於新的雲端網站，您實作一個包含額外檢查的自訂監視頁面 (路徑 '/monitor.aspx')。

![流量管理員端點監視 (預設行為)][9]

流量管理員設定檔中的監視設定會套用至單一設定檔中的所有端點。 在巢狀設定檔中，您針對每個網站使用不同的子設定檔來定義不同的監視設定。

![搭配每個設定的流量管理員端點監視][10]

## <a name="faq"></a>常見問題集

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

## <a name="next-steps"></a>後續步驟

深入了解 [流量管理員的運作方式](traffic-manager-how-traffic-manager-works.md)

了解如何 [建立流量管理員設定檔](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png



<!--HONumber=Jan17_HO1-->


