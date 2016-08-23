<properties
   pageTitle="應用程式閘道簡介 | Microsoft Azure"
   description="此頁面提供第 7 層負載平衡的應用程式閘道服務的概觀，包括閘道大小、HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性，以及 SSL 卸載。"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="gwallace"/>

# 應用程式閘道概觀

Microsoft Azure 應用程式閘道會根據第 7 層負載平衡，提供 Azure 管理的 HTTP 負載平衡解決方案。

應用程式負載平衡可讓 IT 系統管理員與開發人員建立以 HTTP 為基礎的網路流量路由規則。應用程式閘道服務高度可用且計量。如需 SLA 和價格的詳細資訊，請參閱 [SLA](https://azure.microsoft.com/support/legal/sla/) 和[價格](https://azure.microsoft.com/pricing/details/application-gateway/)頁面。

應用程式閘道目前支援下列的第 7 層應用程式傳遞：

- HTTP 負載平衡
- 以 Cookie 為基礎的工作階段同質性
- [安全通訊端層 (SSL) 卸載](application-gateway-ssl-arm.md)
- [URL 型內容路由](application-gateway-url-route-overview.md)
- [多站台路由](application-gateway-multi-site-overview.md)

## HTTP 第 7 層負載平衡

Azure 透過在傳輸層級 (TCP/UDP) 執行的 Azure Load Balancer，並讓所有連入的網路流量負載平衡到「應用程式閘道」服務，提供第 4 層負載平衡。「應用程式閘道」會將路由規則套用至 HTTP 流量，藉此提供第 7 層 (HTTP) 負載平衡。當您建立應用程式閘道時，會與一個端點 (VIP) 建立關聯，而該端點會做為輸入網路流量的公用 IP。

「應用程式閘道」會根據其組態 (無論是虛擬機器、雲端服務、Web 應用程式或外部 IP 位址) 路由傳送 HTTP 流量。

HTTP 第 7 層負載平衡可用於：

- 需要要求來自相同使用者/用戶端工作階段，才能到達相同後端虛擬機器的應用程式。這些應用程式的範例包括購物車應用程式和網頁郵件伺服器。
- 要從 SSL 終止負荷釋出 Web 伺服器陣列的應用程式。
- 在同一個長時間執行的 TCP 連線上必須要有多個 HTTP 要求才能路由傳送至或負載平衡至不同後端伺服器的應用程式 (例如內容傳遞網路)。

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]


## 閘道大小和執行個體

應用程式閘道目前提供三種大小：小型、中型和大型。小型執行個體大小是針對開發和測試案例。

每一訂用帳戶您可以建立最多 50 個應用程式閘道，而且每一應用程式閘道最多可以有 10 個執行個體。應用程式閘道負載平衡為 Azure 管理服務，可允許將第 7 層負載平衡器佈建在 Azure 軟體負載平衡器之後。

下表顯示每個應用程式閘道執行個體的平均效能輸送量：


| 後端頁面回應 | 小型 | 中型 | 大型|
|---|---|---|---|
| 6 K | 7\.5 Mbps | 13 Mbps | 50 Mbps |
|100 K | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE] 這些值是應用程式閘道輸送量的近似值。實際的輸送量會依據不同的環境詳細資料而有所不同，例如平均頁面大小、後端執行個體位置，以及提供一個頁面所需的處理時間。


## 健康狀況監視

Azure 應用程式閘道會自動監視後端執行個體的健康狀況。如需詳細資訊，請參閱[應用程式閘道健全狀況監視概觀](application-gateway-probe-overview.md)。





## 設定和管理

您可以藉由使用 REST API，以及使用 PowerShell Cmdlet 來建立和管理應用程式閘道。


## 後續步驟

了解應用程式閘道之後，您可以[建立應用程式閘道](application-gateway-create-gateway-portal.md)，或是[建立應用程式閘道 SSL 卸載](application-gateway-ssl-arm.md)來為 HTTPS 連線進行負載平衡。

若要了解如何使用 URL 型內容路由來建立應用程式閘道，請移至[使用 URL 型路由建立應用程式閘道](application-gateway-create-url-route-arm-ps.md)以取得詳細資訊。

<!---HONumber=AcomDC_0810_2016------>